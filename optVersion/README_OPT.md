# Oracle Instant Client /opt installation instructions

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
If you also wish to be able to run sqlplus locally using the Instant Client, then do the following (basically step 5 of the Instant Client [install instructions](http://www.oracle.com/technetwork/topics/intel-macsoft-096467.html#ic_osx_inst)), though note the different directory path than given in the install instructions based on the /opt choice we made above.

Create symbolic links for required "SQL*Plus libraries" -- again, note directory structure difference (same as above)
```
ln -s /opt/oracle/instantclient/{libsqlplus.dylib,libsqlplusic.dylib} /usr/local/lib/
```
Add the Instant Client directory to the PATH environment variable
```
export PATH=/opt/oracle/instantclient:$PATH
```
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
That's it!  I hope this helps...
