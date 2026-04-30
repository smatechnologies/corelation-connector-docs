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
The encryption is tied to the machine where the file was created. Create the encrypted file on the machine where the connector runs.

## Glossary

**Encrypted password file** — A file produced by `SMACreateCorelationPasswordFile` that stores a password in encrypted form. Connector applications decrypt the file at runtime using the same machine-specific key.

**CorelationPassword** — The configuration parameter in connector INI files that accepts either a plain-text password or the path to an encrypted password file.
