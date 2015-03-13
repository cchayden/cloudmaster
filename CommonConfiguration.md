# Common Configuration Parameters #

## Common Configuration Parameters ##

Common configuration options can be used with any policy (job or resource).

  * `name` -- The name of the pool, used to report status.  This is taken from the section name if not supplied.
  * `policy` -- This mandatory parameter specifies the policy class to use.  Currently supported policies are job, resource, manual and default.
  * `policy_interval` -- How often in seconds the policy is evaluated.  Defaults to 60.
  * `poll_interval` -- This time, in seconds, is the smallest quantum of time.  The queue size is checked every poll\_interval.  Defaults to 5 seconds.
  * `audit_instance_interval` -- Every `audit_instance_interval minutes`, the instance manager queries EC2 to detect instances started or stopped **outside** of instance manager.  Defaults to 1 minute.
  * `summary_interval` -- This tells how oten, in seconds, to display a summary of the queue depthand instance pool size.  If zero, the summary is given every  `poll_interval`.  Defaults to 0.
  * `instance_log` -- Normally activity of instance\_manager as well as the log messages sent by instances are saved in one log, which can be stored in a file or in syslog.  It is sometimes useful to see the logs for each instance individually.  If `instance_log` is set, it is the name of a directory in which these log file will be written.  Each file is named after the instance-id (or i-number).  The default is **not** to write such log files.
  * `instance_report_interval` -- This tells how often, in seconds, to give a report of the instances.  The instance report shows each instance, its state, and its estimated load..  Default to 60 seconds.
  * `receive_count` -- Every poll\_interval, CloudMaster checks for status messages.  It reads a maximum of this many messages each time.  This defaults to 20 messages, so that receiving messages does not unduly slow down the other actiities taking place.
  * `work_queue_name` -- The name of the work queue.  If not given, it defaults to `<name>-work`} where `name` is the pool name.
  * `status_queue_name` -- The name of the status queue.  If not given, it defaults to `<name>-status` where `name` is the pool name.
  * `active_set_type` -- The active set is a record of the instances in the pool at any given time.  It is used, particularly in conjuntion with the resource policy, to assign users to specific instances.  It contains the public DNS name of every instance along with its load estimate.  It can be stored in an S3 object, or it can be sent periodicaly to a gien queue.  Possible values are `s3`, in which case `active_set_key` and `active_set_bucket` must be defined, or `queue` in which case `active_set_queue` must be defined, or `none` in which case the active set is not stored.  The default is `none`.
  * `active_set_interval` -- The active set is written (to S3 or to the queue) every `active_set_iterval` seconds.  If this is 0, it is written each time the instances are audited (`audit_instance_interval`).
  * `active_set_bucket` -- the name of the bucket used to store active set reports.  This defaults to the bucket name in AwsConfiguration.
  * `active_set_key` -- This is the key used to store the active set.  It default to `active-set-<name>-instances`} where `name` is the pool name
  * `active_set_queue_name` -- This is the queue name that the active set is sent to every `active_set_interval`.  It defaults to `<name>-active-set`} where `name` is the pool name.
  * `ami_name` This is the name of the instance to start.  It is mandatory.  This name is used to match registered images, with a substring match.  This must select exactly one image -- if not, CloudMaster cannot start.
  * `key_pair_name` -- This is the name of the keypair (not the pathname).  It does not include the file extension either.  It defaults to a value derived from the AwsConfiguration keypair.
  * `security_groups` => The security groups used to start the instance.  Ths is an array value.  Defauls to `["default"]`.
  * `instance_type` -- This is the insstance type to start.  Defaults to "m1.small",
  * `user_data` -- User data is passed to each inatance when it starts.  This typicaly includes the environment (env) as well as the access key and the secret key.  This defaults to sending these three items.  You can give additional data.  The values you supply are merged with these defaults and serialized with YAML.

# See Also #
  * AwsConfiguration
  * JobConfiguration
  * ResourceConfiguration
  * ManualConfiguration