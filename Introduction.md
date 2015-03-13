# Overview of CloudMaster #

CloudMaster provides control and monitoring of Amazon Web Services (AWS) Elastic Compute Cloud (EC2) computational resources.  It manages _pools_ of identical servers, running in the EC2 "compute cloud".  It is monitors the demand for services, and increases or decreases the pool size accordingly.

CloudMaster typically runs on your own server, not on EC2.  This way you do not have to pay for a constantly-running server when your pool services only occasional requests.  CloudMaster provides periodic reports, which can be redirected to a log file for unattended operation.

# Features #

CloudMaster provides these features:
  * Manages any number of pools.
  * Each pool is governed by a configurable set of policies.
  * Configuration is done through an init file.
  * Several models of server behavior are supported: others can be added.
  * It communicates with the instances through Simple Queue Servece (SQS) queues.
  * The current set of running instances is made available through Simple Storage Service (S3).  This allows external entities to assign work to servers.
  * Log messages sent by the instances are collected and displayed.

# Acknowledgements #
James Murty's book provided the inspiration and also the libraries that are the foundation for this project.  I have included an extended version of those libraries.

Code adapted from _Programming Amazon Web Services_.
Copyright (c) 2008 James Murty.  Used with permission.  All rights reserved.
http://oreilly.com/catalog/9780596515812/index.html

CloudMaster itself is inspired by Lifeguard (http://code.google.com/p/lifeguard), written by David Kavanagh. I redesigned and rewrote it in ruby, so that it could be used without having to install Java.  Many of my ideas were initially based on this design.

# More Information #
  * [Try It Out](ProjectSetup.md)
  * [How The Sample Applications Work](HowSampleWorks.md)
  * [How CloudMaster Works](HowCloudMasterWorks.md)
  * [Manage Your Own Instances](RunningYourOwn.md)
  * [Building A Configuration File](ConfigurationFile.md)
  * [Make Your Own Policy](MakeYourOwnPolicy.md)
  * [Project Structure](ProjectStructure.md)
  * [Using AWS Tools](UsingTools.md)
  * [Frequently Asked Questions](FAQ.md)