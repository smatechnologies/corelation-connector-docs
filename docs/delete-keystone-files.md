# SMADeleteKeyStoneFiles

This application sends the appropriate commands to KeyStone to update the database and delete the specified files.  It requires an SSH session to get the exact name of the files (that match the specified mask) and then uses the web service interface to request the file deletion(s) from KeyStone.

## Usage

`SMADeleteKeyStoneFiles.exe -Filenamemask TEST*`

### Command line options

- **-ConfigFile**: The name of the configuration file to use. Optional. Default value is `%PROGRAMDATA%\SMADeleteKeyStoneFiles.ini`.

- **-Debug**: Enables debug mode, including more detailed logging in the job output.

- **-DumpXML**: Write all XML data into the job output.

- **FilenameMask**: Regular expression that selects which files to delete.

- **OnlyListFiles**: Only list the files that matched the `FileNameMask` and do not delete them.

## Configuration file

## Configuration file parameters

- **Hostname**: The DNS resolvable name or IP address of the machine where the file(s) to be deleted are stored.

- **Port**: The port to connect to.

.... THIS NEEDS TO BE COMPLETED

### Sample configuration file

```ini
#-------------------------------------------------------------------------------------
#
#	This file contains the configuration parameters for SMADeleteKeyStoneFiles
#
#-------------------------------------------------------------------------------------
[SSH Connection Parameters]
HostName=184.191.169.146
Port=52032
UserName=smaopcon
Password=.\mypassword.dat
SSHEncryptionAlgorithms=aes256-cbc,3des-cbc
SystemPrompt=\$ 
LoginTimeoutSeconds=30
CommandExecutionTimeoutSeconds=60
OutputRetrievalSeconds=3
KeyStoneReportDirectory=/cr/dbhome/D0000T99/export

[KeyStone Connection Parameters]
CorelationIPAddress=184.191.169.146
CorelationPort=52007
CorelationUser=sma
CorelationPassword=.\mykeystonepw.dat
CorelationDeviceName=system
CorelationNameSpace=http://www.corelationinc.com/queryLanguage/v1.0
```

## Supported encryption protocols

|Name|Description|
|---|---|
|aes256-ctr|256-bit AES encryption in CTR mode|
|aes256-cbc|256-bit AES encryption in CBC mode|
|aes192-ctr|192-bit AES encryption in CTR mode|
|aes192-cbc|192-bit AES encryption in CBC mode|
|aes128-ctr|128-bit AES encryption in CTR mode|
|aes128-cbc|128-bit AES encryption in CBC mode|
|3des-ctr|192-bit (3-key) triple DES encryption in CTR mode|
|3des-cbc|192-bit (3-key) triple DES encryption in CBC mode|
|cast128-cbc|CAST-128 encryption|
|blowfish-cbc|Blowfish encryption|
|arcfour|ARC4 encryption|
|arcfour128|128-bit ARC4 encryption|
|arcfour256|256-bit ARC4 encryption|
