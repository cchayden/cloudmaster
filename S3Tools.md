# S3 Tools #

These toools operate on the S3 store.

All tools need AWS credentials, which they obtan either from environment variables or a configuration file.  See AwsSetup for information on how to set this up.

You access S3 starting with a bucket, which contains key-object pairs.
Buckets are unique toroughout S3, and each one belongs to an account.
Some commands set the bucket name using the aws\_bucket setting.

## Buckets ##
### list-buckets ###
`list-buckets`

Displays the names of all buckets in your account.

### create-bucket ###
`create-bucket name`

Creates a bucket of the given name.
Bucket names must be unique in S3, so if you have already created a bucket with that name, the request will fail.

### delete-bucket ###
`delete-bucket`

Deletes the bucket of the given name.

## List Objects ##

### list-objects ###
`list-objects  [-l] [prefix [bucket]]`

Displays all keys in the given bucket, or in AWS\_BUCKET.  If the prefix is given, the display is liited to keys matching the prefix.  If "-l" is given, a long listing is displayed. A long listing includes the owner, size, and modication date of each key.

### list-object ###
`list-object key [bucket]`

Displays the object associated with the given key.  If bucket is given, look in that bucket, otherwise use AWS\_BUCKET.

## Store Objects ##
### put-object ###
`put-object filename key [bucket]`


Creates a key and stores the contents of the given file as its object (value).

## Delete Objects ##

### delete-object ###
`delete-object key [bucket]`

Deletes the object identified by the given key.  After this succeeds, the key will no longer be in the bucket.

### delete-objects ###
`delete-objects prefix [bucket]`

Deletes all objects in matching the given prefix.  Be careful!

# See Also #

  * Ec2Tools
  * SqsTools
  * SdbTools