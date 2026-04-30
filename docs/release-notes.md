---
sidebar_label: Release notes
title: Corelation Connector release notes
description: "Version history and change details for the Corelation Connector, including new features, improvements, and bug fixes."
tags:
  - Reference
  - Automation Engineer
  - Corelation Connector
---

# Corelation Connector release notes

## 26

### 26.0.0

2025

### What's new

:eight_spoked_asterisk: **CON-815**: Added SSL/TLS support for secure connections to the Corelation server in `SMADeleteKeyStoneFiles`.

New configuration parameters:

- `UseSSL` — Enable or disable SSL/TLS encryption. Default: `false`.
- `TLSVersion` — Specify the TLS protocol version: `TLS`, `TLS11`, `TLS12`, or `N/A` (auto-negotiate).
- `CorelationServerName` — Server name for certificate validation. Required when `UseSSL=true`.

Supported protocols: TLS 1.0, TLS 1.1, TLS 1.2, and auto-negotiate (SSL 3.0 through TLS 1.2). Setting `UseSSL=false` maintains backward-compatible plain TCP behavior.

### Why this matters

SSL/TLS support for `SMADeleteKeyStoneFiles` brings feature parity with `SMARunCorelationJob` and allows organizations to encrypt all Corelation connector traffic to meet security and compliance requirements.

### Bug fixes

:white_check_mark: **CON-807**: Added a shared mutex to `SMARunCorelationJob` so that concurrent instances take turns inspecting the batch queue and assigning work when using the `leastbusy` option.

---

## 22

### 22.4.3

### Bug fixes

:white_check_mark: **INTPLT-367**: The `leastbusy` batch queue option now correctly filters to only open queues when selecting the least-busy queue. Previous versions could consider closed queues.

### 22.4.0

### What's new

:eight_spoked_asterisk: **INTPLT-367**: Added `leastbusy` as an option for the `-BatchQueueName` parameter in `SMARunCorelationJob`. When specified, the connector queries the Corelation API for all open batch queues and enqueues the new job on the queue with the fewest jobs. The Corelation Sub-Type configuration in Solution Manager now includes a checkbox to enable this option.

### Why this matters

The `leastbusy` option distributes batch job load across available queues automatically, reducing contention during peak processing periods without requiring manual queue assignment.

### Bug fixes

:white_check_mark: **CONNUTIL-631**: Fixed a parsing error in `SMARunCorelationJob` that occurred when the KeyStone XML response used Unix-style line feed characters instead of Windows-style carriage return and line feed.

### 22.3.0

### What's new

:eight_spoked_asterisk: **CONNUTIL-609**: Changed `SMARunCorelationJob` to automatically negotiate the TLS protocol version offered by the server when `TLSVersion=N/A` is set in the configuration file. Previously, you had to specify the exact TLS version, which required trial and error to determine.

:eight_spoked_asterisk: **CONNUTIL-609**: Improved SSL error logging in `SMARunCorelationJob` to include specific details about certificate failures — such as expiry date, hostname mismatch, or revocation errors — to help identify connection problems faster.

### 22.2.0

### What's new

:eight_spoked_asterisk: **CONNUTIL-583**: Added a `TLSVersion` configuration option to `SMARunCorelationJob`, allowing you to specify the TLS protocol version (`TLS12`, `TLS11`, `TLS`, or `N/A`).

:eight_spoked_asterisk: **CONNUTIL-579**: Added logic to `SMAExecuteKeystoneCommand` to handle XML element changes between Corelation software releases by specifying optional elements in the connector configuration, preventing `missing elements` errors after a Corelation upgrade.

---

## 21

### 21.0.0

### What's new

:eight_spoked_asterisk: **CONNUTIL-518**: Added support in `SMARunCorelationJob` for configuring the key exchange algorithm, MAC algorithm, and public key algorithm. Updated the nSoftware SSH library to version 20.

:eight_spoked_asterisk: **CONNUTIL-518**: Updated the nSoftware SSH library to version 20 in `SMADeleteKeyStoneFiles` to support the same expanded algorithm set.

---

## 20

### 20.0.1

### What's new

:eight_spoked_asterisk: **ALCONN-353**: Added support for the `optionsName` parameter in `SMARunCorelationJob`.

### 20.0.0

### What's new

:eight_spoked_asterisk: **ALCONN-351**: Updated the nSoftware SSH library to version 16 in `SMARunCorelationJob`.

:eight_spoked_asterisk: **ALCONN-352**: Updated the nSoftware SSH library to version 16 in `SMADeleteKeyStoneFiles`.

---

## 19

### 19.0.0

### What's new

:eight_spoked_asterisk: Added DNS lookup support to `SMARunCorelationJob` when the `CorelationIPAddress` value does not appear to be an IP address.
