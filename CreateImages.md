# Create Sample Images #

This section will lead you through the process of creating your own images.  These images will run the prime number and fibonacci generators on EC2, which will interact with CloudMaster.

There are already pre-built public images built as described here.  You can simply use these images if you want to get started immediately.  If so, go to RunCloudMaster.

The code to create the images is located in `$AWS_HOME/instances`.

## Base Image ##

We will be creating three images: a base image, a primes image, and a fibonacci image.  The base image is used as the basis for the other images.  It is handy, because you can use it as the starting point for your other projects as well.  The base image is built from a Fedora 8 public image, and adds a login account (aws) and some useful tools such as emacs.

To build:
```
  cd $AWS_HOME/instances/base
  ./build-image
```

The process takes between 10 and 15 minutes.  It does the following steps:
  * Starts an instance from a Fedora 8 public image.
  * Copies files from your computer to the instance.
  * Executes an "install" command on the instance.  This command
    1. Adds the user account.
    1. Installs emacs.
    1. bundles the image (makes a snapshot of the disk).
    1. Uploads the image to your S3 bucket.
    1. Registers the image (so you can start it).
  * Stops the instance.

This last step is critical: you have started an instance, and have incurred a charge of five cents.  Unless you stop the instance, you will continue to be billed five cents per hour (forever).  The build-image script does this for you, but if it is interrupted or fails for some reason, then it might not do this.

You can use the command `list-instances` to see the status of your instances.  If you see a "terminated" instance, that is good.  If you see a "running" instance, then you should note its "i-number" and give the command
```
  stop-instance <i-number>
```

One thing that occasionally goes wrong is that the software upload (copying the bundeld disk image from your running instance to your S3 bucket) occasionally fails.  The libraries that support the upload do not deal with this gracefully, and just quit.  You should examine the output of the whole process and make sure it completed successfully.  If there is an upload error, you must repeat the process.

## Primes Image ##
Creating the primes image is not much different than creating the base image.
```
  cd $AWS_HOME/instances/primes
  ./build-image
```
This performs the same steps of starting an instance, copying software to it, installing the software, bundling, storing, registering, and stopping the instance.

The value of AWS\_ENV that you set up in the environment is incorporated into the image name.  This way you can set it to "test" and make a "primes-test" image, and then later set it to "production" and make a "primes-production" image. This allows development, test, and production environments to remain separate.  To start with we will leave AWS\_ENV undefined so there will be no suffix on the image.

## Fibonacci Image ##
Creating the fibonacci image is more of the same:
```
  cd $AWS_HOME/instances/fibonacci
  ./build-image
```

## Verify ##
Now you have created three images.  Check that they have all been registered:
```
  list-my-images
```
You should see three lines of output, one for each image.

You can start one of the images and log in to it.
```
  start-instance <ami-number or name>
  wait-for-active-instance <i-number>
  sleep 15
  login-instance <i-number>
```
The `start-instance` command displays an instance id ("i-number") identifying the instance that it has started.  The instance takes a while to boot, and until then you cannot log in.  You can run `list-instances` until you see it is running, or you can use the `wait-for-active-instance` command to to that.  Even after the instance is running, its servers are not started right away, so you need to wait an additional few seconds befoe you log in.

Once you are logged in as root, you can su to the account you made (AWS\_USER) or you can just play around as root.  Once you are done, you can stop the instance by giving the command `poweroff` **on the instance**.  _Do not do this on your workstation._  Or else you can give the command
```
  stop-instance <i-number>
```
on your workstation to stop the instance.

## Next Step ##
Now that you have created the necessary images, you can start them using the instance manager: RunCloudMaster