---
title: SMARunCorelationJob
description: "Reference documentation for SMARunCorelationJob, which starts and monitors a KeyStone batch job from OpCon."
sidebar_label: SMARunCorelationJob
tags:
  - Reference
  - Automation Engineer
  - Corelation Connector
---

# SMARunCorelationJob

## What is it?

`SMARunCorelationJob.exe` starts and monitors a batch job defined in the Corelation KeyStone database. When the job completes without exceptions, the application exits with a value of `0`.

- Use this application when you need to trigger a KeyStone batch job from an OpCon schedule.
- Use this application to monitor job completion and capture the exit status for downstream dependencies.
- Use this application with the `leastbusy` batch queue option to distribute load across available queues automatically.

## Usage

```
SMARunCorelationJob.exe -jobname myJob
```

## Command-line options

| Option | Required | Description |
|---|---|---|
| `-jobname` | One of `-jobname` or `-jobserial` | The name of the KeyStone job to start. |
| `-jobserial` | One of `-jobname` or `-jobserial` | The serial number of the KeyStone job to start. |
| `-configuration` | No | The path to a configuration file. If not specified, `SMARunCorelationJob.ini` in the same directory is used. |
| `-batchservername` | No | The name of the batch server. Default: `Batch Server`. |
| `-batchqueuename` | No | The name of the batch queue. Default: `Main Batch Queue`. Accepts `leastbusy` to automatically select the queue with the fewest pending jobs. |
| `-dumpxml` | No | Includes XML messages exchanged with the Corelation server in the job output. |
| `-dumpjobdetails` | No | Displays job details and exits. Use with `-dumpxml` to see all job parameters. |
| `-propertyowner` | No | Nests batch options under the specified property name in the generated XML. |
| `-showjoblist` | No | Lists all batch jobs defined in the Corelation database. |
| `-showviewsubmit` | No | Displays the response to the `VIEW_SUBMIT` call. The response to the actual submit is always shown when `-dumpxml` is active. |
| `-arrayofparameters` | No | Specifies an array name and its values as `ArrayName\|Value1\|Value2`. |
| `-batchoptions` | No | Specifies one or more property name/value pairs separated by `\|`. |
| `-param1` through `-param99` | No | Specifies nested parameters. Format: `tag\|value` or `parent\|child\|value` for nested structures. |
| `-debug` | No | Enables full debug output, including all XML exchanges and job status queries. |

### leastbusy batch queue option

When `-batchqueuename leastbusy` is specified, the connector queries the Corelation API for all open batch queues and enqueues the job on the queue with the fewest pending jobs. If multiple queues are equally empty, the first one returned by the API is selected.

:::note
- The Corelation Sub-Type configuration in Solution Manager includes a checkbox to enable this option. The checkbox requires Solution Manager 25.0 or later.
- Corelation Connector version 22.4.0 or later is required to use `leastbusy` from the command line.
- Version 22.4.3 includes a fix ensuring only open queues are considered.
:::

### Specifying parameters with -batchoptions

The `-batchoptions` string uses `|` to separate name/value pairs:

```
-batchoptions="IMPORT_FILE_NAME|mytestfile.txt|Prop2|Value2"
```

Use `-propertyowner` to nest the options under a parent property:

```
-propertyowner="InterfaceOptions" -batchoptions="IMPORT_FILE_NAME|mytestfile.txt"
```

### Specifying parameters with -arrayofparameters

```
-arrayofparameters="ArrayName|Value1|Value2"
```

### Specifying nested parameters with -param1 through -param99

```
-param1="alpha|beta|gamma|value"
```

## Configuration file

The configuration file uses INI format. By default, `SMARunCorelationJob.exe` reads `SMARunCorelationJob.ini` from the same directory as the executable.

### Configuration parameters

| Parameter | Description |
|---|---|
| `CorelationIPAddress` | The DNS-resolvable hostname or IP address of the Corelation server. |
| `CorelationPort` | The port number for communication with the Corelation server. |
| `UseSSL` | Set to `true` to enable SSL/TLS encryption. Default: `false`. |
| `CorelationServerName` | The server name for certificate validation. Required when `UseSSL=true`. |
| `CorelationUser` | The username for authenticating to the Corelation server. |
| `CorelationPassword` | The password for the Corelation user, or the path to an encrypted password file. See [SMACreateCorelationPasswordFile](./create-password-file.md). |
| `CorelationDeviceName` | The device name associated with the Corelation server. |
| `CorelationNameSpace` | The XML namespace used by Corelation. Do not change this value unless directed by SMA. |
| `MillisecondsBetweenRetries` | The time in milliseconds to wait between connection attempts. Default: `60000` (one minute). |
| `MaximumNumberOfRetries` | The maximum number of connection retry attempts. Default: `30`. |

### Sample configuration file

```ini
#======================================================================
# SMARunCorelationJob configuration
#======================================================================
[ConnectionDetails]
CorelationIPAddress=192.168.1.1
CorelationPort=52007
UseSSL=false
CorelationServerName=N/A
CorelationUser=youruser
CorelationPassword=yourpassword
CorelationDeviceName=system
CorelationNameSpace=http://www.corelationinc.com/queryLanguage/v1.0
MillisecondsBetweenRetries=10000
MaximumNumberOfRetries=10
```

## FAQs

**Can I run a job by its serial number instead of its name?**
Yes. Use `-jobserial` in place of `-jobname`. One of these two parameters is required.

**What happens if the connection to the Corelation server fails?**
The connector retries the connection up to `MaximumNumberOfRetries` times, waiting `MillisecondsBetweenRetries` milliseconds between each attempt. Both values are set in the configuration file.

**How do I avoid storing passwords in plain text?**
Use [SMACreateCorelationPasswordFile](./create-password-file.md) to create an encrypted password file, then set `CorelationPassword` to the path of that file.

**How does leastbusy select a queue?**
The connector calls the Corelation API to retrieve all open batch queues, then selects the one with the fewest pending jobs. If multiple queues have the same count, the first queue returned by the API is used.

**What does exit code 0 mean?**
The job completed on the Corelation server without exceptions.

## Glossary

**Batch queue** — A KeyStone construct that holds pending batch jobs for processing by the batch server.

**Batch server** — The KeyStone component that processes batch jobs from the batch queue.

**leastbusy** — A special value for `-batchqueuename` that instructs the connector to query all open queues and select the one with the fewest jobs.

**CorelationNameSpace** — The XML namespace identifier used in all API requests to the Corelation server. This value is provided by Corelation and should not be changed.
