# Manage Your Own Instances #

Primes and Fibonacci number servers are just examples, but you probably want to manage your own servers.  This explains how to do it.

The primes sender and the fib client are simple examples of service clients.  These are written in ruby, but could have been implemented in any language that allows you to access the Amazon services SQS and S3.  In a real situation, they would probably be embeddd in whatever application is using the services.  The allocator is structured so that you can reuse it by invoking it as a subprocess, or else you can reimplement it in your own application.

To manage your own servers, you need to build them as EC2 images.  In addition to whatever they do, you need to have them send periodic status reports through the status queue.  The primes and fibonacci instances illustrate how to do this, and the fibonacci server even has a reusable status monitor that you can adapt to monitor your own service.

The followng gives more details on how to go about making your own images.

## Build Images ##

First, you need to build suitable images.  I will leave this to you -- it is your application.  Just make sure that it comes up ready to go when it boots, or there is some other way to make it come to life.  Here are a few suggestsions.

  * It is probably a bad idea to store your secret key **on the image** because if someone can break into the image or into the instance and read the file containing the key, they can charge their compute time to you.  CloudMaster passes the access key and the secret key to the app as user data.  The processes the need it get it from the infrastructure, not from the file system.  This makes it a little more secure, but if someone can get on your instance they could get your keys using `wget`.
  * You need to add something to your application that will send busy/idle reports (jobs policy) or load estimates (resource policy).  Even if you are using this for health monitoring or log collection only, you still need to send periodic keep-alives.  The example in [generate-primes.rb](http://code.google.com/p/cloudmaster/source/browse/trunk/instances/primes/generator-primes.rb) sends status messages when the state changes, and [generate-fib.rb](http://code.google.com/p/cloudmaster/source/browse/trunk/instances/fibonacci/generatde-fib.rb) sends status message periodically.  You might want to create a separate process to send the status message, and start it from init.  There is an example of just such a process, which was designed to monitor a Darwin Streaming Server in [http:/code.google.com/p/cloudmaster/source/browse/trunk/instances/fibonacci/monitor monitor]
  * If you name the queues, security groups, and images appropriately, the base classes will build many of the configuration parameters correctly for you.  The primes sample has everything built to convention.  The fibonacci sample does not -- it uses "fib" almost everywhere (queue names, security group name, active set key name) but uses "fibonacci" for the image name.  [config.rb](http://code.google.com/p/cloudmaster/source/browse/trunk/examples/config.rb) illustrates how to override this one parameter.

## Running CloudMaster ##
Once you have set up instances, you can control them with CloudMaster.  You need to create a configuration file to tell it what to run.  Copy `cloudmaster/examples/aws-config.ini` and `cloudmaster/examples/config.ini` to  your home directory, and fill in the keys and other information in `aws_config.ini`.  Then you can run `run-cloudmaster` to start everything up.

## Create Configuration Files ##

Configuration settings are read from `aws-config.ini` and `config.ini`, which may be stored in `$HOME` or `$AWS_HOME`. The contents of these fles are merged.  The configuration file consists of a number of sections marked by headings, containing parameter assignments.  Normally, the `AWS` section is stored in `$HOME/aws-config.ini`.  Several settings in the AWS section are mandatory, and and are used to provide parameters such as your AWS credentials, common to all instance pools.  `Config.ini` contains a section for each pool, describing the characteristics of that pool.

Section names are enclosed in square brackets, like this: `[AWS]`.  Sections describing pools have the form `[Pool-<name>]` where `<name>` is the name of the pool.  The pool name is used in the names of the queues and images, by defalt,  Common AWS configuration parameters are described in AwsConfiguration.  The pools parameters ared described in CommonConfiguration, JobConfiguration, and ResourceConfiguration.

Let's start by examining the sample configuration file.  It contains two pool sections, Pool-primes and Pool-fib.

Pool-primes is simple, because it uses default parameters  for everything.  The only thing that needs to be specified is the policy:
```
[Pool-primes]
policy=job
```

The fibnacci configuration is only a little more complicated.
```
[Pool-fib]
policy=resource
ami_name=cloudmaster-ami-fibonacci
active_set_type=s3
queue_load_factor=10
```

The `ami-name` overrides the conventional name (which would have been `cloudmaster-ami-fib`).  The `active_set_type` is set to enable writing the active set to S3.  And `queue_load_factor` is set because we know the fibonacci servers serve ten users, so we want a new one created when there are 10 entries in the queue during one policy interval.

You can create your own pool sections to match your own images.  If you follow the conventions, it can be as simple as the primes pool, but you can use other conventions by expliitly supplying the parameters.

## Run CloudMaster ##

Once you have set up the config file, store in in `$HOME/config.ini`, or else make sure it is in the current directory when you run cloudmaster.

```
  run-cloudmaster
```