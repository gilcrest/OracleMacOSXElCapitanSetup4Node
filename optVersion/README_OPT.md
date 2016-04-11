# Oracle Instant Client /opt installation instructions

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
