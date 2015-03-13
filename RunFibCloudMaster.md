# Run Fibonacci Instances #

Now we can run the fibonacci instances.  Stop cloudmaster (use Control-C) and start it again with "-f"
```
  run-cloudmaster -p fib
```

This time it reports on the fibonacci instances, of which there are none.
```
$ run-cloudmaster -p fib
Jul 14 20:32:02 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:32:02 08 fib ---Instance Summary---
Jul 14 20:32:02 08 fib ----------------------
Jul 14 20:32:08 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:32:15 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:32:20 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:32:25 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:32:32 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:32:38 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:32:43 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:32:49 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:32:54 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:33:01 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:33:08 08 fib Instances: 0 Queue Depth: 0
Jul 14 20:33:08 08 fib ---Instance Summary---
Jul 14 20:33:08 08 fib ----------------------
```

The fibonacci instance works as follows.  Clients connect to it on port 20808.  For each newline they send to the server, the client receives the next fibonacci number.

But how does the client know what server to connect to?  CloudMaster maintains a list of active instances (along with their load) in S3.  An _allocator_ reads this information, picks an appropriate instance, and reports back its public DNS information.  at the same time it lets CloudMaster know there is new work by queueing a work request.

In this case, there are no running instances, so they allocator cannot actually start an instance.  It queues a work order anyway, so that an instance will start and a subsequent request will succeed.

To start this process, do the following in a new window:
```
  $ cd $AWS_HOME/samples/fibonacci/client
  $ run-client
```

The result, as we expect is the following:
```
  $ run-client
  no servers are running, try again in a minute
```

CloudMaster responds as follows:
```
Jul 14 20:46:53 08 fib Instances: 0 Queue Depth: 1
Jul 14 20:46:53 08 fib ---Instance Summary---
Jul 14 20:46:53 08 fib ----------------------
Jul 14 20:46:56 08 fib Started instance i-2fe83b46
Jul 14 20:47:03 08 fib Instances: 1 Queue Depth: 0
Jul 14 20:47:09 08 fib Instances: 1 Queue Depth: 0
```
Because the work queue entries are meant to let CloudMaster know about new client requests, it empties the queue itself.  This did not happen for the primes instance.

Eventually the instance is up and ready:
```
Jul 14 20:49:00 08 fib Instances: 1 Queue Depth: 0
Jul 14 20:49:00 08 fib ---Instance Summary---
Jul 14 20:49:00 08 fib   i-2fe83b46 State: active Load: 0.00 Since Last Report: 52
Jul 14 20:49:00 08 fib ----------------------
Jul 14 20:49:08 08 fib Instances: 1 Queue Depth: 0
```

Now we can try again to run a client.  This client requests a fibonacci number once a second.
```
$ run-client
1
2
3
5
8
13
21
34
55
89
144
233
377
610
987
1597
2584
4181
6765
10946
17711
28657
46368
75025
...
```

Looking at CloudMaster log, we see the load estimate, which reflects that the server can handle up to 10 clients, and that it is currently serving one.
```
Jul 14 20:51:04 08 fib Instances: 1 Queue Depth: 0
Jul 14 20:51:04 08 fib ---Instance Summary---
Jul 14 20:51:04 08 fib   i-2fe83b46 State: active Load: 0.10 Since Last Report: 55
Jul 14 20:51:04 08 fib ----------------------
Jul 14 20:51:11 08 fib Instances: 1 Queue Depth: 0
```

When we run another client, it receives its own fibonacci sequence.  CloudMaster reports the increased load:
```
Jul 14 20:54:15 08 fib Instances: 1 Queue Depth: 1
Jul 14 20:54:15 08 fib ---Instance Summary---
Jul 14 20:54:15 08 fib   i-2fe83b46 State: active Load: 0.20 Since Last Report: 8
Jul 14 20:54:15 08 fib ----------------------
Jul 14 20:54:22 08 fib Instances: 1 Queue Depth: 0
```
Note what happened: run-client queued a work request, but it also found and connected to a suitable instance.  At the time of the next summary, the load of 0.2 was reported, and then CloudMaster emptied the work queue.

If we interrupt the clients, they disconnect and the load goes back down to 0.
```
Jul 14 20:58:29 08 fib Instances: 1 Queue Depth: 0
Jul 14 20:58:29 08 fib ---Instance Summary---
Jul 14 20:58:29 08 fib   i-2fe83b46 State: active Load: 0.00 Since Last Report: 20
```

Eventually CloudMaster shuts down and stops the instance.
```
Jul 14 21:37:26 08 fib Instances: 1 Queue Depth: 0
Jul 14 21:37:26 08 fib ---Instance Summary---
Jul 14 21:37:26 08 fib   i-2fe83b46 State: active Load: 0.00 Since Last Report: 6
Jul 14 21:37:26 08 fib ----------------------
Jul 14 21:37:26 08 fib i-2fe83b46 Shutting down instance 
Jul 14 21:37:27 08 fib i-2fe83b46 Terminating instance 
Jul 14 21:37:32 08 fib Instances: 0 Queue Depth: 0
```

# Next Step #
Clean up any remaining instance.  CleanUpInstances