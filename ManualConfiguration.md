# Manual Configuration Parameters #

## Manual Configuration Parameters ##

These parameters apply to the manual policy.  This policy delegates the job of determining the number of instances to an external entity.

The manual policy uses a queue to transmit instructions on how to adjust the instance pool size.  Each message allows the upward or downward adjustment of the queue size.

  * `maximum_number_of_instances` -- The maximum number of instancs to allow in the pool.  No more tan this number will be created, and if the instance manager discovers others running, it will stop some to bring the total below the maximum.  Defalts to 2.
  * `minimum_number_of_instances` -- The minimum number of instances to allow.  If fewer instances are running, more will be started.  Defaults to 0.
  * `start_limit` -- The maximum number of instances to start each time the policy is applied.  Thus at most `start_limit` instances will be started each `policy_interval`.  Defaults to 1.
  * `stop_limit`  -- The maximum number of instances to stop each time the policy is applied.  Thus at most `stop_limit` instances will be stopped each `policy_interval`.  Defaults to 1.
  * `minimum_lifetime` -- Since Amazon charges a fixed amount per hour or part of an hour, it makes sense to let an instance keep running for the first part of an hour.  This is the minimum amount of time, in minutes, for an instance to run.  Defaults to 55 minutes.
  * `manual_queue_name` -- The name of the queue that is used to send instructions to the manual policy.  If not given, it defaults to `<name>-manual`} where `name` is the pool name.

Messages sent on the manual queue are YAML encoded, and contain one fidle: `adjust`.
The value of this field is the number of instances to increase the pool by (if positive) or the number to decrease the ppok by (if negative).

CloudMaster adjusts the instance pool up or down as directed.  In addition, it acts to ensure that the number of instances stay between the minimum and maximum, that instances are not stopped until they have achieved their minimum lifetime, and that the start and stop limits are enforced.

# See Also #
  * AwsConfiguration
  * CommonConfiguration
  * JobConfiguration
  * ResourceConfiguration