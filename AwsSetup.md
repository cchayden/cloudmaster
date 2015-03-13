# AWS Setup #

This section tells you how to create the bucket, keypair, and other AWS items that you need to use the sample applications.  By convention, the settings in this section are stored in `aws-setup.ini` so they can be shared by multiple CloudMaster projects.  Hower, if you want, you an put all the configuration parameters in a single configuration file.

Make sure you have set up the environment, as described in EnvironmentSetUp.

## Create bucket ##

A bucket name gives you an area within S3 to store data.  Bucket names are unique across all of S3, so you need to pick something unique.

Try
```
  create-bucket <your bucket name>
```

If you have chosen a unique name, then the bucket is assigned to you and you see a confirmation message.  If this message is "not created" then it probably means that the bucket name is already taken, and you need to choose something else.

Once you have established a bucket, make sure to update the aws\_bucket setting in your config.ini file.

You can see which buckets are assigned to your account using:
```
  list-buckets
```

You can delete a bucket (careful) using:
```
  delete-bucket <bucket name>
```

## Create Keypair, Queues, and Groups ##

Now you need to create a keypair, queues used by your instances to communicate with instance manager, and security groups to protect the instances.  Assuming you have set up your `aws_keys` seting in your config file properly, then you can do everything by running one script.
```
  $AWS_HOME/examples/setup
```

You should only have to run this script once, although if you want to create an additional environment you will need to rerun the portion of the script that creates queues.

## Next Step ##
Next you need primes and fibonacci programs, stored as EC2 images, to run from CloudMaster.  There are pre-built public images named `chayden/cloudmaster-ami-primes` and `chayden/cloudmaster-ami-fibonacci` that you can run to try out the system.  If you want to use these, you can skip the next step.  But if you want to see how these are built, it is described in detail.

To create images, see CreateImages.

To run instance manager with the pre-built images, to to RunCloudMaster.