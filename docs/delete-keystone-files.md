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

:::note
Connection details can be obtained from Corelation.
:::

### SSH Connection Parameters

- **HostName**: The DNS resolvable name or IP address of the machine where the file(s) to be deleted are stored.

- **Port**: The port that the SSH daemon (on the remote system) is listening on.

- **UserName**: The user credentials to use to log into the remote system.

- **Password**: This is the password of the User specified above. Alternately, this can be the path and filename to an encrypted file (See [SMACreatePassword](create-password-file)).

- **SSHEncryptionAlgorithms**: The encryption algorithms to select from that can be negotiated for the connection. The supported protocols are shown below. The specification should be in the form of a comma separated string.

- **SSHKeyExchangeAlgorithms**: The key exchange algorithms to select from that can be negotiated for the connection. The supported protocols are shown below. The specification should be in the form of a comma separated string.

:::note
Newer instances of SSH used by Corelation may not support any of the diffie-hellman based algorithms. If that occurs then set SSHKeyExchangeAlgorithms to:

`SSHKeyExchangeAlgorithms=curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521`
:::

- **SSHMacAlgorithms**: The Mac algorithms to select from that can be negotiated for the connection. The supported protocols are shown below. The specification should be in the form of a comma separated string.

- **SSHPublicKeyAlgorithms**: The public key algorithms to select from that can be negotiated for the connection. The supported protocols are shown below. The specification should be in the form of a comma separated string.

- **SystemPrompt**: This is a regular expression that will be used to determine when the system is sitting at a prompt string and ready for input.

:::note
The dollar sign ($) is used to match end of line. To match a literal dollar sign, you must escape it with a preceding backslash (`\$`).
:::

- **LoginTimeoutSeconds**: The number of seconds to give the remote system to respond to the login request.

- **CommandExecutionTimeoutSeconds**: The maximum number of seconds to wait for a command to execute.

- **OutputRetrievalSeconds**: The number of seconds to wait before retrieving command output.

- **KeyStoneReportDirectory**: The directory path on the remote system where KeyStone export files are stored (e.g., `/cr/dbhome/D0000T99/export`).

### KeyStone Connection Parameters

- **CorelationIPAddress**: The DNS resolvable name or IP address of the Corelation/KeyStone server.

- **CorelationPort**: The port number that the Corelation/KeyStone web service is listening on.

- **CorelationUser**: The username for authenticating to the Corelation/KeyStone web service.

- **CorelationPassword**: The password for the Corelation user. Alternately, this can be the path and filename to an encrypted file (See [SMACreatePassword](create-password-file)).

- **CorelationDeviceName**: The device name used by the Corelation system (typically "system").

- **CorelationNameSpace**: This is the XML NameSpace used by the Corelation interface. The value should be `http://www.corelationinc.com/queryLanguage/v1.0`.

:::caution
This should not be changed (unless directed to by SMA).
:::

### SSL/TLS Configuration (Version 26.00.00.00+)

The following parameters enable secure SSL/TLS connections to the Corelation server. This feature was added in version 26.00.00.00 and provides encryption for data transmitted between SMADeleteKeyStoneFiles and the Corelation server.

- **UseSSL**: Set to `true` to enable SSL/TLS encryption, `false` for plain TCP connections. Default: `false`.
  - When set to `false`, the connector maintains backward compatibility with plain TCP connections
  - When set to `true`, all communication with the Corelation server is encrypted

- **TLSVersion**: Specifies which TLS protocol version to use. Required when `UseSSL=true`.
  - `TLS` = TLS 1.0 only
  - `TLS11` = TLS 1.1 only
  - `TLS12` = TLS 1.2 only (**recommended**)
  - `N/A` = Auto-negotiate (supports SSL 3.0, TLS 1.0, TLS 1.1, TLS 1.2)

:::tip
For production environments, it is recommended to use `TLS12` for the strongest security. Only use `N/A` if you need to support legacy systems or are unsure which TLS version the Corelation server supports.
:::

- **CorelationServerName**: The server name for certificate validation. Required when `UseSSL=true`.
  - This must match the Common Name (CN) or Subject Alternative Name (SAN) in the certificate presented by the Corelation server
  - Certificate validation will fail if there is a mismatch between this value and the server's certificate
  - Set to `N/A` when `UseSSL=false`

:::caution
Proper certificate validation is essential for secure connections. Ensure the `CorelationServerName` matches the certificate issued by your Corelation server. Contact your Corelation administrator if you're unsure of the correct server name.
:::

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
SSHEncryptionAlgorithms=aes256-ctr,aes192-ctr,aes128-ctr,aes256-cbc,aes192-cbc,aes128-cbc,3des-ctr,3des-cbc,blowfish-cbc,arcfour256,arcfour128,arcfour,cast128-cbc,aes256-gcm@openssh.com,aes128-gcm@openssh.com,chacha20-poly1305@openssh.com

SSHKeyExchangeAlgorithms=curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1,diffie-hellman-group14-sha256,diffie-hellman-group-exchange-sha256,diffie-hellman-group-exchange-sha1,ecdh-sha2-nistp256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,ecdh-sha2-nistp384,ecdh-sha2-nistp521
#####	Note:	Newer instances of SSH used by Corelation may not support any of the diffie-hellman based algorithms.  If 
#####	that occurs then comment out the previous line and uncomment the following line.
#SSHKeyExchangeAlgorithms=curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521

SSHMacAlgorithms=hmac-sha2-256,hmac-sha2-512,hmac-sha1,hmac-md5,hmac-ripemd160,hmac-sha1-96,hmac-md5-96,hmac-sha2-256-96,hmac-sha2-512-96,hmac-ripemd160-96,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com
SSHPublicKeyAlgorithms=ssh-ed25519,ecdsa-sha2-nistp256,ecdsa-sha2-nistp384,ecdsa-sha2-nistp521,ssh-rsa,ssh-dss,x509v3-sign-rsa,x509v3-sign-dss

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

#-------------------------------------------------------------------------------------
# SSL/TLS Configuration (Required for secure Corelation connections)
#-------------------------------------------------------------------------------------
# UseSSL: Set to 'true' to enable SSL/TLS, 'false' for plain TCP (default: false)
UseSSL=false

# TLSVersion: Specify TLS protocol version
#   TLS    = TLS 1.0 only
#   TLS11  = TLS 1.1 only
#   TLS12  = TLS 1.2 only (recommended)
#   N/A    = Auto-negotiate (SSL3, TLS 1.0, 1.1, 1.2)
TLSVersion=N/A

# CorelationServerName: Server name for certificate validation (required when UseSSL=true)
# Must match the certificate name presented by the Corelation server
CorelationServerName=N/A
```

## Security best practices

### SSL/TLS for Corelation connections

When connecting to Corelation servers, consider the following security recommendations:

#### When to enable SSL/TLS

- **Production environments**: Always enable SSL/TLS (`UseSSL=true`) when connecting to production Corelation servers to protect sensitive financial data during transmission.
- **Development/testing**: SSL/TLS may be optional in isolated development environments, but is still recommended as a best practice.
- **Compliance requirements**: Many regulatory frameworks require encryption of data in transit. Verify your organization's security policies.

#### TLS version selection

- **Recommended**: Use `TLS12` for the highest level of security currently supported.
- **Auto-negotiate (`N/A`)**: Use only if you need compatibility with older Corelation servers or are unsure which TLS version is supported. This mode supports SSL 3.0, TLS 1.0, TLS 1.1, and TLS 1.2.
- **Legacy versions**: TLS 1.0 and TLS 1.1 are considered deprecated and should only be used if required by older Corelation installations.

:::warning
SSL 3.0 has known security vulnerabilities and should only be used if absolutely necessary for compatibility with legacy systems.
:::

#### Certificate validation

- Ensure the `CorelationServerName` parameter matches the Common Name (CN) or Subject Alternative Name (SAN) in the Corelation server's SSL certificate.
- Certificate validation failures will cause connection errors. Work with your Corelation administrator to obtain the correct server name.
- Self-signed certificates are supported but should be properly configured.

#### Backward compatibility

- Setting `UseSSL=false` maintains full backward compatibility with previous versions of SMADeleteKeyStoneFiles.
- Existing configuration files without SSL parameters will continue to work using plain TCP connections.
- No changes are required to existing configurations unless you want to enable SSL/TLS.

### Password security

- Use encrypted password files instead of plain text passwords in configuration files.
- Create encrypted password files using the [SMACreatePassword](create-password-file) utility.
- Store password files with restricted file system permissions.
- Use separate encrypted files for SSH passwords and Corelation web service passwords.

## Reference

### Supported SSH encryption algorithms

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
|aes256-gcm@openssh.com|256-bit AES-GCM encryption|
|aes128-gcm@openssh.com|128-bit AES-GCM encryption|
|chacha20-poly1305@openssh.com|ChaCha20-Poly1305 authenticated encryption|

### Supported key exchange algorithms

curve25519-sha256, curve25519-sha256@libssh.org, diffie-hellman-group14-sha1, diffie-hellman-group1-sha1, diffie-hellman-group14-sha256, diffie-hellman-group-exchange-sha256, diffie-hellman-group-exchange-sha1, ecdh-sha2-nistp256, diffie-hellman-group16-sha512, diffie-hellman-group18-sha512, ecdh-sha2-nistp384, ecdh-sha2-nistp521

### Supported MAC algorithms

hmac-sha2-256, hmac-sha2-512, hmac-sha1, hmac-md5, hmac-ripemd160, hmac-sha1-96, hmac-md5-96, hmac-sha2-256-96, hmac-sha2-512-96, hmac-ripemd160-96, hmac-sha2-256-etm@openssh.com, hmac-sha2-512-etm@openssh.com

### Supported public key algorithms

ssh-ed25519, ecdsa-sha2-nistp256, ecdsa-sha2-nistp384, ecdsa-sha2-nistp521, ssh-rsa, ssh-dss, x509v3-sign-rsa, x509v3-sign-dss

### Supported SSL/TLS protocols

The following TLS protocols are supported for secure Corelation web service connections (when `UseSSL=true`):

|TLSVersion Value|Protocol(s) Used|Security Level|
|---|---|---|
|TLS12|TLS 1.2 only|**Recommended** - Highest security|
|TLS11|TLS 1.1 only|Acceptable for legacy systems|
|TLS|TLS 1.0 only|Deprecated - use only if required|
|N/A|Auto-negotiate: SSL 3.0, TLS 1.0, TLS 1.1, TLS 1.2|Use for compatibility when TLS version is unknown|

:::note
The SSL/TLS support applies only to the Corelation web service connection (KeyStone Connection Parameters). The SSH connection uses its own encryption as specified by the SSH encryption algorithms.
:::