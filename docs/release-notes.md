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

### 26.0.2

2026

### Bug fixes

:white_check_mark: **CON-483**: Fixed intermittent `SMARunCorelationJob` failures on slow or unreliable networks, where the Corelation core completed a job but the connector could not return the result. The connector now applies a configurable read timeout (`ReceiveTimeoutMilliseconds`) so a silent or slow core is retried instead of the job hanging, and retries the batch server and queue reads with a bounded reconnect and resend (`ReceiveRetryMaxDelayMilliseconds` caps the backoff; `MaximumNumberOfRetries` bounds the attempts). `SUBMIT` is deliberately never retried, because resending could double-submit a batch, so it now fails cleanly on a lost response instead of crashing on an empty one. The read timeout is opt-in: the default of `0` preserves existing behavior, and the sample configuration recommends `60000`.

:white_check_mark: **CON-1749**: Fixed message truncation in `SMARunCorelationJob` that affected messages containing non-ASCII characters. The connector now sizes the outbound buffer by the UTF-8 byte count instead of the UTF-16 character count, so these messages are transmitted without truncation. This fix is always in effect and requires no configuration.

### 26.0.1

2026

### Bug fixes

:white_check_mark: **CON-1542**: Fixed a timeout failure in `SMADeleteKeyStoneFiles` that occurred when the KeyStone export directory contained a large number of files (~25,000+). The SSH listing now uses a targeted glob to retrieve only matching files instead of the entire directory. Added automatic fallback if the glob exceeds the shell's argument limit. Additionally, matched files are now deleted from the filesystem via SSH after their Corelation DB records are removed, using batched commands with per-batch logging.

### 26.0.0

2025

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

---

## 20

### 20.0.1

### What's new

:eight_spoked_asterisk: **ALCONN-353**: Added support for the `optionsName` parameter in `SMARunCorelationJob`.

### 20.0.0

### What's new

:eight_spoked_asterisk: **ALCONN-351**: Updated the nSoftware SSH library to version 16 in `SMARunCorelationJob`.

---

## 19

### 19.0.0

### What's new

:eight_spoked_asterisk: Added DNS lookup support to `SMARunCorelationJob` when the `CorelationIPAddress` value does not appear to be an IP address.
