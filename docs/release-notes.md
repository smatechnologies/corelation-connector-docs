---
sidebar_label: "Release notes"
---

# Corelation Connector release notes

## 22.3.0

#### SMARunCorelationJob

- **CONNUTIL-609**: Existing implementation of Corelation connector required the user to configure the exact encryption level (SSL3.0, TLS 1.0, TLS1.1 or TLS1.2) being used by the server in the ini file used in the job. That is difficult to know for the implementation user and made it difficult to get the corelation job working without trying the possible combinations. Changed connector logic to use secure protocol level offered by server if not specifically configured (SSL3.0, TLS1.0-1.2) in the connector ini. Now the user can just leave the TLSVersion=N/A in the ini file (which is default) and it will automatically connect using the encryption level offered by server.

In case of SSL errors, the error message didn’t describe in detail if there was a problem with the certificate expiry date or hostname or revocation or something else that caused the connection to fail. Added more detailed logging for connection error information to help users figure out what the problem might be.

## 22.2.0

#### SMARunCorelationJob

- **CONNUTIL-583**: Added config entry to specify TLS version number (TLS12,TLS11, TLS, or N/A).

#### SMAExecuteKeystoneCommand

- **CONNUTIL-579**: Added logic to avoid 'missing elements' error when Corelation software changes them between releases by specifying them in the connector config.

## 21.0.0

#### SMARunCorelationJob

- **CONNUTIL-518**: Added support to configure key exchange algorithm, Mac algorithm, and public key algorithm. Upgraded nSoftware SSH to Version 20.

#### SMADeleteKeyStoneFiles

- **CONNUTIL-518**: Upgraded nSoftware SSH to Version 20 to add new algorithms.

## 20.0.1

#### SMARunCorelationJob

- **ALCONN-353**: Added support for optionsName.

## 20.0.0

#### SMARunCorelationJob

- **ALCONN-351**: Upgraded nSoftware SSH to Version 16.

#### SMADeleteKeyStoneFiles

- **ALCONN-352**: Upgraded nSoftware SSH to Version 16.

## 19.0.0

#### SMARunCorelationJob

- Added a DNS lookup if the CorelationIPAddress did not look like an IP Address.
