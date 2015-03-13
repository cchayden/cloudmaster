# Service Models #

CloudMaster needs to predict how instances behave to be able to manage them properly.  It does this by looking at he work queue and the reported load, and by applying this to a model of their behavior.  CloudMaster currently has three models, and is designed to accommodate additional models.

## Job Model ##
The job model is the simplest to explain.  In this model, each instance in the pool performs the same batch operation based on a _work request_.  Requests are sent by clients through a _work queue_.  Each instance repeatedly reds a work request, processes the request, and at the end provides a status report through the _status queue_.  On success, the work request is deleted from the queue.

The pool consists of any number of instances, all competing to remove requests from the queue.  It does not matter which one gets which request, and SQS manages the task of delivering each message only once.  (It does not deliver them in order, so there is no way to ensure requests are serviced in anything resembling the order in which they were submitted.)

With this work model, the primary goal is to regulate the size of the work queue.  Bsic queueing theory tells us that we should keep the queue size small, to minimize the time clients have to wait for results.  But the cost of keeping it too small is a larger instance pool, containing many idle servers.  So the strategy, in general, is to start more servers if the queue size is too large, and to stop some idle servers when the queue is very small.

CloudMaster uses a very simple queueing model to predict how many instances are needed to achieve a configured desired queue size.  If you know more about the statistical characteristics of the workload, you may be able provide a model that better maintains the queue size within desired limits.

## Resource Model ##
In the job model, the instance was either 100% busy (processing a work request) or completely idle (waiting for more work).  In the resource model, instances are partly busy.  Each one reports is estimated load periodically, and the instance manager uses these reports along with optional entries in the work queue to size the instance pool.

What the instances are doing is not important: what matters is that the load estimate be stable enough over time to allow the instance manager to control it sensibly.  (This means the estimated load should not change wildly between successive reports).  The instance manager does no smoothing or averaging -- it reacts to what it sees at any given time.

As an example, consider a server that performs long interactive sessions with clients.  Each connection consumes a fraction of the server's resources, so there is a maximum number of connections permitted.  The load can be estimated as the ratio of the current connections to the maximum connections.

Another example would be a server whose capacity is determined not by the number of clients, but by the work these clients request.  In this case, the estimated load could be derived from the CPU load average.

Instance might be passively accepting connections from clients, or they might be actively establishing these connections.  Or they might be observing some external phenomenon (like customers pasing through a gate) and performing ork in response.  However they obtain work, members of a resource pool are assumed to be identical and are required to report their estimated load periodically.

CloudMaster is trying to maintain the estimated load on the instances between a configured maximum and minimum.  If all instances are experiencing high load, then additional instances are created.  But  it is a waste of money to maintain too many instances when they are lightly loaded, so CloudMaster work to stop some when the demand is less.

Managing instances in the resource model is more complex because
  * Usually some other entity (not CloudMaster) is assigning clients to specific instances, by handing out public DNS names.
  * CloudMaster cannot, in general, stop lightly used (but not completely idle) instances without disrupting some of their clients.
  * A lightly loaded instance may not have much work to do, but it may also just have come up, and is building up load.  CloudMaster cannot simply shut down lightly loaded instances.
  * It takes some time for a newly started server to be ready to serve users.  CloudMaster must account for this in deciding whether to start new servers.

To allow an external entity to intelligently assign clients to active instances, CloudMaster periodically writes (in S3) a record of which instances are active, along with their public DNS names and estimated load.  In general, it is expected that CloudMaster assign work to the least loaded server.

When CloudMaster determines that it is appropriate to stop an instance, it first marks one of the instances (the least lightly loaded) as being in the "shutdown" state.  In this state, it is no longer reported as active (in S3) and presumably the allocator will stop assigning it new clients.  It is expected that its load will gradually decrease.  When its load reaches zero (or when a configured length of time passes in the shutdown state) then the instance is actually stopped.

When CloudMaster needs to increase the pool size, it first checks to see if there are any shut down instances.  If there are, it revives them first.  In general itis more expensive (and slower) to start a new server than to reuse an existing one.

## Common Control Characteristics ##
Some aspects of the instances are common to both models.
  * Rate limits on starting and stopping -- you can limit the number of instances started or stopped at once
  * Maximum and minimum pool size -- you can set maximum and minimum limits on the pool size.  This allows you, for instance, to make sure there is always one server running.
  * Minimum lifetime -- because you are charged per hour or any part of an hour for instances, once you start an instance it make sense to keep it around for most of the first hour, even if current conditions do not demand that.
  * Periodic status reports -- instances are required to make periodic status reports, giving their estimated load.  If an instance fails to send in status for a given time period, it is asumed to be hung and is stopped.
  * Identical instances -- the servers are all started using the same image.  On startup, and regularly thereafter, CloudMaster audits the instances running the images it is managing.  If it discovers that new instances are running, it adds them to to pool it manges.  If it discvers that instances that is managing have disappeared, it drops them from its inventory.

If you need to ensure that a fixed number of servers are always running, despite the possibility that a server will hang or that the instance will spontaneously stop itself, you can configure the maximum and minimum number of servers to be the same, and simply run a status reporter on each instance.  If the instance hangs CloudMaster will stop it and start another, and if it disappears CloudMaster will start a replacement.  Now you just have to ensure the reliability of CloudMaster itself.

# Extensions #
You can add your own policy.  A policy is responsible for:
  * ensuring that the number of instances is between a configurable minimum and maximum
  * stopping instances that are not responding
  * increasing or decreasing the pool in response to load

You simply define a class such as MyOwnPolicy with instance methods {{ensure\_limits}}}, `stop_hung_instances` and `apply`.  Then configure CloudMaster to use the policy "`:myOwn`".  CloudMaster will call your class's methods periodically.  Your class can do whatever it wants to create or stop instances.  Your class constructor is given the configuration information and a pointer to an "instances" object that is used to keep track of, and operate on, the instances.


If desired, you can inherit from CloudMaster::Policy.  This provides default implementations of the three required methods.  It also provides access to some useful methods that start and stop instances.  The default implementation of `ensure_limits` and `stop_hung_instances` is probably adequate.  The default implementation of `adjust` calls on `apply` which simply returns a count of the number of istances to start (positive) or stop (negative).  So in the simplest case you need only inherit from `Policy` and override `adjust`.

