# How CloudMaster Works #

CloudMaster runs on your computer, but it uses the Amazon Web Service APIs to monitor queues, start and stop instances, and store instance information.  It **could** run on an EC2 instance, but there is no particular advantage to dedicating a server just to do this.  Because the AWS  APIs allow the service to be controlled from anywhere, location does not matter.

# Service Models #

CloudMaster makes some assumptions about the behavior of instances, so that it can effectively decide when to scale the pool up and down.  It has two policies, corresponding to the most common stateless and stateful server types.

The _job_ policy is appropriate for servers that process requests presented to them through a queue, and which process them one at a time.

The _resource_ policy is appropriate for servers that maintain persistent connections from clients, but which are otherwise identical.

# Configuring CloudMaster #

CloudMaster needs to know at least a few things about the instances it is managing.  For example, it needs to know the instance name to start, the name of the keypair to use, the policy type (job or resource), and the names of the queues that instances use for work and status.  Most configuration parameters are optional, but you will want to set them to match your server characteristics.

# Learn More #
  * Service Models Explained: Jobs and Resources ServiceModels
  * Common Configuration Parameters CommonConfiguration
  * Job Configuration Parameters JobConfiguration
  * Resource Configuration Parameters ResourceConfiguration