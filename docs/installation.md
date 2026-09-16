---
title: Corelation Connector installation
description: "Obtain and extract the Corelation Connector programs on a Windows machine that can reach the Corelation KeyStone system."
sidebar_label: 'Installation'
tags:
  - Procedural
  - System Administrator
  - Installation
---

# Corelation Connector installation

## What is it?

The Corelation Connector is distributed as a single archive containing a set of command-line programs. There is no installer: extract the archive to the directory where the programs will run, then configure each program you intend to use.

Complete this step before any of the configuration described on the program pages.

## Install the programs

To install the Corelation Connector, complete the following steps:

1. Obtain the `SMACorelationConnector.zip` archive.
2. Extract the archive to the directory where the programs will run, on a Windows machine that can reach the Corelation KeyStone system.
3. Confirm that the extracted directory contains the programs listed below.

## What the archive contains

| Item | Purpose |
|---|---|
| `SMARunCorelationJob.exe` | Starts and monitors a KeyStone batch job. See [SMARunCorelationJob](./run-corelation-job.md). |
| `SMAExecuteKeystoneCommand.exe` | Runs KeyStone administrative commands over SSH. See [SMAExecuteKeystoneCommand](./execute-keystone-command.md). |
| `SMADeleteKeyStoneFiles.exe` | Removes KeyStone export files and their database records. See [SMADeleteKeyStoneFiles](./delete-keystone-files.md). |
| `SMASumItems.exe` | Sums numeric fields in an XML file and stores the total as an OpCon property. See [SMASumItems](./sum-items.md). |
| `SMACreateCorelationPasswordFile.exe` | Creates an encrypted password file for use in configuration files. See [SMACreateCorelationPasswordFile](./create-password-file.md). |
| `SMATechnicalServicesUtilities.dll` | Shared library used by the programs. Keep it alongside them. |

Each program reads its own configuration file from the directory it runs in, named after the program with an `.ini` extension. The program pages describe the settings each one accepts.

## FAQs

**Do I need to install every program?**

No. The programs are independent and share only the support library. Install the archive once and configure only the programs you intend to use.

**Where do the configuration files go?**

In the same directory as the program, unless a program documents a way to point elsewhere. `SMADeleteKeyStoneFiles` accepts a `-ConfigFile` argument for this.

**Does the archive contain anything else?**

The archive may contain additional programs used by Continuous for support and diagnosis. Those are not intended for customer use and are not documented here.

**Related topics:**

- [Overview](./overview.md)
- [Release notes](./release-notes.md)
