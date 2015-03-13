# Job Configuration Parameters #

## Job Configuration Parameters ##

These parameters apply to the jobs policy.  They are used to manage the smooth flow of jobs from the queue, through the instance pool, to the status queue.

The primary consideration is the balance between keeping enough instances idle at al times to ensure quick processing of jobs, or keeping enough items in the queue to ensure hig utilization of the instances.  The primary parameter is making this tradeoff is  `start_threshold`.  It represents the desired number of queued items per active server.  If the queue is larger, then the manager will tend to increase the pool size; if smaller, it will decrease it.

For example, if the `start_threshold` is 2, and there are 3 instances, CloudMaster will tend to maintain the queue at 6 items.  The idea behind this is that if the average time to process a job is T, then 3 instances service 3 queue items each T, and with an average of 6 items, the jobs are delayed 2T.  Thus the average delay is `start_threshold * T`.

  * `maximum_number_of_instances` -- The maximum number of instancs to allow in the pool.  No more tan this number will be created, and if CloudMaster discovers others running, it will stop some to bring the total below the maximum.  Defalts to 2.
  * `minimum_number_of_instances` -- The minimum number of instances to allow.  If fewer instances are running, more will be started.  Defaults to 0.
  * `start_limit` -- The maximum number of instances to start each time the policy is applied.  Thus at most `start_limit` instances will be started each `policy_interval`.  Defaults to 1.
  * `minimum_lifetime` -- Since Amazon charges a fixed amount per hour or part of an hour, it makes sense to let an instance keep running for the first part of an hour.  This is the minimum amount of time, in minutes, for an instance to run.  Defaults to 55 minutes.
  * `stop_limit`  -- The maximum number of instances to stop each time the policy is applied.  Thus at most `stop_limit` instances will be stopped each `policy_interval`.  Defaults to 1.
  * `watchdog_interval` -- If an instance does not send status information in a long time, it probably means it has died or hung, and should be stopped.  This is the amount of time that has to elapse before an instance is considered to be hung.  It defaults to 10 minutes.  Make sure that the maximum service time is less than the `watchdog_interval`.  Otherwise the instance will be stopped after it has spent considerable time working, and the job will jumt reappear again later.
  * `start_threshold` -- Determines the average queue length, thus delay.  See above.  Defaults to 2.
  * `idle_threshold` -- This is used to determine how many instances to stop.  It is only used if the queue is empty.  The number of instances to stop is determined by the number of idle instanced divided by `idle_threshold` (rounded)  If this value is greater than 2, the last instance can never be stopped, even if there is no work.  Defalts to 1.

# See Also #
  * AwsConfiguration
  * CommonConfiguration
  * ResourceConfiguration
  * ManualConfiguration