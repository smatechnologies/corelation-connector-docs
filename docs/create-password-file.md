---
title: SMACreateCorelationPasswordFile
description: "Reference documentation for SMACreateCorelationPasswordFile, which creates an encrypted password file for use in Corelation Connector configuration files."
sidebar_label: SMACreateCorelationPasswordFile
tags:
  - Reference
  - Automation Engineer
  - Corelation Connector
---

# SMACreateCorelationPasswordFile

## What is it?

`SMACreateCorelationPasswordFile.exe` encrypts a plain-text password and stores it in a file. Other Corelation Connector applications can reference the encrypted file path in their configuration files instead of storing passwords in plain text.

- Use this application to secure Corelation server credentials stored in connector configuration files.
- Use this application to create separate encrypted files for SSH passwords and KeyStone web service passwords.

## Usage

```
SMACreateCorelationPasswordFile.exe -file file -password password
```

## Command-line options

| Option | Required | Description |
|---|---|---|
| `-file` | Yes | The path and name of the encrypted password file to create. |
| `-password` | Yes | The plain-text password to encrypt. |

## FAQs

**Where do I reference the encrypted password file in a connector configuration?**
Set the `CorelationPassword` or `Password` field in any Corelation Connector configuration file to the path of the encrypted file. For example: `CorelationPassword=.\mypassword.dat`.

**Can I use the same encrypted file for both SSH and KeyStone web service connections?**
Only if both connections use the same password. Otherwise, create separate encrypted files for each credential and reference them in the corresponding configuration sections.

**Is the encrypted file portable between machines?**
Yes. The same encrypted file can be used on any machine running the connector, so you do not need to create one per server.

:::caution
Because the file is portable, it must be protected like the password itself. Anyone who obtains a copy of the file and has the connector software can recover the password from it. Restrict the file using file system permissions, and do not include it in backups, shares or tickets that a wider audience can read.
:::

## Glossary

**Encrypted password file** — A file produced by `SMACreateCorelationPasswordFile` that stores a password in encrypted form rather than as plain text. Connector applications decrypt it at run time. The file is portable between machines and must be protected by file system permissions.

**CorelationPassword** — The configuration parameter in connector INI files that accepts either a plain-text password or the path to an encrypted password file.
