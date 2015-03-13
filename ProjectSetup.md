# CloudMaster Setup #

CloudMaster supervises EC2 instances, but without something to control, there is not much you can see.  So this project also contains two sample servers that you can use to experiment with the system. These exemplify two of the models of server behavior that CloudMaster is equipped to handle (See ServiceModels).

# Sample Servers #
The two sample servers don't do anything very useful: they compute prime numbers and Fibonacci numbers.  Their purpose is to model typical server behavior, for instance transcoding video files and serving out the video streams.

This shows the structure of the sample servers.  ![http://chayden.net/CloudMaster/CloudMaster.png](http://chayden.net/CloudMaster/CloudMaster.png)

## Primes ##
The **primes** server models a batch process such as transcoding.  Each server monitors a _work queue_ containing requests to produce some number of prime numbers.  The server removes this request, computes the requested number of primes, and sends a status report containing the last of these numbers.

## Fibonacci ##
The **fibonacci** server models a connection-oriented process such as serving out media streams.  Clients connect to the server, and request successive fibonacci numbers by sending requests over the connection.  Each time the server reads a newline from the client it sends the next fibonacci number.

## Other Components ##
The **prime sender** generats work for the primes servers by sending requests to generate prime numbers.  The **fib clent** generates work for te fibonacci servers by connecting to them and requesting fibonacci numbers.  Teh **allocator** is used by the fib client to find a fibonacci server to connect to.

# CloudMaster #
CloudMaster monitors the queue backlog and the estimated loads, and decides whether it needs to increase of decrease the number of primes or fibonacci servers.  It also writes the active set, which records which servers are active.  It has a large number of configurable parameters that can tailor its behavior to particular requirements.

CloudMaster is implemented in ruby, so that in theory it can run on any platform.  This document assumes you are installing it on Linux, and that you already have ruby 1.8.1 or later installed.

Because ruby is interpreted, there is no build or install required.  You simply check out the code, set up your configuration in `config.ini`, and you are ready to go.  Of course you need to have an Amazon Web Services account which you can get at http://aws.amazon.com.

## Loose Coupling ##
Because the components (prime sender, fib client, cloudmaster, and the primes and fibonacci servers) communicate using queues and S3, they need not be located on the same computer.  Although there should be only one cloudmaster running, there can be any number of prime senders, located on any number of systems, and there can be any number of fib clients, located anywhere.

## Steps ##
To set up the sample you have to get an Amazon Web Services account, enter your credentials in the sample configuration file, and run cloudmaster, primes sender, and fib client on your own computer.  These steps are  described below.

  * Sign up for Amazon Web Services SignUp
  * Set up environment EnvironmentSetUp
  * Set up AWS AwsSetup
  * Create images CreateImages
  * Run CloudMaster RunCloudMaster
  * Request primes RunPrimesCloudMaster
  * Request Fibonacci numbers RunFibCloudMaster