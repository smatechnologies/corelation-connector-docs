---
title: Corelation Connector
description: "Overview of the Corelation Connector, which enables KeyStone jobs to be run from OpCon."
sidebar_label: Overview
tags:
  - Conceptual
  - Automation Engineer
  - Corelation Connector
---

# Corelation Connector

## What is it?

The Corelation Connector enables KeyStone jobs to be run from OpCon. A job can be specified by its name or its serial number and is monitored until completion.

- Use this connector when you need to trigger and track KeyStone batch jobs from an OpCon schedule.
- Use this connector to integrate Corelation core banking operations into a broader automated workflow.
- Use this connector to manage KeyStone database administration tasks — backups, restores, and file deletions — from a single scheduling platform.

The connector interface consists of the following command-line applications:

| Application | Purpose |
|---|---|
| [SMARunCorelationJob](./run-corelation-job.md) | Starts and monitors a KeyStone batch job |
| [SMAExecuteKeystoneCommand](./execute-keystone-command.md) | Runs KeyStone administrative commands over SSH |
| [SMACreateCorelationPasswordFile](./create-password-file.md) | Creates an encrypted password file for use in configuration files |
| [SMASumItems](./sum-items.md) | Sums numeric fields in an XML file and stores the result as an OpCon property |

## FAQs

**What is KeyStone?**
KeyStone is the core banking platform provided by Corelation. The Corelation Connector communicates with KeyStone using its XML API over TCP/IP or SSH.

**Does the connector support encrypted connections?**
Yes. `SMARunCorelationJob` supports SSL/TLS connections to the Corelation server. Set `UseSSL=true` and specify a `TLSVersion` in the configuration file to enable encryption. `SMAExecuteKeystoneCommand` uses SSH for all communication, which is encrypted by default.

**Can I run a job by serial number instead of name?**
Yes. `SMARunCorelationJob` accepts either `-jobname` or `-jobserial` to identify the job to run.

**What happens if the connector cannot reach the Corelation server?**
`SMARunCorelationJob` retries the connection up to `MaximumNumberOfRetries` times, waiting `MillisecondsBetweenRetries` between each attempt. Both values are set in the configuration file.

**How do I avoid storing plain-text passwords in configuration files?**
Use `SMACreateCorelationPasswordFile` to create an encrypted password file, then reference that file path in the `CorelationPassword` or `Password` field of any connector configuration file.

**What version of Solution Manager is required to use the leastbusy batch queue option?**
Solution Manager 25.0 or later is required to see and use the **-BatchQueueName leastbusy** checkbox in the Corelation Sub-Type configuration. Corelation Connector version 22.4.0 or later is required to use the option from the command line.

## Glossary

**KeyStone** — The core banking software platform developed by Corelation, Inc. The Corelation Connector communicates with KeyStone using its XML-based API.

**Batch queue** — A KeyStone construct that manages the order and execution of batch jobs. The Corelation Connector can target a named queue or use the `leastbusy` option to automatically select the queue with the fewest pending jobs.

**Batch server** — The KeyStone server component that processes batch jobs. The connector references the batch server by name in the configuration or command line.

**MSGIN** — The OpCon message input directory. `SMASumItems` writes property values to OpCon by placing event messages in this directory.

**SSH** — Secure Shell, a cryptographic network protocol used by `SMAExecuteKeystoneCommand` to connect to the remote KeyStone server.

**SSL/TLS** — Secure Sockets Layer / Transport Layer Security, the encryption protocol used by `SMARunCorelationJob` to secure TCP connections to the Corelation server.

**OpCon property** — A named variable in OpCon that can be set and read by jobs and schedules. `SMASumItems` stores its computed total in an OpCon property for use by downstream jobs.
