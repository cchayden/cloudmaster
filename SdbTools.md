# SimpleDB Tools #

These toools operate on SimpleDB.

All tools need AWS credentials, which they obtan either from environment variables or a configuration file.  See AwsSetup for information on how to set this up.

SimleDB consists of a number of independent **domains**.  Each domain contains **items** which contain **attributes** and **values**.  These commands let you create, list, and delete domains, and to create, update, delete, and retrieve items.
Each item has a unique **name** that is used to identify it.  This name, the attributes, and the values are all handled as strings.  A single attribute may have multiple values.  When all the vaues of an attribute have been deleted, the attribute itself disappears (so an attribute cannot have no value).  Similarly, when all attibutes are deleted, an item disappears, so an item cannot have no attributes.

## Domains ##
### list-domains ###
`list-domains`

Displays the names of all domains in your account.

### create-domain ###
`create-domain name`

Creates a domain of the given name.
Domain names must be unique in SimpleDB, so if you have already created a bucket with that name, the request will fail.

### delete-domain ###
`delete-domain`

Deletes the domain of the given name.

## Items ##

### list-attributes ###
`list-attributes domain-name item-name  [attribute-name]`

Displays attributes and values of the item with the given name in the given domain.  If no attribute name is given, then all attributes are displayed, otherwise just the given attribute is displayed.

## Create or Update Attributes ##
### put-attributes ###
`put-attributes domain-name item-name [-r] [attribute:value]+`

Creates or updates the attributes of the item with the given item-name in the given domain.  If the item does not exist, it is created, otherwise it is updates.  If "-r" is given, attributes values replace those already present, otherwise values are added to the existing attributes.  One or more attribute:value pairs must be given.  The attribute is separated from the value by a colon, which is not part of either the attribute or the value.

### copy-attributes ###
`copy-attributes from-domain-name from-item-name to-domain-name to-item-name [-r] [attribute]*`

Copies the attributes of the item with the given from-item-name in the given from-domain to the item to-item-name in the to-domain.  If attributes are given, only these attributes are copied.  Otherwise all attributes are copied.  If "-r" is given, attributes values replace those already present, otherwise values are added to the existing attributes.

### delete-attributes ###
`delete-attributes domain-name item-name [attribute[:value]]*`

In a given domain, deletes the item, all attributes, or specific attribute:value pairs, depending on the arguments given.  If only the item-name is given, deletes the item.  If an attribute is given, deletes al values of that attribute in the item.  If an attribute:value pair is given, eletes only that value.  Any number of attribtes or attribute:value pairs can be given wth this command.

### query ###
`query domain-name [query-expression]`

Performs a query over the items in the given domain.  See the SimpleDB documentation for instructions on how to write query expressions.  Be sure to enclose these expressions in double quotes, so they will be treated as a single argument to this command.  Displays the item names matching the query expression.  You need to call `get-attributes` to get the contents of the item(s).

### query-with-attributes ###
`query-with-attributes domain-name [query-expression [attribute-name]]`

Performs a query over the items in the given domain, as in `query`.  Displays the item names and the attributes of the items matching the query expression.  If attribute-name is given, only values of that attribte are displayed, otherwise all attributes and values are displayed.

# See Also #

  * Ec2Tools
  * SqsTools
  * S3Tools