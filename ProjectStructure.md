# Project Structure #

The project contains several components:
  * CloudMaster itself
  * A library of command-line tools used to manipulate the Amazon services.
  * Libraries shared between CloudMaster and the tools.
  * Code and scripts to build two example Amazon images.
  * Code to run the example images under the control of CloudMaster.
  * Unit tests.

```
  cloudmaster
    app
    lib
      AWS
      SafeAWS
      RetryAWS
      MockAWS
    tools
    instances
      base
      primes
      fibonacci
        monitor
    examples
      primes
      fibonacci
      sqs-allocator
    test
```

## Explanation ##
The `cloudmaster` directories contains four subdirectories:
  * `app` contains CloudMaster itself.
  * `lib` contains code used by tools, cloudmaster, tests, and instances.
  * `instances` contains code to build sample instances.
  * `examples` contains code to run the sample instances with cloudmaster.
  * `test` contains unit tests.

Within `lib` the structure is:
  * `AWS` contains AWS access libraries.  These are slightly modified versions of libraries provided by James Murty to accompany "Programming Amazon Web Services."
  * `SafeAWS` wraps the AWS libraries with code that catches and reports errors in AWS access library calls.
  * `retryAWS` wraps the AWS libraries with code that catches errors in AWS access library calls and retries them if possible, with exponential backoff.
  * `MockAWS` provides a mock implementation of (some of the) AWS library calls, for use by unit tests.

The `tools` directory contains a set of command-line tools used to interact with AWS.  These are described in [Command Line Tools](UsingTools.md).

If you use CloudMaster in your own project, you will need the `lib` directory and the `app` directory, and you will need to modify or build something similar to `run-cloudmaster`. You will also need to build images, and you will probably need to use the tools.  So my advice is to copy the entire project, and then add your own instances to the `instances` directory and your own applications in `examples`.

# More Information #
  * [Try It Out](ProjectSetup.md)
  * [Testing](TestDescription.md)