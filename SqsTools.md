# SQS Tools #

These commands operate on SQS queues.  If the underlying SQS operation fails, they display an error message.

All tools need AWS credentials, which they obtan either from environment variables or a configuration file.  See AwsSetup for information on how to set this up.

## List ##
### list-queues ###
`list-queues`

Displays all queues available in the account.

## Manage Queues ##
### create-queue ###
`create-queue name`

Creates a queue with the given name.  Queues are local to an account.  If the queue already exists, this still succeeds.
### delete-queue ###
`delete-queue name`

Deletes a queue of the given name.  If the queue does not exist, displays an error.
### get-queue-info ###
`get-queue-info name`

Displays the queue attributes, which are currently the approximate number of mesages in the queue and the visibility timeout.
### modify-queue ###
`modify-queue name visibility-timeout`

Modifies attributes of a queue.  The only attribute that can be modified is the visibility timeout.

## Send and Receive ##
### send-queue ###
`send-queue name message`

Sends the message to the given queue.  The message may be any string, but make sure you quote it appropriately.

### receive-queue ###
`receive-queue name`

Receives a message from the queue and displays it, and deletes it.
### dump-queue ###
`dump-queue name`

Reads and deletes repeatedly from the queue, displaying the messages, until the queue is empty.

# See Also #


  * S3Tools
  * Ec2Tools
  * SdbTools