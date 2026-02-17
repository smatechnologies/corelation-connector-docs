# SMAExecuteKeystoneCommand

This application is a command line utility which allows the user to execute common Keystone commands from OpCon. This is accomplished by building an XML request packet and feeding it to the keystone application through a SSH session.  The application tries to format the information returned into a more readable format.

## Usage

`SMAExecuteKeystoneCommand.exe –Command=listDatabases`

### Command line options

- **-Command**: This directive indicates what function to perform.  Depending on its value, other command line arguments may be required.  The possible values are:

  - backupDatabase
  - listBackups
  - listDatabases
  - listHADRStatus
  - listRollforwardTimes
  - restoreDatabase
  - purgeBackups
  - writeBackupsToMedia
  - keyClone

- **-ConfigFile**: Allows the user to specify a configuration file other than the default (SMAExecuteKeystoneCommand.ini). Optional.

- **-TestMode**: If this is specified (with no arguments), the output will simply display the configuration and command line options.

#### backupDatabase

When `-Command=backupDatabase`, the following options are valid:

- **-optionsName**: The name of a set of backup options that have been previously stored. The default options are used if this element is blank or not specified. The stored backup options may indicate that the backup is to be sent to a backup product instead of to disk. The optionsName can only contain uppercase letters, digits, and the underscore character. Optional.

- **-databaseName**: This required directive indicates which database to perform the operation upon.

- **-online**:	Valid values are “Yes” or “No”. If not specified, an online backup is performed. An offline backup causes all connections to the database to be disconnected throughout the entire backup process, so an offline backup should never be performed on the live production database. Optional.

#### listBackups

When `-Command=listBackups`, the following options are valid:

- **-databaseName**: If blank or unspecified, the system returns a list of all backups for all databases, otherwise it returns a list of the backups for the specified database.

- **-location**: Valid values are “primary” or “reload”.  The system maintains two separate lists of backups. The “primary” set of backups contains backups that have been taken, stored on disk, and not yet purged. The “reload” set of backups contains backups that have been reloaded to disk from an external media such as tape. The system only returns a list from one of these locations. If not specified, the system returns the “primary” list.

#### listDatabases

When `-Command=listDatabases`, there are no additional options.

#### listHADRStatus

When `-Command=listHADRStatus`, the following options are valid:

- **-databaseName**: This required directive indicates which database to perform the operation upon.

#### listRollforwardTimes

When `-Command=listRollforwardTimes`, the following options are valid:

- **-sourceDatabaseName**: This required directive indicates which database to perform the operation upon.

- **-sourceLocation**: Valid values are “primary” or “reload”.  The system maintains two separate lists of backups. The “primary” set of backups contains backups that have been taken, stored on disk, and not yet purged. The “reload” set of backups contains backups that have been reloaded to disk from an external media such as tape. The system only returns a list from one of these locations. If not specified, the system returns the “primary” list.

- **-sourceTimestamp**: You must specify the timestamp identifying the backup. Use the “listBackups” operation to find all the possible source backups.

#### purgeBackups

When `-Command=purgeBackups`, the following options are valid:

- **-databaseName**: This required directive indicates which database to perform the operation upon.

- **-location**: Valid values are “primary” or “reload”.  The system maintains two separate lists of backups. The “primary” set of backups contains backups that have been taken, stored on disk, and not yet purged. The “reload” set of backups contains backups that have been reloaded to disk from an external media such as tape. The system only purges from one of these locations. If not specified, the system purges from the “primary” set. If you specify “reload”, the system purges all reloaded backups for all databases.

- **-timestamp**: You must specify a retention timestamp if “location” is not specified as “reload”. This timestamp indicates that backups taken on or after the timestamp are to be retained, and backups taken prior to the timestamp are purged. The timestamp is in the form YYYYMMDDHHMMSS and is interpreted to be in the default time zone of the server’s operating system.

:::note
Corelation advises that the most current backup is NEVER purged (as a safety precaution to prevent accidental deletion).
:::

#### restoreDatabase

When `-Command=restoreDatabase`, the following options are valid:

- **-optionsName**: The name of a set of backup options that have been previously stored. The default options are used if this element is blank or not specified. The stored backup options may indicate that the backup is to be pulled from a backup product instead of from disk. The optionsName can only contain uppercase letters, digits, and the underscore character.

- **-sourceDatabaseName**: You must specify the database name of the backup.

- **-sourceLocation**: You can optionally specify a value of “primary” or “reload”. The system maintains two separate lists of backups. The “primary” set of backups contains backups that have been taken, stored on disk, and not yet purged. The “reload” set of backups contains backups that have been reloaded to disk from an external media such as tape. If not specified, the system uses the “primary” set.

- **-sourceTimestamp**: You must specify the timestamp identifying the backup. Use the “listBackups” operation to find all the possible source backups.

  This can be set to “CURRENT”.  If set to CURRENT, listBackups will be run and the latest backup timestamp will be used.

- **-destinationDatabaseName**: You must specify the destination database name. The database must already exist. This must be a test database, not the production database. The system requires an interactive “keystone” session to restore to the production database.

- **-restoreSoftware**: You can optionally specify “Yes” or “No”. If it is not specified, the system assumes “No”. You only need to specify “Yes” if the backup is from a software release prior to the current KeyStone software release. If you specify “Yes”, the system restores a private copy of the software for use by the destination database only. It does not interfere with any other databases.

- **-restoreExportFiles**: You can optionally specify “Yes” or “No” to restore export and/or import files. If not specified, the system assumes “Yes”. Specifying “No” saves disk space if these files are not needed.

- **-restoreImportFiles**: You can optionally specify “Yes” or “No” to restore export and/or import files. If not specified, the system assumes “Yes”. Specifying “No” saves disk space if these files are not needed.

- **-rollforwardTo**: You can optionally specify “endOfBackup”, “endOfLogs”, or “pointInTime”. This indicates how far the database logs should be applied. The “endOfBackup” option indicates that the logs should be applied up to the time the backup completed. The “endOfLogs” option indicates that all available logs should be applied. This is useful for making a “clone” of a database that is an exact copy up through the time that the restore operation began. The “pointInTime” option indicates that the logs should be applied up to a specified point-in-time. You must also specify “rollforwardEndOfBusinessDate” or “rollforwardTimestamp” if you use this option. If “rollforwardTo” and “rollforwardEndOfBusinessDate” and “rollforwardTimestamp” are not specified, the system uses “endOfBackup”.

- **-rollforwardTimestamp**: Optionally specifies the rollforward time in YYYYMMDDHHMMSS format in the UTC time zone. If “rollforwardTo” is specified as “endOfBackup” or “endOfLogs”, or if “rollforwardEndOfBusinessDate” is specified, then this value is ignored.

- **-rollforwardEndOfBusinessDate**: Optionally specifies the end-of-business date cutoff for rolling forward. If “rollforwardTo” is specified as “endOfBackup” or “endOfLogs” then this value is ignored. If “rollforwardTo” is specified as “pointInTime”, then either “rollforwardTimestamp” or “rollforwardEndOfBusinessDate” must be specified. If “rollforwardEndOfBusinessDate” is specified then “rollforwardTimestamp” is ignored.

  The expected format is yyyy-mm-dd.

- **-rollforwardMode**: You can optionally specify “continue” or “complete”. If not specified, the system assumes “complete”. The use of “continue” indicates that an interactive session will be used to perform further rollforward commands, or that you are restoring a database on an HADR standby system for the purpose of re-initializing the HADR processing.  

#### writeBackupsToMedia

When `-Command=writeBackupsToMedia`, the following options are valid:

- **-optionsName**: The name of a set of backup export options that have been previously stored. The default options are used if this element is blank or not specified. The stored backup export options may indicate that the backup is to be written to a network fileserver instead of a tape drive. The optionsName can only contain uppercase letters, digits, and the underscore character.

- **-databaseSelection**: You can optionally specify “All” or “Live” or “List”. If unspecified, then “All” is assumed. “All” indicates that all databases are selected. “Live” indicates that only live production databases are selected. “List” indicates that a list of databases is supplied in “databaseList” container elements. If “databaseList” container elements exist, then the system ignores this “databaseSelection” element and treats it as though “List” were specified.

- **-databaseList**: Optionally specifies a list of databases. All backups for the specified databases will be written to the external media.  Use the vertical pipe symbol (|) to separate database names.

- **-comment**: You can optionally specify a comment of up to 200 characters that will be written to the header on the external media and is displayed when the external media is reloaded. Typically this is used to indicate the contents of the media, such as “2014-12 Month-End Backups”.

#### keyClone

When `-Command=keyClone`, the following options are valid:

- **-databaseName**: This required directive indicates which database to perform the operation upon. The database must be a KeyClone database.

- **-mode**: You can specify ***status***, ***continue*** or ***runstats*** (the default is ***status***). 
  The status mode returns the status of the specified database.
  The continue mode is a command to the Klone server to continue database synchronization after a pause.
  The runstats mode runs database statistics on the specified database. This can take a few seconds to hours depending on the size of the database and the specified **priorRecordCountLimit** value.  

- **-priorRecordCountLimit**: Used for ***runstats*** mode and optionally specifies that the system should only run statistics for tables where the previous statistics information indicates that the number of records in the table is less that the priorRecordCountLimit value. If you specify 0 or do not provide a value, then the internally defined value of 1,000,000 is used. If you specify -1 the system runs statistics on all tables. 

### Sample executions

#### SMAExecuteKeystoneCommand.exe -Command=listDatabases

```
console
Copyright Software Management and Associates - 2014
Version 5.20.0.0

Command Line Arguments
----------------------
-Command                      : listDatabases
-TestMode                     : False

Configuration File
------------------
HostName                      : 174.76.4.194
Port                          : 52032
UserName                      : smaopcon
Password                      : ************
SSHEncryptionAlgorithms       : aes256-cbc,3des-cbc
SystemPrompt                  : \$
LoginTimeoutSeconds           : 30
CorelationNameSpace           : http://www.corelationinc.com/ns/administration/v1.0
CommandPrependString          : keystone -xml <<eof-tag
CommandAppendString           : eof-tag

Connected: OK
SSHStatus: Sending local version: "SSH-2.0-IP*Works! SSH Client v9.0".
SSHStatus: Read remote version string: "SSH-2.0-OpenSSH_5.8".
SSHStatus: Beginning key exchange.
SSHStatus: Preparing algorithm negotiation.
SSHStatus: Sending local enabled algorithm list.
SSHStatus: Reading remote enabled algorithm list.
SSHStatus: Selected key exchange algorithm "diffie-hellman-group14-sha1".
SSHStatus: Selected server host key algorithm "ssh-rsa".
SSHStatus: Selected client encryption algorithm "aes256-cbc".
SSHStatus: Selected server encryption algorithm "aes256-cbc".
SSHStatus: Selected client MAC algorithm "hmac-sha1".
SSHStatus: Selected server MAC algorithm "hmac-sha1".
SSHStatus: Selected client compression algorithm "none".
SSHStatus: Selected server compression algorithm "none".
SSHStatus: Algorithm negotiation complete.
SSHStatus: Sending Diffie-Hellman key exchange initialization request.
SSHStatus: Verifying server's Diffie-Hellman key exchange response.
SSHServerAuthentication: af:90:a5:e3:63:6f:d4:2e:d5:d8:33:8e:f4:fa:76:e9
SSHStatus: Diffie-Hellman key exchange complete. Calculating key sets.
SSHStatus: Sending new key packet to server. Setting client keys.
SSHStatus: Received new key packet from server. Setting server keys.
SSHStatus: Key exchange complete.
SSHStatus: Requesting service: "ssh-userauth".
SSHStatus: Service request "ssh-userauth" accepted.
SSHStatus: Authenticating user for service "ssh-connection".
SSHStatus: Requesting user's authentication mechanism list from SSH host.
SSHStatus: Attempting password authentication.
SSHStatus: Password authentication succeeded.
SSHStatus: Successfully authenticated user for service "ssh-connection".
SSHStatus: Attempting to open "session" channel [0].
SSHStatus: Successfully opened "session" channel [0].
SSHStatus: Attempting to open vt100 terminal for channel [0].
SSHStatus: Successfully opened vt100 terminal for channel [0].
SSHStatus: Attempting to start service "shell" on channel [0].
SSHStatus: Successfully started service "shell" on channel [0].
Last unsuccessful login: Mon Dec  1 09:44:54 PST 2014 on 
Last login: Fri Dec  5 06:38:50 PST 2014 on /dev/pts/0 from 67.239.72.66
*******************************************************************************
*                                                                             *
*                                                                             *
*  Welcome to AIX Version 7.1!                                                *
*                                                                             *
*                                                                             *
*  Please see the README file in /usr/lpp/bos for information pertinent to    *
*  this release of the AIX Operating System.                                  *
*                                                                             *
*                                                                             *
*******************************************************************************
$ 
Sending --> [keystone -xml <<eof-tag
]
Sending --> [<administration xmlns="http://www.corelationinc.com/ns/administration/v1.0">
]
Sending --> [  <operation>
]
Sending --> [    <listDatabases />
]
Sending --> [  </operation>
]
Sending --> [</administration>
]
Sending --> [eof-tag
]
keystone -xml <<eof-tag
> <administration xmlns="http://www.corelationinc.com/ns/administration/v1.0">
>   <operation>
>     <listDatabases />
>   </operation>
> </administration>
> eof-tag
<administration type="c" xmlns="http://www.corelationinc.com/ns/administration/v1.0">
 <operation type="a">
  <listDatabases type="c">
   <results type="c">
    <databaseList type="a">
     <databaseName>D0000T00</databaseName>
     <description>Demo</description>
     <webapp>Demo</webapp>
    </databaseList>
    <databaseList type="a">
     <databaseName>D0000T13</databaseName>
     <description>Indirect Lending</description>
     <webapp>IndirectLending</webapp>
    </databaseList>
    <databaseList type="a">
     <databaseName>D0000T98</databaseName>
     <description>Keybridge Multi-Client Testing</description>
     <webapp>Keybridge2</webapp>
    </databaseList>
    <databaseList type="a">
     <databaseName>D0000T99</databaseName>
     <description>Keybridge Testing</description>
     <webapp>Keybridge</webapp>
    </databaseList>
    <databaseList type="a">
     <databaseName>JSPRSRVR</databaseName>
     <description>Jasper Server</description>
     <webapp></webapp>
    </databaseList>
    <success>Yes</success>
   </results>
  </listDatabases>
 </operation>
</administration>
$ 
Sending --> [exit
]
SSHStatus: Reached end of data channel [0].
SSHChannelRequest: 0, exit-status
SSHStatus: SSH channel [0] closed.
exit

Session Terminated


DataBase Name             Description                                        Application               
------------------------- -------------------------------------------------- ------------------------- 
D0000T00                  Demo                                               Demo                      
D0000T13                  Indirect Lending                                   IndirectLending           
D0000T98                  Keybridge Multi-Client Testing                     Keybridge2                
D0000T99                  Keybridge Testing                                  Keybridge                 
JSPRSRVR                  Jasper Server                                                                
                                                                                                       

Success: Yes
Exit Value : 0
```

#### SMAExecuteKeystoneCommand.exe -Command= listBackups

```console
Copyright Software Management and Associates - 2014
Version 5.20.0.0

Command Line Arguments
----------------------
-Command                      : listBackups
-databaseName                 : 
-location                     : 
-TestMode                     : False

Configuration File
------------------
HostName                      : 174.76.4.194
Port                          : 52032
UserName                      : smaopcon
Password                      : ************
SSHEncryptionAlgorithms       : aes256-cbc,3des-cbc
SystemPrompt                  : \$
LoginTimeoutSeconds           : 30
CorelationNameSpace           : http://www.corelationinc.com/ns/administration/v1.0
CommandPrependString          : keystone -xml <<eof-tag
CommandAppendString           : eof-tag

Connected: OK
SSHStatus: Sending local version: "SSH-2.0-IP*Works! SSH Client v9.0".
SSHStatus: Read remote version string: "SSH-2.0-OpenSSH_5.8".
SSHStatus: Beginning key exchange.
SSHStatus: Preparing algorithm negotiation.
SSHStatus: Sending local enabled algorithm list.
SSHStatus: Reading remote enabled algorithm list.
SSHStatus: Selected key exchange algorithm "diffie-hellman-group14-sha1".
SSHStatus: Selected server host key algorithm "ssh-rsa".
SSHStatus: Selected client encryption algorithm "aes256-cbc".
SSHStatus: Selected server encryption algorithm "aes256-cbc".
SSHStatus: Selected client MAC algorithm "hmac-sha1".
SSHStatus: Selected server MAC algorithm "hmac-sha1".
SSHStatus: Selected client compression algorithm "none".
SSHStatus: Selected server compression algorithm "none".
SSHStatus: Algorithm negotiation complete.
SSHStatus: Sending Diffie-Hellman key exchange initialization request.
SSHStatus: Verifying server's Diffie-Hellman key exchange response.
SSHServerAuthentication: af:90:a5:e3:63:6f:d4:2e:d5:d8:33:8e:f4:fa:76:e9
SSHStatus: Diffie-Hellman key exchange complete. Calculating key sets.
SSHStatus: Sending new key packet to server. Setting client keys.
SSHStatus: Received new key packet from server. Setting server keys.
SSHStatus: Key exchange complete.
SSHStatus: Requesting service: "ssh-userauth".
SSHStatus: Service request "ssh-userauth" accepted.
SSHStatus: Authenticating user for service "ssh-connection".
SSHStatus: Requesting user's authentication mechanism list from SSH host.
SSHStatus: Attempting password authentication.
SSHStatus: Password authentication succeeded.
SSHStatus: Successfully authenticated user for service "ssh-connection".
SSHStatus: Attempting to open "session" channel [0].
SSHStatus: Successfully opened "session" channel [0].
SSHStatus: Attempting to open vt100 terminal for channel [0].
SSHStatus: Successfully opened vt100 terminal for channel [0].
SSHStatus: Attempting to start service "shell" on channel [0].
SSHStatus: Successfully started service "shell" on channel [0].
Last unsuccessful login: Mon Dec  1 09:44:54 PST 2014 on 
Last login: Mon Dec  8 13:48:06 PST 2014 on /dev/pts/2 from 67.239.72.66
*******************************************************************************
*                                                                             *
*                                                                             *
*  Welcome to AIX Version 7.1!                                                *
*                                                                             *
*                                                                             *
*  Please see the README file in /usr/lpp/bos for information pertinent to    *
*  this release of the AIX Operating System.                                  *
*                                                                             *
*                                                                             *
*******************************************************************************
$ 
Sending --> [keystone -xml <<eof-tag
]
Sending --> [<administration xmlns="http://www.corelationinc.com/ns/administration/v1.0">
]
Sending --> [  <operation>
]
Sending --> [    <listBackups />
]
Sending --> [  </operation>
]
Sending --> [</administration>
]
Sending --> [eof-tag
]
keystone -xml <<eof-tag
> <administration xmlns="http://www.corelationinc.com/ns/administration/v1.0">
>   <operation>
>     <listBackups />
>   </operation>
> </administration>
> eof-tag
<administration type="c" xmlns="http://www.corelationinc.com/ns/administration/v1.0">
 <operation type="a">
  <listBackups type="c">
   <databaseName></databaseName>
   <location>primary</location>
   <results type="c">
    <logs type="a">
     <databaseName>D0000T00</databaseName>
     <location>primary</location>
     <logMegabytes>414</logMegabytes>
    </logs>
    <logs type="a">
     <databaseName>D0000T13</databaseName>
     <location>primary</location>
     <logMegabytes>460</logMegabytes>
    </logs>
    <logs type="a">
     <databaseName>D0000T98</databaseName>
     <location>primary</location>
     <logMegabytes>1471</logMegabytes>
    </logs>
    <logs type="a">
     <databaseName>D0000T99</databaseName>
     <location>primary</location>
     <logMegabytes>664</logMegabytes>
    </logs>
    <logs type="a">
     <databaseName>JSPRSRVR</databaseName>
     <location>primary</location>
     <logMegabytes>346</logMegabytes>
    </logs>
    <backups type="a">
     <databaseName>D0000T99</databaseName>
     <location>primary</location>
     <timestamp>20140728173119</timestamp>
     <databaseMegabytes>85</databaseMegabytes>
     <dbhomeMegabytes>94</dbhomeMegabytes>
     <softwareMegabytes>175</softwareMegabytes>
    </backups>
    <success>Yes</success>
   </results>
  </listBackups>
 </operation>
</administration>
$ 
Sending --> [exit
]
SSHStatus: Reached end of data channel [0].
SSHChannelRequest: 0, exit-status
SSHStatus: SSH channel [0] closed.
exit

Session Terminated


------------------------------     Log Files                ------------------------------
DataBase Name             Location             Log Size (Megabytes)      
------------------------- -------------------- ------------------------- 
D0000T00                  primary              414                       
D0000T13                  primary              460                       
D0000T98                  primary              1471                      
D0000T99                  primary              664                       
JSPRSRVR                  primary              346                       
                                                                         

------------------------------     Database Backup(s)       ------------------------------
DataBase Name             Location             Timestamp            DB Size (MgBS)       DB Home Size (MgBS)  Software Size (MgBS) 
------------------------- -------------------- -------------------- -------------------- -------------------- -------------------- 
D0000T99                  primary              20140728173119       85                   94                   175                  
                                                                                                                                   

Success: Yes
Exit Value : 0
```

## Configuration file

### Configuration file parameters

:::note
Connection details can be obtained from Corelation.
:::

- **HostName**: The DNS resolvable name or IP address of the remote system to execute the command on.

- **Port**: The port that the SSH daemon (on the remote system) is listening on.

- **UserName**: The user credentials to use to log into the remote system.

- **Password**: This is the password of the User specified above.  Alternately, this can be the path and filename to an encrypted file (See [SMACreatePassword](create-password-file)).

- **SSHEncryptionAlgorithms**: The encryption algorithms to select from that can be negotiated for the connection.  The supported protocols are shown below.  The specification should be in the form of a comma separated string.

- **SSHKeyExchangeAlgorithms**: The key exchange algorithms to select from that can be negotiated for the connection.  The supported protocols are shown below.  The specification should be in the form of a comma separated string.

:::note
Never instances of SSH used by Corelation may not support any of the diffie-hellman based algorithms.  If that occurs then set SSHKeyExchangeAlgorithms to:

`SSHKeyExchangeAlgorithms=curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521`
:::

- **SSHMacAlgorithms**: The Mac algorithms to select from that can be negotiated for the connection.  The supported protocols are shown below.  The specification should be in the form of a comma separated string.

- **SSHPublicKeyAlgorithms**: The public key algorithms to select from that can be negotiated for the connection.  The supported protocols are shown below.  The specification should be in the form of a comma separated string.

- **SystemPrompt**: This is a regular expression that will be used to determine when the system is setting at a prompt string and ready for input.

:::note
The dollar sign ($) is used to match end of line.  To match a literal dollar sign, you must escape it with a preceding backslash (`\$`).
:::

- **LoginTimeoutSeconds**: The number of seconds to give the remote system to respond to the login request.

- **CommandPrependString**: The text to be submitted immediately before the generated XML sequence.  

:::caution
This should not be changed (unless directed to by SMA).
:::

- **CommandAppendString**: The text to be submitted immediately after the generated XML sequence.

:::caution
This should not be changed (unless directed to by SMA).
:::

- **CorelationNameSpace**: This is the XML NameSpace used by this Corelation interface.  The value should be `“http://www.corelationinc.com/ns/administration/v1.0”`.

:::caution
This should not be changed (unless directed to by SMA).  
:::

### Sample configuration file

```ini
#-------------------------------------------------------------------------------------
#
# This file contains the configuration parameters for SMAExecuteKeystoneCommand
#
#-------------------------------------------------------------------------------------
[Connection Parameters]
HostName=192.168.1.1
Port=52032
UserName=youruser
Password=.\yourpasswordfile.pw
SSHEncryptionAlgorithms=aes256-ctr,aes192-ctr,aes128-ctr,aes256-cbc,aes192-cbc,aes128-cbc,3des-ctr,3des-cbc,blowfish-cbc,arcfour256,arcfour128,arcfour,cast128-cbc,aes256-gcm@openssh.com,aes128-gcm@openssh.com,chacha20-poly1305@openssh.com
SSHKeyExchangeAlgorithms=curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1,diffie-hellman-group14-sha256,diffie-hellman-group-exchange-sha256,diffie-hellman-group-exchange-sha1,ecdh-sha2-nistp256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,ecdh-sha2-nistp384,ecdh-sha2-nistp521
SSHMacAlgoritms=hmac-sha2-256,hmac-sha2-512,hmac-sha1,hmac-md5,hmac-ripemd160,hmac-sha1-96,hmac-md5-96,hmac-sha2-256-96,hmac-sha2-512-96,hmac-ripemd160-96,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com
SSHPublicKeyAlgorithms=ssh-ed25519,ecdsa-sha2-nistp256,ecdsa-sha2-nistp384,ecdsa-sha2-nistp521,ssh-rsa,ssh-dss,x509v3-sign-rsa,x509v3-sign-dss

[Session Parameters]
SystemPrompt=>
LoginTimeoutSeconds=30
CommandPrependString=keystone -xml <<eof-tag
CommandAppendString=eof-tag 
CorelationNameSpace=http://www.corelationinc.com/ns/administration/v1.0
ElementExclusion=dbhomeMegabytes,softwareMegabytes,databaseMegabytes
```

## Reference

### Supported Encryption Algorithms

aes256-ctr,aes192-ctr,aes128-ctr,aes256-cbc,aes192-cbc,aes128-cbc,3des-ctr,3des-cbc,blowfish-cbc,arcfour256,arcfour128,arcfour,cast128-cbc,aes256-gcm@openssh.com,aes128-gcm@openssh.com,chacha20-poly1305@openssh.com

### Supported Key Exchange Algorithms

curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1,diffie-hellman-group14-sha256,diffie-hellman-group-exchange-sha256,diffie-hellman-group-exchange-sha1,ecdh-sha2-nistp256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,ecdh-sha2-nistp384,ecdh-sha2-nistp521

### Supported Mac Algorithms

hmac-sha2-256,hmac-sha2-512,hmac-sha1,hmac-md5,hmac-ripemd160,hmac-sha1-96,hmac-md5-96,hmac-sha2-256-96,hmac-sha2-512-96,hmac-ripemd160-96,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com

### Supported Public Key Algorithms

ssh-ed25519,ecdsa-sha2-nistp256,ecdsa-sha2-nistp384,ecdsa-sha2-nistp521,ssh-rsa,ssh-dss,x509v3-sign-rsa,x509v3-sign-dss

### Defining Backup Options

The instructions from Corelation:

For the `<backupDatabase>` operation, the main difference in the specification is the inclusion of a new `<optionsName>` element.  It is up to the credit union to configure a new backup option (this is done from within our “keystone” admin script with menu item 19) Db2 administration, submenu 3) Display or modify backup options), which has all the information about which databases should be backed up, what Db2 backup product should be used (I think “AVAMAR” is the only one supported currently, and hardly any clients—maybe no clients—are using that product…if you just leave this blank, it will be a normal disk backup), whether or not compression is used, and whether or not DBHome backup is included (import and export files mainly).  If the credit union doesn’t configure any backup options or you omit the `<optionsName>`, the system will use default options configured in the 19) Db2 administration menu.

Similarly, the `<writeBackupsToMedia>` operation now includes an `<optionsName>` element as well.  The credit union can set up named backup export options in “keystone” admin script menu item 19) Db2 administration, submenu 4) Display or modify backup export options.  The backup export options control which fileserver shared directory to use, an optional filename segment (which becomes a portion of the backup export filename), a comment, and whether or not to exclude the DBHome part of the backup (if there is one) and whether or not to exclude the Db2 logs (excluding logs can greatly reduce the size of the export file, but also reduces your options for roll-forward should you ever import and restore from that backup export file).  If the credit union does not define a backup export option or you do not specify one, the default behavior is to export to tape.

There is also an `<optionsName>` element now in the `<restoreDatabase>` operation, but unless the client is using a product like AVAMAR, this `<optionsName>` currently has no real functionality.

### Missing Element and ElementExclusion

There is a hidden configuration option called ElementExclusion that can be added to the configuration file under Session Parameters. Simply add the following: `ElementExclusion=` to the bottom of your file and in a comma delimited list define the elements you wish to ignore. Typically this command is needed when encountering a `Missing Elements in one of the child items` error for a command such as `RestoreBackup` or `ListDatabase`. It is possible to disable some fields of the `RestoreBackup`, so some of the tags we search for are no longer returned by keystone. For example, the field `dbhomeMegabytes` may be disabled and not appear in the output, so it would be added to the exclusion list and ignored in the job. Another example is with `ListDatabase` where all your databases may have a `webapp` tag except for a Jasper database. So, adding `webapp` to the list would allow that job to proceed. Values can be added to ElementExclusion in a comma-delimited list.
