# Environment Set Up #

A single environment varialbe `AWS_HOME` is used to specify where `cloudmaster` is installed.  All other configuration (such as your access key and secret key) are stored in `config.ini` in this directory.  To use the examples in this project, you also need to set up `PATH` and `RUBYLIB`

Here is what I have in by own .bash\_profile.

```
export AWS_HOME=$HOME/cloudmaster
PATH=$PATH:$AWS_HOME/tools
export RUBYLIB=$AWS_HOME/lib
```

`AWS_HOME` is set to where you have stored the cloudmaster project.  It is used to set up your PATH and to build images.

CloudMaster comes with a collection of useful tools (UsingTools).  Setting `PATH` and `RUBYLIB` as suggested allows you to use these tools.

Next we will set up AWS [AwsSetup](AwsSetup.md).