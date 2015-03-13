# Configuration Files #

## Configuration Files ##

Instance manager is configured using a set of configuration files, stored in **Ini File** format.  This format consists of sections enclosed in squre brackets, contaiing assignments.

By default, several configuration files are read and the results are merged, with later configuration settings overwriting earlier ones.  The files are read in this order:
  * `aws-config.ini`
  * `default-config.ini`
  * `config.ini`
You would normally put common configuration settings shared between all CloudMaster projects in `aws_config.ini` and specific onfigurations in `config.ini`.  The file `default-config.ini` is normally used to store program defaults -- you should not create such a file yourself.

Each configuration file is searched for in the following places:
  * The current directory
  * `$AWS_HOME`
  * `$HOME`


Each set of configuration files must contain an AWS section, which describes access keys and other information common to all instance pools.  For instance:

```
[AWS]
# substitute your own access key and secret key
aws_access_key='YOUR-AWS-ACCES-KEY'
aws_secret_key='YOUR-AWS-SECRET-KEY'
aws_user=ec2-instance-manager
aws_bucket=chayden
aws_key=$HOME/keys/your-keypair.pem
```

The files normally contan multiple pool sections, one for each instance pool.  Each pool section contains information on how to manage that pool.  For instance

```
[Pool-primes]
policy=job
```

The pages referenced below describe the specific parameters in each of these sections.

# See Also #
  * AwsConfiguration
  * CommonConfiguration
  * JobConfiguration
  * ResourceConfiguration
  * ManualConfiguration