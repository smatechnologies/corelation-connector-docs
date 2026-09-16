---
title: SMADeleteKeyStoneFiles
description: "Remove KeyStone export files and their Corelation database records, using a filename mask to select which files to process."
sidebar_label: 'SMADeleteKeyStoneFiles'
tags:
  - Reference
  - System Administrator
  - Corelation Connector
---

# SMADeleteKeyStoneFiles

## What is it?

`SMADeleteKeyStoneFiles.exe` removes KeyStone export files that match a filename mask. For each matching file it removes the file's record from the Corelation database and then deletes the file itself from the export directory over SSH.

- Use this program to keep a KeyStone export directory from growing without limit.
- Use this program when export files and their database records need to be removed together, so that neither is left orphaned.

The program connects twice: over SSH to list and delete files on the KeyStone host, and to the Corelation KeyStone service to remove the database records. Both connections are configured in its configuration file.

## Usage

```
SMADeleteKeyStoneFiles.exe -FilenameMask mask [-ConfigFile file] [-OnlyListFiles] [-DumpXML] [-Debug]
```

## Command-line options

| Option | Required | Description |
|---|---|---|
| `-FilenameMask` | Yes | The mask selecting which files to process, using DOS-style wildcards (for example `*.txt`). Only files matching the mask are listed, matched against the database, and deleted. |
| `-ConfigFile` | No | Path and name of the configuration file to use. If not specified, the program reads `SMADeleteKeyStoneFiles.ini` from its own directory. |
| `-OnlyListFiles` | No | Lists the files that match the mask without removing any database records or deleting any files. Use this to confirm the mask selects what you expect before running the program for real. No value is required. |
| `-DumpXML` | No | Writes the XML exchanged with the Corelation service to the log, for diagnosis. No value is required. |
| `-Debug` | No | Writes additional diagnostic detail to the log. No value is required. |

:::tip
Run with `-OnlyListFiles` first. The mask is applied to a live export directory, and the program deletes both database records and files, so confirming the selection first is worth the extra run.
:::

## Configuration file

The configuration file is named `SMADeleteKeyStoneFiles.ini` and is read from the program's own directory unless `-ConfigFile` says otherwise. It has two sections.

### SSH Connection Parameters

These settings govern the SSH connection to the KeyStone host, used to list and delete the files.

| Parameter | Description |
|---|---|
| `HostName` | The DNS resolvable name or IP address of the KeyStone host. |
| `Port` | The port the SSH daemon on that host is listening on. |
| `UserName` | The user to log in as. |
| `Password` | The password for that user, or the path to an encrypted password file. See [SMACreateCorelationPasswordFile](./create-password-file.md). |
| `SSHEncryptionAlgorithms` | Comma-separated list of encryption algorithms that may be negotiated. |
| `SSHKeyExchangeAlgorithms` | Comma-separated list of key exchange algorithms that may be negotiated. |
| `SSHPublicKeyAlgorithms` | Comma-separated list of public key algorithms that may be negotiated. |
| `SSHMacAlgorithms` | Comma-separated list of message authentication algorithms. **Not currently applied** — see the caution below. |
| `SystemPrompt` | A regular expression matching the host's shell prompt, used to detect when the session is ready for input. A literal dollar sign must be escaped as `\$`. |
| `LoginTimeoutSeconds` | Seconds to allow the host to respond to the login request. |
| `CommandExecutionTimeoutSeconds` | Seconds to allow a single command to complete. |
| `OutputRetrievalSeconds` | Seconds to wait while collecting command output. |
| `KeyStoneReportDirectory` | The export directory on the KeyStone host that the mask is applied to. |

:::caution
`SSHMacAlgorithms` is **not currently applied**. A value set here has no effect on the algorithms the connection negotiates, and no error is reported. Do not rely on it to restrict message authentication algorithms. Contact Continuous if you need this constrained.
:::

### KeyStone Connection Parameters

These settings govern the connection to the Corelation KeyStone service, used to remove the database records.

| Parameter | Description |
|---|---|
| `CorelationIPAddress` | The address of the Corelation server. |
| `CorelationPort` | The port the Corelation service is listening on. |
| `CorelationUser` | The Corelation user to connect as. |
| `CorelationPassword` | The password for that user, or the path to an encrypted password file. |
| `CorelationDeviceName` | The device name to present to Corelation. |
| `CorelationNameSpace` | The XML namespace used by the Corelation query interface. This should not be changed unless directed to by Continuous. |
| `UseSSL` | `true` to connect over SSL/TLS, `false` for plain TCP. |
| `TLSVersion` | The TLS protocol version to use when `UseSSL=true`. Accepted values: `TLS12` (recommended), `TLS11`, `TLS`, or `N/A` to negotiate automatically. |
| `CorelationServerName` | The server name used for certificate validation. Required when `UseSSL=true`, and must match the name presented by the Corelation server. |

## How files are selected and removed

1. The mask is applied to the directory named by `KeyStoneReportDirectory`, and only matching files are listed.
2. Each listed file is matched against the Corelation database.
3. Records for matched files are removed from the database.
4. The matched files are then deleted from the filesystem, in batches.

Files that match the mask but have no corresponding database record are left untouched. The log records the count at each stage, so a run can be reconciled afterwards.

## FAQs

**What happens to files that match the mask but are not in the database?**

They are left in place. Only files confirmed in the database are deleted, so a mask that is broader than intended does not remove unrelated files — but it is still worth confirming the selection with `-OnlyListFiles` first.

**Can I use the same encrypted password file for the SSH and Corelation connections?**

Only if both use the same password. Otherwise create a separate file for each and reference them in the corresponding section.

**Where does the program write its log?**

To its own directory, alongside the program.

**Related topics:**

- [Overview](./overview.md)
- [Installation](./installation.md)
- [SMACreateCorelationPasswordFile](./create-password-file.md)
