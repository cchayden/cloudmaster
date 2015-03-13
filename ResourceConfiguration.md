# Resource Configuration Parameters #

## Resource Configuration Parameters ##

These parameters control the resource policy.  The primary consideration is to provide enough instances to handle the offered load, without unduly loading any of them down or leaving too many idle.  Each instance operates at some load, ideally between 0 and 1, but possibly going over 1.  The resource manager tries to maintain the load on all instances between `target_upper_load` and `target_lower_load`.

Users are allocated to instances by an external allocator, which sends a message on the work queue for each client it allocates.  Thus the number of items on the work queue each time the policy is evaluated is a measure of the new work that was allocated in the interval.  An instance is considered to have "excess capacity" if its load is below `target_upper_load`.  If the new work can fit within the excess capacity, no new instances are allocated.  If not, then more instances are started.

  * `maximum_number_of_instances` -- The maximum number of instancs to allow in the pool.  No more tan this number will be created, and if the instance manager discovers others running, it will stop some to bring the total below the maximum.  Defalts to 2.
  * `minimum_number_of_instances` -- The minimum number of instances to allow.  If fewer instances are running, more will be started.  Defaults to 0.
  * `start_limit` -- The maximum number of instances to start each time the policy is applied.  Thus at most `start_limit` instances will be started each `policy_interval`.  Defaults to 1.
  * `minimum_lifetime` -- Since Amazon charges a fixed amount per hour or part of an hour, it makes sense to let an instance keep running for the first part of an hour.  This is the minimum amount of time, in minutes, for an instance to run.  Defaults to 55 minutes.
  * `stop_limit`  -- The maximum number of instances to stop each time the policy is applied.  Thus at most `stop_limit` instances will be stopped each `policy_interval`.  Defaults to 1.
  * `watchdog_interval` -- If an instance does not send status information in a long time, it probably means it has died or hung, and should be stopped.  This is the amount of time that has to elapse before an instance is considered to be hung.  It defaults to 10 minutes.  Instances managed as resources normally send status information on a regular schedule.
  * `target_upper_load` -- The load level at which an instance is judeged to have no excess capacity.  Becauseit can take time for a new server to become active, clients may have to be allocated to servers at or beyond this load, but as soon as the new server becomes active, new allocations should be directed to it.  Defaults to 0.75.
  * `queue_load_factor` This is the amount of load that a newly allocated work-queue represents.  It is used to determin how many instances to start.  For example, if there is no excess capacity, and 10 new work items are in the queue	then the instances needed to service that is `10/queue_load_facor`.  Defaults to 2.
  * `target_lower_load` -- When considering whether to reduce the pool size, CloudMaster aims to keep the average load on servers greater than `larget_lower_load`.  It takes the total load across the existng servers, and determines how many would e required if each were loaded to `targer_lower_load`.  If this exceeds the current number of instances, then some instances are shut down (see below). Default to 0.25.
  * `shut_down_threshold` -- If the load on an instance is beow this value, then it is considered idle and can be stopped.  Defalts to 0.
  * `shut_down_interval` -- Time in minutes after which an instance is shut down that is allowed to remain running.  After this time it is stopped, no matter what the load is.  Defaults to 60 minutes.

## Shut Down ##
If the load is such that the pool needs to be reduced, it is generally not acceptable just to stop instances.  Clients are using these instances, and would be disrupted by an immediate termination.  Instead, the instance is first shut down, then stopped.  When an instance is shut down, the allocator no longer assigns new clients to it, so the load tends to decline over time.  Once the load reaches a low enough value `shut_down_threshold`, then the instance is stopped.  It may be desirable to stop a shut down instance after a period of time `shut_down_interval`, even if there are clients, to protect from a few long-running clients holding up the whole instance.

If some instances are shut down because of low usage, but then the load increases, then instead of starting new instances, shut down instances are made active once again.  After this happens, they operate like any other active server.

# See Also #
  * AwsConfiguration
  * CommonConfiguration
  * JobConfiguration
  * ManualConfiguration