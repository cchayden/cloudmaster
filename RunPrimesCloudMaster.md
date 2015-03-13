# Run Primes Instances #

It is a little easier to see what is happening if we run primes and fibonacci instances separately.

Stop cloudmaster (use Control-C) and start it again, using the "-p" option to run only the primes instances:
```
  run-cloudmaster -p primes
```

## What You See ##
Here is what you should see when you run cloudmaster.
```
$ run-cloudmaster -p primes
Jul 14 19:44:07 08 primes Instances: 0 Queue Depth: 0
Jul 14 19:44:07 08 primes ---Instance Summary---
Jul 14 19:44:07 08 primes ----------------------
Jul 14 19:44:14 08 primes Instances: 0 Queue Depth: 0
Jul 14 19:44:20 08 primes Instances: 0 Queue Depth: 0
Jul 14 19:44:25 08 primes Instances: 0 Queue Depth: 0
Jul 14 19:44:32 08 primes Instances: 0 Queue Depth: 0
Jul 14 19:44:38 08 primes Instances: 0 Queue Depth: 0
Jul 14 19:44:45 08 primes Instances: 0 Queue Depth: 0
Jul 14 19:44:50 08 primes Instances: 0 Queue Depth: 0
Jul 14 19:44:56 08 primes Instances: 0 Queue Depth: 0
Jul 14 19:45:02 08 primes Instances: 0 Queue Depth: 0
Jul 14 19:45:09 08 primes Instances: 0 Queue Depth: 0
Jul 14 19:45:09 08 primes ---Instance Summary---
Jul 14 19:45:09 08 primes ----------------------
```

This shows the output of cloudmaster: every five seconds (or so) a report of the queue depth and the number of instances running.  There are no instances running and nothing in the queue.  Once a minute a summary of all running instances is displayed.  Since no instances are running, this summary is empty.

## Generate Work ##
Now let's generate some work.  We will send a work order to the work queue.  Leaving cloudmaster running in one window, open another window.
```
  $ cd $AWS_HOME/examples/primes
  $ feed-primes-queue 
  sent 1|6573
```
The feed-primes-queue sent a request to generate 6573 primes.  Each time it is run, it generates a random number and requests that many primes.

CloudMaster responded by creating an instance to handle the message.
```
Jul 14 20:00:50 08 primes Instances: 0 Queue Depth: 1
Jul 14 20:00:57 08 primes Instances: 0 Queue Depth: 1
Jul 14 20:00:57 08 primes ---Instance Summary---
Jul 14 20:00:57 08 primes ----------------------
Jul 14 20:00:58 08 primes Started instance i-ceeb38a7
Jul 14 20:01:05 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:01:15 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:01:21 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:01:28 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:01:34 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:01:40 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:01:51 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:01:57 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:01:57 08 primes ---Instance Summary---
Jul 14 20:01:57 08 primes   i-ceeb38a7 State: startup Load: 0.00 Since Last Report: 59
Jul 14 20:01:57 08 primes ----------------------
Jul 14 20:02:03 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:02:12 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:02:19 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:02:25 08 primes Instances: 1 Queue Depth: 1
Jul 14 20:02:32 08 primes i-ceeb38a7 Processing: 1 6573
Jul 14 20:02:35 08 primes i-ceeb38a7 Processed: 1 6573 primes (65809) in 2
Jul 14 20:02:39 08 primes Instances: 1 Queue Depth: 0
Jul 14 20:02:44 08 primes Instances: 1 Queue Depth: 0
Jul 14 20:02:51 08 primes Instances: 1 Queue Depth: 0
Jul 14 20:02:56 08 primes Instances: 1 Queue Depth: 0
Jul 14 20:03:02 08 primes Instances: 1 Queue Depth: 0
Jul 14 20:03:02 08 primes ---Instance Summary---
Jul 14 20:03:02 08 primes   i-ceeb38a7 State: active Load: 0.00 Since Last Report: 29
Jul 14 20:03:02 08 primes ----------------------
```
What is happening here is that the queue depth is 1 (from the work request).  CloudMaster evaluates how many instances are needed once a minute, so when it does, it decides to start a new instance.  On the next status report the summary shows the one instance, which is still in the startup state.

Once the instance gets going, it reads the work request from the queue.  There are two lines of output labelled with the instance number: these are log messages sent by the instance.  The first indicates that the processor has picked up the work request and has started processing it.  The second indicates that this processing has finished, and that the 6573rd prime is 65809, and that it took 2 seconds to compute this.

After that, the queue depth is 0, the instance continues to exist, and the next summary shows that it is in the active state with a load of 0 (since it is not processing work at that time).

## Termnate Instances ##
After a period of inactivity, CloudMaster decides that it should stop the idle instance.
```
Jul 14 20:06:06 08 primes Instances: 1 Queue Depth: 0
Jul 14 20:06:06 08 primes ---Instance Summary---
Jul 14 20:06:06 08 primes   i-ceeb38a7 State: active Load: 0.00 Since Last Report: 213
Jul 14 20:06:06 08 primes ----------------------
Jul 14 20:06:06 08 primes i-ceeb38a7 Terminating instance 
Jul 14 20:06:12 08 primes Instances: 0 Queue Depth: 0
Jul 14 20:06:18 08 primes Instances: 0 Queue Depth: 0
Jul 14 20:06:28 08 primes Instances: 0 Queue Depth: 0
```
Now the instance pool is back to the initial state.

# Next Step #
Next we demonstrate how to run the fibonacci instance RunFibCloudMaster