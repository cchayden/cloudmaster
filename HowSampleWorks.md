# Introduction #

The sample applications consist of a prime number generator and a fibonacci generator.  They illustrate two styles of servers, managed by different policies.

## Primes ##
The primes server models a **batch** service.  Each instance reads work requests from a queue (`primes-work`).  The server then computes the requested number of primes, and sends the result to another queue (`primes-status`) as a **log message**.  It also sends **status messages** to the status queue, giving the busy/idle status of the server.

CloudMaster monitors the size of the work queue, and when it grows sufficiently large, it starts another primes server.  Conversely, if the queue is empty ad the status indicates that there are idle servers, then it stops prime servers.  It also logs any log messages received from one of the primes servers.

## Fibonacci ##
At the same time, CloudMaster is also managing a set of fibonacci servers.  These model a connection-oriented service, in which clients connect to a server directly, and maintain that connection for a significant length of time.

A fibonacci server listens on a given port, and accepts connections from clients.  Each time the client sends a newline character, the server sends the next fibonacci number.  You can connect to one with telnet and see a new fibonacci number every time you type   ENTER.  The project includes a client that connects and fetches a succession of fibonacci numbers.

The fibonacci server periodically sends load estimates over its status queue (`fib-status`).  It estimates the load from the number of clients that are connected and the maximum number it can handle.  For this example, the fibonacci server can handle ten clients, so each on contributes a load of 0.1.

CloudMaster monitors the fibonacci servers through these status messages, and starts a new one when the load warrents.  When the load is low, it is desirable to stop one or more servers, but clients are connected and would be disrupted if their server stopped suddenly.  So the server is moved to the _shut down_ state, where no new clients are allocated to it.  After all clients drop off, or after a suitable period of time passes, the instance is then stopped.  If the load rises again, shut down servers are activated again before new instances are started.

CloudMaster itself does not make the assignment of users to servers -- that is done externally.  This is because this assignment likely takes place on a different server than the one running instance manager, or might even be done in a distributed fashion.  But CloudMaster helps out by maintaining a list of the active servers, along with their associated loads.  This information is maintained in S3, where it can be read by any server anywhere.

The fibonacci server also uses a work queue (`fib-work`).  If there are no fibonacci servers running, then the allocator queues a request, which CloudMaster uses as a signal to start some instances.

The sample includes an allocator which works together with the sample fibonacci client to find a server and connect to it.  This allocator simply chooses the server with the least load.

# Summary #

This summarizes the steps in starting, working, and stopping.

## Start Up ##

### Primes ###
If there are no servers running, the sequence of events is this.
  1. Client submits request to primes-work
  1. CloudMaster sees entry in queue, and no instances, so it starts one
  1. Instance starts
  1. Instance reads work-queue, computes requested primes, and sends result as log message   to primes-status
  1. CloudMaster logs the result
  1. Instance deletes message

### Fibonacci ###
If there are no servers running, then the following takes place.
  1. Client asks the allocator for a server to connect to
  1. Allocator consults active set on S3
  1. Allocator finds no servers running
  1. Allocator puts message in fib-work queue and returns error to client
  1. CloudMaster sees request in fib-work and starts a fibonacci server
  1. CloudMaster updates active set with identity of fibonacci server
  1. Some time later, client again asks the allocator for a fibonacci server
  1. Allocator returns the identity of the fibonacci server
  1. Client makes the connection
  1. Server begins to report non-zero load

## Normal Operation ##

### Primes ###
  1. Client sends request to primes-work
  1. Server receives and processes request, returns result to pries-status

### Fibonacci ###
  1. Client asks allocator for an instance
  1. Allocator consults active set, returns an instance to client
  1. Client connects to instance

## Pool Grows ##

### Primes ###
  1. CloudMaster sees that primes-work has many entries
  1. CloudMaster starts one or more additional primes instances

### Fibonacci ###
  1. CloudMaster sees that all instances are above their target load
  1. CloudMaster starts one or more fibonacci instances
  1. CloudMaster updates active set

## Pool Shrinks ##

### Primes ###
  1. CloudMaster sees that queue is empty, and that servers are idle
  1. CloudMaster stops one or more idle servers

### Fibonacci ###
  1. CloudMaster sees that there is space capacity, which would allow the users to be served by fewer instances
  1. CloudMaster moves one or more servers into shutdown state, meaning they are removed from the active ser maintained on S3
  1. If shutodwn servers have zero load, or if they have been in shutdown state for a sufficient time, then they are stopped