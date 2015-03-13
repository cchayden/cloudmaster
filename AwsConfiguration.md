# AWS Configuration Parameters #

## Common AWS Configuration Parameters ##

These configuration parameters are shared by all the instance pools.
Most of these parameters are used to establish communication with AWS as a whole.
The values are stored in the `[AWS]` section of one of the configuration files, normally `aws_config.ini`.

  * `aws_env` -- Used to keep development, test, and production environments separate.  This symbol is incorporated in the image, queue, and S3 key names, so that two developers using different values of env should never collide.
  * `aws_access_key` -- The access key used to identify you to Amazon.  This and the aws\_secret key  can be obtained from your AWS account page, and identify you to AWS.  They are used in every interaction have with the web service.
  * `aws_secret_key` -- The secret key used to prove you are who you claim to be.
  * `aws_user` -- This is the name of an account that will be used on the instances to run the sample services.  You should probably pick your own favorite login name.  The user string is also incorporated into image names.
  * `aws_bucket` -- The bucket to use for storing machine images, and also used when storing information about active instances.
  * `aws_key` -- The full pathnane of the keypair file used to log into instances.  See the explanation below.

You will use a ssh keypair to log into instances running on EC2.  The EC2 service will make this for you, but you have to store it.  `aws_key` specifies the full pathname of the keypair file.  I recommend you store your keys in `$HOME/keys`).  By convenion the keypair file is named 

<aws\_user>

-kp.pem.

You need to store the certificate and private key that you obtained from the AWS web page in the same directory as the ssh keypair. Give the filenames in `aws_cert` and `aws_private_key`.  These credentials are used to create and store images, but are not required to run CloudMaster itself.

# See Also #
  * CommonConfiguration
  * JobConfiguration
  * ResourceConfiguration