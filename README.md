# cfdns
CFDNS is a CloudFlare DNS CLI client in bash, with a little (**a lot**) `jq`!

There is an [offical CloudFlare cli client](https://blog.cloudflare.com/python-cloudflare/) but I did not want to install another python module, so this is inspired by the lessons learnt from [my very own rsdns](https://github.com/linickx/rsdns)

## Basic Usage

Using cfdns should be straight forward.  


     cfdns [-eaidh] command

### Options:  

 * `-e` email address
 * `-a` API key
 * `-i` ID (Doamin ID)
 * `-d` Domain
 * `-t` TTL
 * `-h` Help
 * `-v` Verbose (Debug Mode)

### Commands:

 * list                ~ List Domains or Records ( d domain.com | -i xxyyzz)
 * list help           ~ Prints the Help for List Command
 * add                 ~ Add a record
 * add help            ~ Prints the Help for the Add Command
 * add a               ~ Add an A Record
 * update              ~ Update a record
 * update help         ~ Prints the Help for the Update Command
 * update a            ~ Add an A Record
 * delete              ~ Delete a record
 * delete help         ~ Prints the Help for the Delete Command
 * find_domain         ~ Find a Domain ID
 * find_domain help    ~ Prints the Help for the find_domain Command
 * find_record         ~ Find a record by type and name
 * find_record help    ~ Prints the Help for the find_record Command
 * help                ~ Prints out the usage information
 * version             ~ Print the version number
 * readme              ~ Generates README.md

#### Version = 0.1.1
## The Config File
The config file is `~/.cfdns_config` the following variables are supported to save some typing:

* `CFEMAIL` - Your Email Address (associated with your CloudFlare account)
* `CFAPIKEY` - Your CloudFlare API Key
* `CFDOM` - CloudFlare Domain (if you only work with one zone)
* `CFDOMID` - CloudFlare Domain ID (if you only work with one zone)
* `CFRTTL` - TTL (Time to Live) for if you are not a fan of automatic

Example File:

    $ cat ~/.cfdns_config
    CFEMAIL="cfdns@linickx.com"
    CFAPIKEY="7846b13a48e6ac2b8dd79264f8ac814a12765"
    $


## Listing Domains and Records

 By Default list will print out all the zones (domains) associated with your cloudflare accout  
 
 If you supply the `-d`, domain reords will be shown, or you can supply the domain id with `-i`  
 e.g.  
     `cfdns -d linickx.com list`  
 or  
     `cfdns -i xxxyyyzzz list`  

 Example output:

    $ ./cfdns -d linickx.com list
    Domain linickx.com = 166ce8f27d9ca271e885f09b43ef1af0
    ID                                  Name                             Type     Content
    166ce8f27d9ca271e885f09b43ef1af1    *.linickx.com                    A        5.79.27.96
    166ce8f27d9ca271e885f09b43ef1af2    linickx.com                      A        5.79.27.96
    166ce8f27d9ca271e885f09b43ef1af3    www.linickx.com                  A        5.79.27.96
    166ce8f27d9ca271e885f09b43ef1af4    *.linickx.com                    AAAA     2a00:1a48:7803:107:181e:ce0e:ff08:f58
    166ce8f27d9ca271e885f09b43ef1af5    linickx.com                      AAAA     2a00:1a48:7803:107:181e:ce0e:ff08:f58
    166ce8f27d9ca271e885f09b43ef1af6    www.linickx.com                  AAAA     2a00:1a48:7803:107:181e:ce0e:ff08:f58
    $
 

## Add a DNS Record

 All commands require either `-i` or `-d` to direct the add request to the correct zone
 
 The first argument is the "name" for example test.linickx.com  
 The second argument is the "content" for example 123.123.1.1  
 The third argument option, supply "proxy" to enably proxying (which is off by default)  

 e.g.  
     `cfdns -d linickx.com add a test.linickx.com 123.123.1.1`  
 or  
     `cfdns -d linickx.com add a test.linickx.com 123.123.1.1 proxy`  
 or  
     `cfdns -i xxxyyyzzz add a test.linickx.com 123.123.1.1`  
 or  
     `cfdns -i xxxyyyzzz add a test.linickx.com 123.123.1.1 proxy`  

 This is an example output:

    $ ./cfdns -d linickx.com add a test.linickx.com 123.123.1.1 proxy
    Domain linickx.com = 166ce8f27d9ca271e885f09b43ef1af0
    Requesting Record...
     Name: test.linickx.com
     Content: 123.123.1.1
     Proxied: true
    Success.
    $

### Supported (Implemented) Record Types
* a


## Update a DNS Record

 Updated / Change an existing record.

 Example:

    $ ./cfdns -d linickx.com update a test.linickx.com 4.4.4.3
    Domain linickx.com = 166ce8f27d9ca271e885f09b43ef1af0
    Record A test.linickx.com = f5962f2ccdc0de9195c2352f893ff57c
    Updating Record...
     Name: test.linickx.com
     Content: 4.4.4.3
    Success.
    $



## Delete a Record

 Delete an existing record.

 Example:

    $ ./cfdns -d linickx.com delete a test.linickx.com
    Domain linickx.com = 166ce8f27d9ca271e885f09b43ef1af0
    Record A test.linickx.com = f5962f2ccdc0de9195c2352f893ff57c
    Delete Record (test.linickx.com)...
    Success.
    $


## Find A Domain ID

 The input is a domain name (zone) the output is the CloudFlare Zone ID.  
 Mainly used as an internal function, but might be useful for someone.

 Example:

    $ ./cfdns find_domain linickx.com
    Domain linickx.com = 166ce8f27d9ca271e885f09b43ef1af0
    $


## Find A Record ID

The input is similar to other commands, record type folled by a record name; the output is the CloudFlare Record ID. ** REQUIRES ** `-d`   
Mainly used as an internal function, but might be useful for someone.

Example:

    $ ./cfdns -d linickx.com find_record a linickx.com
    Domain linickx.com = 166ce8f27d9ca271e885f09b43ef1af0
    Record A linickx.com = 22097d2d138caeade7e3326434a38ad5
    $

