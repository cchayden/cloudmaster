# Run CloudMaster #

CloudMaster is a flexible software component that is designed to be configurable.
Used with the example configuration files, it manages a pool of prime number generators (modeling batch processing servers) and a pool of fibonacci generators (modelling connection-oriented servers).  This page  explains how to run the example.  Other pages describe the two service models, and explain the configuration parameters in detail.

This section assumes you have either built primes and fibonacci images, or are using the pre-built images.

## Running The Server ##
Running CloudMaster is simple, once things are set up:
```
  cd $AWS_HOME/examples
```
Edit `aws-config.ini` and insert your access key and secret key, and add the path to your keypair file.  If you are not using the pre-built instances, then also add your username and bucket.  Then **save the result** as `$HOME/aws-config.ini`.  Also copy `config.ini` to `$HOME/config.ini`.
```
  run-cloudmaster
```
This runs cloudmaster (on your computer).  It defaults to getting configuration from `aws-config.ini` and `config.ini` which you just produced in the previous step.

The result will be the following:
```
$ run-cloudmaster
Jul 14 21:47:09 08 primes Instances: 0 Queue Depth: 0
Jul 14 21:47:09 08 primes ---Instance Summary---
Jul 14 21:47:09 08 primes ----------------------
Jul 14 21:47:09 08 fib Instances: 0 Queue Depth: 0
Jul 14 21:47:09 08 fib ---Instance Summary---
Jul 14 21:47:09 08 fib ----------------------
Jul 14 21:47:16 08 primes Instances: 0 Queue Depth: 0
Jul 14 21:47:17 08 fib Instances: 0 Queue Depth: 0
Jul 14 21:47:21 08 primes Instances: 0 Queue Depth: 0
Jul 14 21:47:26 08 fib Instances: 0 Queue Depth: 0
Jul 14 21:47:27 08 primes Instances: 0 Queue Depth: 0
```

The output is a mixture of reports and summaries for the primes instances and the fibonacci instances.  For simplicity, we will examine these separately.

## Next Step ##
Next we run the primes instance through cloundmaster. RunPrimesCloudMaster

# Learn More #
  * Service Models Explained: Jobs and Resources ServiceModels
  * Common Configuration Parameters CommonConfiguration
  * Job Configuration Parameters JobConfiguration
  * Resource Configuration Parameters ResourceConfiguration