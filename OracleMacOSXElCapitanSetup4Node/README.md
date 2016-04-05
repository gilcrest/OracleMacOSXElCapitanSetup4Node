# OracleMacOSXElCapitanSetup4Node
Installation walkthrough of various components needed for node development using an Oracle database for OS X Beginners.  

Long time Windows user here, trying to get setup on my new Macbook Pro for use with an Oracle database.  If you're an OS X / Linux wiz, this is probably not for you and you should go straight to the various Oracle installation pages, like [here](https://github.com/oracle/node-oracledb/blob/master/INSTALL.md#instosx).

If you're like me and used to setting up Oracle on Windows (where they have a pretty easy database installation for their Express Edition "XE"), then the macbook OS X setup is a bit bewildering. Since I am a macbook newbie, I will spell out step by step exactly what I did to setup my local environment for node development with Oracle to aid those of you who may have a similar challenge (and to help myself for the next time I need to do this).  There are some excellent posts from folks like [Christopher Jones](https://blogs.oracle.com/opal/) and [Dan McGhan](https://jsao.io/) , but most assume you know what you are doing on a macbook, and I for one do not...  There are numerous components you need to be successful on your Mac when developing node apps with Oracle, namely:

 - Virtualbox
 - Oracle Instant Client
 - sqlplus
 - node-oracledb package

## Virtualbox ##
So, in order to run an Oracle database locally, you need VirtualBox - from the Virtualbox site  - "VirtualBox is a general-purpose full virtualizer for x86 hardware, targeted at server, desktop and embedded use."  So, you're probably thinking, I want to run a database on my macbook, why do I need virtualization?  Well, unlike on Windows, Oracle does not offer Oracle XE expressly for OS X (at least not any semi-current version of Oracle).  I don't know why, but they just don't, so... you have to run a VM on your machine to have an Oracle database running on your macbook.  Oracle has nicely packaged up a TODO:Talk about VirtualBox installation

Once you've got VirtualBox installed and running, you'll need a few more things installed:

Xcode - this is a dependency of the node-oracledb driver, you get it from the Apple App Store.  It's a beefy program - close to 5GB, so you may wait a little while before it's downloaded and installed. Even after it's installed, when you open it for the first time, it will install some more stuff, so be patient....  Basically, I opened Xcode once after it was installed, but then never really needed to open it again in the context of node-oracledb usage

Oracle's Instant Client software in order to use Oracle's node-oracledb client.  You need to go to the Oracle Technology Network Site and download three files:
Instant Client Package - Basic: All files required to run OCI, OCCI, and JDBC-OCI application
Instant Client Package - SDK: Additional header files and an example makefile for developing Oracle applications with Instant Client
Instant Client Package - SQL*Plus: Additional libraries and executable for running SQL*Plus with Instant Client (you don't technically need this for node developement, but I like it for debugging purposes)
I chose the 64 bit version of these as that is appropriate for my machine. 
Once everything is downloaded, you have a couple of choices to make, based on Oracle's instructions here - I chose to go with the "Install Instant Client in /opt" route.  I had to do a little research on this one, as other linux servers I've worked on already had an /opt directory, but my macbook did not. 

switch to the root user su (superuser) using sudo:
$ sudo su -

After typing your password, you are put to the /var/root directory, I switched to the root directory:
$ cd /

create the opt directory in root (after confirming it does not exist on my macbook):
$ mkdir opt

create the /opt/oracle directory
$ mkdir /opt/oracle

navigate back to the Downloads folder (I have to use the full path here as I'm still logged in as root and can't use ~/Downloads)

$ cd /Users/putyourusernamehere/Downloads

Unzip each of the downloaded instant client zips
$ unzip instantclient-basic-macos.x64-11.2.0.4.0.zip
$ unzip instantclient-sdk-macos.x64-11.2.0.4.0.zip
$ unzip instantclient-sqlplus-macos.x64-11.2.0.4.0.zip

Once you've completed unzipping all 3 files, you'll notice they're all exploded into the same directory: instantclient_11_2 - you need to move this directory to /opt/oracle and rename the directory to instantclient while doing so with the following command

$ mv instantclient_11_2 /opt/oracle/instantclient

create a symbolic link file in the opt/oracle/instantclient directory

$ ln -s /opt/oracle/instantclient/libclntsh.dylib.11.1 /opt/oracle/instantclient/libclntsh.dylib

create more symbolic links, but this time to the default library path (/usr/local/lib)

$ ln -s /opt/oracle/instantclient/{libclntsh.dylib.11.1,libnnz11.dylib,libociei.dylib} /usr/local/lib/

Installation of node-oracledb is the simplest step of the lot 

exit to logout of the root user

$ exit
