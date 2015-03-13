# Testing #

There is a comprehensive set of unit tests in the dest directory http://code.google.com/p/cloudmaster/source/browse/trunk/test/.  This page explains how they work.

## Mock Environment ##

The test environment needs to simulate the Amazon web services EC2, SQS, and S3.  It also needs to have control of time, since many aspects of behavior are time based.

There are three vesions of the AWS interfaces: the original one (slightly modified) supplied with James Murty's book; a safe version that catches and logs all exceptions that are thrown, and a mock version for testing.   An `AwsContext` factory object is used to create a consistent met of interface objects.  The sample program uses the Safe library, so it is not unexpectedly stopped by an exception, and the test environment uses the Mock library.  The Mock library is tailored somewhat to the needs of CloudMaster -- it only implements the methods that are used by the application.  (The same is true of the Safe library.)

In addition to mock libraries, the test environment uses a mock clock.  The class `Clock` extends `Time` with a `sleep` method that merely calls `Kernel.sleep`.  The mock `Clock` is a replacement for Clock, and is used if it is required into the program before the standard `Clock` is defined.  The mock `Clock` defies just enough of the `Time` functionality to get by.  It keeps its own notion of time, which starts at 0 and advances only when `sleep` is called.  It includes a `set` function as well, which is used in testing.

## Unit Tests ##

The standard Test::Unit testing environment is used to organize and run the tests.  You can run all the tests using the command (in the test directory)"
```
  run-tests
```

The test cases themselves each exercise one of the classes.  Within a test case, each of the methods is tested.  The `cloudmaster-tests` are the most comprehensive, and involve all components working together.
