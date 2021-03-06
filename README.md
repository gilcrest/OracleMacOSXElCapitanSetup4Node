# OracleMacOSXElCapitanSetup4Node
Long time Windows user here, trying to get setup on my new Macbook Pro for use with an Oracle database.  If you're an OS X / Linux wiz, this is probably not for you and you should go straight to the various Oracle installation pages, like [here](https://github.com/oracle/node-oracledb/blob/master/INSTALL.md#instosx).

If you're like me and used to setting up Oracle on Windows (where they have a pretty easy database installation for their Express Edition "XE"), then the macbook OS X setup is a bit bewildering. Since I am a macbook neophyte, I will spell out step by step exactly what I did to setup my local environment for node development with Oracle to aid those of you who may have a similar challenge (and to help myself for the next time I need to do this).  There are some excellent posts from folks like [Christopher Jones](https://blogs.oracle.com/opal/) and [Dan McGhan](https://jsao.io/), but most assume you know what you are doing on a macbook, and I for one do not... I give full credit to Chris and Dan, actually - their posts were tremendously helpful to me, I'm just going through in a bit more detail the steps I took to get node-oracledb up and running on my macbook. 

There are numerous components you need to be successful on your Mac when developing node apps with Oracle, namely:

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

**IMPORTANT** - there are 2 methods to install Oracle Instant Client: one method requires you to sudo with root access and is considered a riskier install (given you need to sudo). If you are going to have more than one user on your macbook, you may wish to go this route - you can get the step by step instructions [here](https://github.com/gilcrest/OracleMacOSXElCapitanSetup4Node/blob/master/optVersion/README_OPT.md) If, like me, you are the sole user of your macbook or prefer the less risky install, then the set of rules below are for you.

In order to use Oracle's node-oracledb client, you need to go to the Oracle Technology Network Site and download three files:

 1. Instant Client Package - Basic: All files required to run OCI, OCCI, and JDBC-OCI application
 2. Instant Client Package - SDK: Additional header files and an example makefile for developing Oracle applications with Instant Client
 3. Instant Client Package - SQL*Plus: Additional libraries and executable for running SQL*Plus with Instant Client (you don't technically need this for node developement, but I like it for debugging purposes)

*I chose the 64 bit version of these as that is appropriate for my machine.* 

Navigate to the Downloads folder
```
$ cd ~/Downloads
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

Once you've completed unzipping all 3 files, you'll notice they're all exploded into the same directory: instantclient_11_2 - you need to move this directory to your default User directory
```
mv instantclient_11_2 ~/
```
Navigate to the directory
```
cd ~/instantclient_11_2
```
Create a [symbolic link](https://en.wikipedia.org/wiki/Symbolic_link) file in the ~/instantclient directory
```
ln -s libclntsh.dylib.11.1 libclntsh.dylib
```
Create more symbolic links, this time link the OCI libraries into the User default library path (which likely does not exist on your machine yet):

First, create the User lib directory
```
mkdir ~/lib
```
Next, link the OCI libraries into the directory you just made (note, you should still be in the ~/instantclient_11_2 directory in order for the below command to work, given the pwd in the path)
```
ln -s $(pwd)/{libclntsh.dylib,libclntsh.dylib.11.1,libnnz11.dylib,libociei.dylib} ~/lib/
```

That's it for the Instant Client installation, you can validate proper installation in a few different ways - we will validate in two ways:

 1. install sqlplus
 2. install node-oracledb

## sqlplus ##
If you also wish to be able to run sqlplus locally using the Instant Client, then do the following (basically step 5 of the Instant Client [install instructions](http://www.oracle.com/technetwork/topics/intel-macsoft-096467.html#ic_osx_inst)).

Create symbolic links for required "SQL*Plus libraries"
```
ln -s ~/instantclient_11_2/{libsqlplus.dylib,libsqlplusic.dylib} ~/lib/
```
Add the Instant Client directory to the PATH environment variable
```
export PATH=~/instantclient_11_2:$PATH
```
**Note** - In order for this PATH variable addition to persist the next time you start a terminal, you can add it to your /etc/paths file using the instructions [here](http://architectryan.com/2012/10/02/add-to-the-path-on-mac-os-x-mountain-lion/#.VwxdnBMrKCR) - the instructions are for Mountain Lion, but they work for El Capitan as well.

Startup sqlplus using the "Easy Connect" form
```
sqlplus hr/oracle@localhost/orcl
```
If all went well, you'll see
```
SQL*Plus: Release 11.2.0.4.0 Production on Tue Apr 5 12:30:24 2016

Copyright (c) 1982, 2013, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options
```
## node-oracledb ##

Assuming all is well with above, we now need to add two temporary variables to your PATH to allow the node-oracledb installer to find the Instant Client libraries and headers. These variables only need to exist when you run your node-oracledb installation and will then naturally be removed when you close your shell or start a new terminal instance:
```
export OCI_LIB_DIR=~/lib
export OCI_INC_DIR=~/instantclient_11_2/sdk/include
```

Now for the fun part - install node-oracledb from the [NPM registry](https://www.npmjs.com/package/oracledb):

```
npm install oracledb
```
As part of the installation, you may see a bunch of warnings fly by, i.e. *warning: cannot refer to a non-static
      member from the handler of a constructor function try block [-Wexceptions]*, but they don't seem to do anything

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
