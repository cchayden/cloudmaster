# FAQ #
**Symptom**: Commands fail with a message about not being able to load libssl.

**Action**: In some distros, such as Ubuntu 8.10, you need to load ssl libraries: `sudo apt-get install libopenssl-ruby`.

---

**Symptom**: When building an instance or when running CloudMaster, there a complaint about aws\_accesskey or aws\_secret\_key.

**Action**: Make sure AWS\_ACCESS\_KEY and AWS\_SECRET\_KEY are defined and exported in your environment or in the configuration file.

**Action**: Make sure you have the right secret key.  The one used in this document is not actually your secret key.

**Action**: Make sure you have modified the example `aws-config.ini` and that the one that came with the project is not found in the config file search path before the one that you have modified.

---

**Symptom**: You cannot log in to an instance or even build an instance because there is some complaint about file permissions being too lenient.

**Action**: Make sure that the key files are not readable except by you.

---

**Symptom**: You get a message saying that you cannot run more than 20 instances.

**Action**: Amazon currently limits each account to 20 running instances.  Maybe you have started a lot of instances and have forgotten about them.

---

**Symptom**: You have just started an instance, and you cannot log in to it.

**Action**: it takes about 15 seconds after an instance has reported itself running before the ssh daemon is fully up and working.  If this is the problem, just wait.
If this problem persists, it probably means you do not have the keypair file installed or configured.

---

**Symptom**: Your instance claims to be running, but you cannot connect to it.

**Action**: Make sure that it was started with the right security settings.  The Amazon firewall may be blocking access.  You can check these permission settings with `list-groups`.

---

**Symptom**: You instance is running and was working, but now seems to not be working.

**Action**:  Your server may have crashed.  Use login-instance to log in to the instance and check your server.  Use `top` to see what is running.  Use the `service` command to see if the servers (primes or fib) are running.

---

**Symptom**: Build-image fails, usually with an AWS error, perhaps a 500 internal server error.

**Action**: Amazon web services occasionally fail due to network errors.  This happens with some frequency when uploading an image file from an instance to S3 (this is entirely within the Amazon network).  The Amazon-supplied tools do not recover from these errors, so the best thing to do is re-run the `build-image` command.  If you want, you can log in to the instance, edit `/mnt/<directory>/install`, delete all the steps before the upload (but make sure you keep the first part, which sets the environment variables) and run `install` manually.  Make sure you supply the right `AWS_ENV` value on the command line.

---

**Symptom**: CloudMaster shows a stack trace on its stdout.

**Action**: As before, Amazon web services API calls occasionally fail.  The libraries display a stack trace and attempt to keep going.  You can restart CloudMaster.  It will pick up the instances that it was previously managing, and take ownership.

---

**Symptom**: CloudMaster does not pick up pre existing running instances.

**Action**: You may have rebuilt and reregistered the image, and therefore existing instances are no longer recognized as being associated with the manifest name.  They are really different, so you are out of luck.

---

**Symptom**: There are pre-existing running instances (see previous item).  Primes or Fibonaccci serving does not seem to work properly.

**Action**: If you have been rebuilding images, there is a good chance that the preexisting instance is running an older version of the primes or fibonacci software.  You can check this by running `list-instances -f`.  It gives a list of each instance with its image name.  If you see an ami-number instead of a name, the image is defunct and some buggy version is probably grabbing work and botching it up.  Stop the instances with `stop-intance`.

---

**Symptom** : CloudMaster complains it cannot find an image name.

**Action**: you may not have built both the primes and fibonacci image with your AWS\_ENV tag.  If you have only built one, and it is the one you are interested in, then this warning is innocuous.  If it refers to the image you are interested in, it means you need to check to see if there is, indeed, no image.

---

**Symptom**: CloudMaster stops instances when you don't think it should.

**Symptom**: CloudMaster starts instances when you don't think it should

**Symptom**: CloudMaster fails to start or stop instances when you think it should.

**Action**: CloudMaster's instance management algorithm is complex, with many adjustable parameters that interact with each other in unpredictable ways.  Review the configuration parameters in CommonConfiguration, JobConfiguration, and ResourceConfiguration.

---

**Symptom**: CloudMaster complains about queues not being found.  Also primes or fibonacci service never works.

**Action**: Queues need to be created before you can use them.  This only needs to be done once, so it is not generally done in the installers (which are run multiple times).  If you are using a new `AWS_ENV` value, say "zzz," for the first time, you need to give commands such as
```
	create-queue primes-work-zzz
	create-queue primes-status-zzz
	create-queue fib-work-zzz
	create-queue fib-status-zzz
	create-queue fib-instance-zzz
```
There is a setup script that will do all this in `examples/setup`.

---

**Symptom**: CloudMaster stops instances for some unknown reason.

**Action**: Instances that do not send status for a long time are presumed to be hung, and are stopped.  What constitutes a long time is defined by tunable parameters.  If it actually takes longer than this to finish one job, then you need to increase this parameter, or else the job will never finish (since it is killed and retried).  You will only observe this if you have increased the size of the prime numbers generated by the work generator, since the standard ones finish quickly.

---

**Symptom**: Instances stop, but you don't know why.

**Action**: You should check CloudMaster's output.  It tells you when it is terminating instances.  Its reports (every minute) a list of the instances it thinks is running.  If your instance was in this list one minute, and missing the next, then CloudMaster may have decided to stop it.  If it discovers it is gone but it has not stopped it itself, it reports that.  In this case, you may be running another CloudMaster with the same AWS\_ENV.  Otherwise you probably stopped the instance, either by logging in and executing a `poweroff` command, by remotely executing this command, by running `stop-instance`, or by purposely or inadvertently stopping your instance with ElasticFox or some other tool.  If you notice right away (within a few minutes) the instance may still be visible to `list-instances`.

---

**Symptom**: Based on `top`, your compute-bound application seems to be getting only 43% of the instance CPU.  Where is the rest going?

**Action**: By looking at the CPU line at the top of the display, you can see that there is actually very little idle or system time being used.  It is all user time, except for a new and mysterious new category, "st" which is using up 57% of the time.  The "st" category is time used by other virtual machines, and it is always around 57% on small instances for compute bound jobs.  Since Amazon guaranteed a 1.7GHZ-equivalent virtual machine, it is apparently running on a faster processor and this is all you get.  Amazon explicitly states that an instance does not get to use any "leftover" CPU cycles, but is strictly limited to what is guaranteed.

---

**Symptom**: You are unsure whether an instance was started by hand (`start-instance`) or by CloudMaster.

**Action**: Log in to the instance using "login-instance".  Then do:
```
  wget 'http://169.254.169.254/latest/user-data'
```
If this gives you a 404 Not Found error, then the instance was not stated by CloudMaster.  If it gives you a short text reply that contains the values of aws\_env, aws\_access\_key and aws\_secret\_key then it was started by CloudMaster.

---

**Symptom**: You are unsure if you are running an another copy of CloudMaster.

**Action**: After you put something in the primes queue, run the command `get-queue-info primes-status`.  If the queue length is 0, then something is reading the status queue, probably an instance of CloudMaster.

---

**Symptom**: You are sending out primes jobs, but they seem not to be producing any results.

**Action**: See if they are sitting in the queue or are being removed and processed.  Run `get-queue-status primes-work`.  If the queue length is 0, then something is reading the work queue, probably a primes instance.  This may or may not be running on an Amazon instance (you might have started it locally).  If you have stopped all the Amazon primes instances (or you are sure they are all associated with different queues) then there must be a primes version running NOT on an Amazon instance.  This is probably reading the work queue and deleting the queue entries without producing the expected results.