# OracleMacOSXElCapitanSetup4Node
Installation walkthrough of various components needed for node development using an Oracle database for OS X Beginners.  

Long time Windows user here, trying to get setup on my new Macbook Pro for use with an Oracle database.  If you're an OS X / Linux wiz, this is probably not for you and you should go straight to the various Oracle installation pages, like [here](https://github.com/oracle/node-oracledb/blob/master/INSTALL.md#instosx).

If you're like me and used to setting up Oracle on Windows (where they have a pretty easy database installation for their Express Edition "XE"), then the macbook OS X setup is a bit bewildering. Since I am a macbook newbie, I will spell out step by step exactly what I did to setup my local environment for node development with Oracle to aid those of you who may have a similar challenge (and to help myself for the next time I need to do this).  There are some excellent posts from folks like [Christopher Jones](https://blogs.oracle.com/opal/) and [Dan McGhan](https://jsao.io/) , but most assume you know what you are doing on a macbook, and I for one do not...  There are numerous components you need to be successful on your Mac when developing node apps with Oracle, namely:

 - node.js
 - Git
 - Virtualbox
 - SQL Developer
 - Xcode
 - Oracle Instant Client
 - node-oracledb package
 - sqlplus

## node.js ##
I won't go into any depth on node.js installation on OS X as it's extremely well documented in numerous places, but you can get it [here](https://nodejs.org/en/)

## Git ##
I won't got into depth for Git, either, but I used this [handy dandy Mac OS X Dev setup page](https://github.com/nicolashery/mac-dev-setup#mac-os-x-dev-setup) for a bunch of other useful stuff...  I followed the [setup for Git](https://github.com/nicolashery/mac-dev-setup#git) verbatim (note, you need to go through the Homebrew steps first).

## Virtualbox ##
In order to run an Oracle database locally on OS X, you need VirtualBox.  From the Virtualbox site - "VirtualBox is a general-purpose full virtualizer for x86 hardware, targeted at server, desktop and embedded use."  So, you're probably thinking, I want to run a database on my macbook, why do I need virtualization?  Well, unlike on Windows, Oracle does not offer Oracle XE expressly for OS X (at least not any semi-current version of Oracle).  I don't know why, but they just don't, so... you have to run a VM on your machine to have an Oracle database running on your macbook. Dan McGhan has done all the legwork for [step by step instructions](https://jsao.io/2015/10/creating-a-local-sandbox-for-node-js-and-oracle-database/) and I followed them verbatim up through *Part 2: Import Database App Development VM*.  Parts 3 an 4 do continue the node setup, but I believe it is actually installing node on the Virtualbox VM, however, my goal is to have it running on my local machine, not the VM (again, I only need the VM to run a local Oracle database).

The only other thing I did to alter the Virtualbox installation is to follow Christopher Jones' post [The Easiest Way to Install Oracle Database on Mac OS X](https://blogs.oracle.com/opal/entry/the_easiest_way_to_enable) and enabled the TCP rule exactly as he specified in the post.

## SQL Developer ##
In order to manage db objects and do just about anything in the database, I use SQL Developer.  The installation of this is straightforward and files/instructions can be found [here](http://www.oracle.com/technetwork/developer-tools/sql-developer/downloads/index.html)

Once SQL Developer is installed, connect to your Virtualbox database by creating a connection with Connection Type = "Basic", Hostname = "localhost", Port = 1521 and Service Name = "orcl"

Once setup is complete, you just need to make sure your VM is up and running and SQL Developer should be able to connect (as it uses something other than the soon to be installed Instant Client).

## Xcode
This is a dependency of the node-oracledb driver, you get it from the Apple App Store.  It's a beefy program - close to 5GB, so you may wait a little while before it's downloaded and installed. Even after it's installed, when you open it for the first time, it will install some more stuff, so be patient....  Basically, I opened Xcode once after it was installed, but then never really needed to open it again in the context of node-oracledb usage

## Oracle Instant Client ##
In order to use Oracle's node-oracledb client, you need to go to the Oracle Technology Network Site and download three files:

 1. Instant Client Package - Basic: All files required to run OCI, OCCI, and JDBC-OCI application
 2. Instant Client Package - SDK: Additional header files and an example makefile for developing Oracle applications with Instant Client
 3. Instant Client Package - SQL*Plus: Additional libraries and executable for running SQL*Plus with Instant Client (you don't technically need this for node developement, but I like it for debugging purposes)

*I chose the 64 bit version of these as that is appropriate for my machine.* 

Once everything is downloaded, you have a couple of choices to make, based on Oracle's instructions [here](https://github.com/oracle/node-oracledb/blob/master/INSTALL.md#-531-install-instant-client-in-opt) or [here](https://github.com/oracle/node-oracledb/blob/master/INSTALL.md#-532-install-instant-client-in-a-user-directory) - I chose to go with the "Install Instant Client in /opt" route.  I had to do a little research on this one, as other linux servers I've worked on already had an /opt directory, but my macbook did not. 

switch to the root user su (superuser) using sudo:

```
$ sudo su -
```

After typing your password, you are put to the /var/root directory, switch to the root directory:

```
$ cd /
```

create the opt directory in root (after confirming it does not already exist on your macbook):

```
$ mkdir opt
```

create the /opt/oracle directory

```
$ mkdir /opt/oracle
```

navigate back to the Downloads folder (I have to use the full path here as I'm still logged in as root and can't use ~/Downloads)

```
$ cd /Users/putyourusernamehere/Downloads
```

Unzip each of the downloaded instant client zips

```
$ unzip instantclient-basic-macos.x64-11.2.0.4.0.zip
```

```
$ unzip instantclient-sdk-macos.x64-11.2.0.4.0.zip
```

```
$ unzip instantclient-sqlplus-macos.x64-11.2.0.4.0.zip
```

Once you've completed unzipping all 3 files, you'll notice they're all exploded into the same directory: instantclient_11_2 - you need to move this directory to /opt/oracle and rename the directory to instantclient while doing so with the following command

```
$ mv instantclient_11_2 /opt/oracle/instantclient
```

create a [symbolic link](https://en.wikipedia.org/wiki/Symbolic_link) file in the opt/oracle/instantclient directory

```
$ ln -s /opt/oracle/instantclient/libclntsh.dylib.11.1 /opt/oracle/instantclient/libclntsh.dylib
```

create more symbolic links, but this time to the default library path (/usr/local/lib)

```
$ ln -s /opt/oracle/instantclient/{libclntsh.dylib.11.1,libnnz11.dylib,libociei.dylib} /usr/local/lib/
```

exit to logout of the root user

```
$ exit
```

That's it for the Instant Client installation, you can validate proper installation in a few different ways - we will validate in two ways:

 1. install node-oracledb
 2. install sqlplus

## node-oracledb ##

This is the fun/easy part - install node-oracledb from the [NPM registry](https://www.npmjs.com/package/oracledb):

```
npm install oracledb
```

Once installed, in order to validate, simply clone the node-oracledb Github repository into whichever directory you choose

```
git clone https://github.com/oracle/node-oracledb.git

```

Once cloned, you can remove any directories you don't want (I chose to remove everything except the examples directory) and then modify the dbconfig.js file with the defaults for connecting to the database that is running on the Virtualbox VM you should have running.

```
module.exports = {
  user          : process.env.NODE_ORACLEDB_USER || "hr",
  password      : process.env.NODE_ORACLEDB_PASSWORD || "oracle",
  connectString : process.env.NODE_ORACLEDB_CONNECTIONSTRING || "localhost/orcl",
  externalAuth  : process.env.NODE_ORACLEDB_EXTERNALAUTH ? true : false
};
```

Run one of the examples
```
node select1.js
```
You should get a result that looks like
```
[ { name: 'DEPARTMENT_ID' }, { name: 'DEPARTMENT_NAME' } ]
[ [ 180, 'Construction' ] ]
```
That's really all that's needed for node-oracledb setup!

## sqlplus ##
If you also wish to be able to run sqlplus locally using the Instant Client, then 