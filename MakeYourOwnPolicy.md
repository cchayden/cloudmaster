# Make Your Own Policy #

The existing Job and Resource policies are useful, but what if you have your own requirements that cannot fit within these policies?  You can make your own policy.  This is quite straightforward, once you understand how tings are structured.

First, you might want to generate documentation.  Just go to the `$AWS_HOME` directory and run
```
  rdoc
```
It will generate a `doc` directory, which contains an `index.html` file for you to browse.

## Policy Creation ##

The policy is implemented as a class, which is consulted from time to time to determine the desired number of instances to run.  Each pool uses a single policy, which is specfied in the configuration.

In CommonConfiguration, the common configuration parameters are presented.  The parameter of interest is `policy`, which specifies the policy to use.  The built-in values are `job`, `resource`, and `default`.  You can supply a different policy name, if you implement the policy you name.

Let's say, for example, you set `policy=custom`.  Then CloudMaster will do
```
  require 'custom_policy'
```
and will then instantiate a class as follows:
```
  policy = CustomPolicy.new(*args)
```
We will get to the constructor arguments in a little bit.

## Policy Execution ##
So now your own policy class has been constructed.  What should it do?  Although you do not have to, it would be easiest for you to base your own policy on the `Policy` base class, in [policy.rb](http://code.google.com/p/cloudmaster/source/browse/trunk/app/policy.rb).

The key method, which all policies must implement, is `adjust`.  This returns a signed integer, the number of instances to start (positive) or stop (negative).  A value of zero means the instance pool should be left as it is.  Every derived class must implement at least this method.

All the other methods that policies provide, such as ensuring that the minimum number of instances are running, terminating instances that have not responded in a long time, and so forth, have default implementations in the base class.

`Policy` also helps to set up the images and the queues.  Again, the base class performs this work.  For instance, `Policy` is responsible for looking up the image given its name, and throwing an exception if a suitable image is not found.  You might want to override this if, for instance, your policy does not involve creating instances, and thus you don't need a image.  Queues are handled the same way: ordinarily a work queue and a status queue must be defined, but if your policy (and instances) does not make use of queues, you can bypass this by overriding the appropriate method.

### Example ###
Let's build a custom policy, just to see how it is done.  Suppose we want to make sure that a different minimum number of instances are running between 10:00 AM and 5:00 PM.  We will let the existing parameter `minimum_number_of_instances` continue to control the overall minimum, but we will write an `adjust` method that will increase the pool size during part of the day.

The example is included in the project.  The policy itself is in [daytime\_policy.rb](http://code.google.com/p/cloudmaster/source/browse/trunk/app/daytime_policy.rb).  There is a unit test for it in [daytime-policy-tests.rb](http://code.google.com/p/cloudmaster/source/browse/trunk/test/daytime-policy-tests.rb), which includes configuration in [test-config.rb](http://code.google.com/p/cloudmaster/source/browse/trunk/test/test-config.rb).

For flexibility, we will provide the alternate minimum value as a configuration parameter `minimum_number_of_instances_daytime`.  We will hard code the times, but we could clearly put these in configuration parameters as well.

### Configuration ###
Configurations are described in RunningYourOwn.  The relevant part is:
```
  [Pool-daytime]
  minimum_number_of_instances_daytime=3
```


### The Policy ###
The policy itself is implemented in its own class, `daytime_policy.rb`.  This must be in the ruby load path.  The policy itself looks like this.

```
  class DaytimePolicy < Policy
    def adjust
      hour = Clock.hour
      return 0 if hour < 10 || hour > 17
      return 0 if @instances.size >= @config[:minimum_number_of_instances_daytime]
      return 1
    end
  end
```
This says if the time is before 10AM or after 5PM, leave everything alone.  The other polices governing minimum instances will still take effect.  Next, if the number of instances is already equal to or greater than the minimum number for the daytime, do not create more.  But otherwise, create one instance.  Terminating instances is not covered in this example.

(Clock is used instead of Time to assist testing.)


### Testing ###
Here is an extract of the unit tests.
```
  def test_daytime_load
    assert_equal(CloudMaster, @policy.class)
  end

  def test_nighttime
    assert_equal(0, @policy.adjust)
  end

  def test_daytime_additional
    Clock.set(11 * 3600)
    assert_equal(1, @policy.adjust)
  end

  def test_daytime_max
    @pool.start_n_instances(3)
    Clock.set(11 * 3600)
    assert_equal(0, @policy.adjust)
  end
```
The first test verifies that the policy an load.

The next test verifies that at night, adjust returns 0.

The third test verifies that during the dat (at 11AM) that adjust return s.

And the final test verifies that once three instances are already running, even at 11AM, adjust returns 0.