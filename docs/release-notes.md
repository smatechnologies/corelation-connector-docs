---
sidebar_label: 'Release notes'
---

# Corelation Connector release notes

## 22.2.0

#### SMARunCorelationJob

- **CONNUTIL-583**: Added config entry to specify TLS version number (TLS12,TLS11, TLS, or N/A).

#### SMAExecuteKeystoneCommand

- **CONNUTIL-579**: Added logic to avoid 'missing elements' error when Corelation software changes them between releases by specifying them in the connector config.

## 21.0.0

#### SMARunCorelationJob

- **CONNUTIL-518**: Added support to configure key exchange algorithm, Mac algorithm, and public key algorithm.  Upgraded nSoftware SSH to Version 20.

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