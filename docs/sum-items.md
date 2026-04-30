---
title: SMASumItems
description: "Reference documentation for SMASumItems, which sums numeric fields in an XML file and stores the result as an OpCon property."
sidebar_label: SMASumItems
tags:
  - Reference
  - Automation Engineer
  - Corelation Connector
---

# SMASumItems

## What is it?

`SMASumItems.exe` reads an XML file, sums all values under a specified tag, and stores the result as an OpCon property. The property can then be used by downstream jobs in the same schedule.

- Use this application to aggregate monetary amounts from a KeyStone posting journal and pass the total to a downstream validation job.
- Use this application to compute totals from any XML output file where a numeric field repeats across multiple records.

## Usage

```
SMASumItems.exe -xmlfilename xmlfilename -OwnerTag "tag" -AmountTag "tag" -PropertyName property
```

## Command-line options

| Option | Required | Description |
|---|---|---|
| `-XMLFileName` | Yes | The path to the XML file containing the data to parse. |
| `-OwnerTag` | Yes | The XML element name of the parent record that contains the fields to sum. |
| `-AmountTag` | Yes | The XML element name of the numeric field to sum. |
| `-PropertyName` | Yes | The name of the OpCon property in which to store the computed total. |

## Configuration file

The configuration file is named `SMASumItems.ini` and must be stored in the same directory as the application.

### Configuration parameters

| Parameter | Description |
|---|---|
| `MSGINPath` | The path to an OpCon MSGIN directory for submitting property events. |
| `MSGINUser` | An OpCon user with permission to run `$PROPERTY:ADD` events. |
| `MSGINPassword` | The external token for the specified `MSGINUser`. |

## Example

### Sample XML file

The following example shows a posting journal with four steps, each containing an `amount` field:

```xml
<postingJournal type="c">
  <prologue type="c">
  …
  <step type="a">
   <postingRequest type="c">
    …
    <amount>400.00</amount>
    …
   </postingRequest>
  </step>
  …
  <step type="a">
   <postingRequest type="c">
    …
    <amount>.34</amount>
    …
   </postingRequest>
  </step>
  …
  <step type="a">
   <postingRequest type="c">
    …
    <amount>200.00</amount>
    …
   </postingRequest>
  </step>
  …
  <step type="a">
   <postingRequest type="c">
    …
    <amount>225.00</amount>
    …
   </postingRequest>
  </step>
</postingJournal>
```

### Sample command line

```
SMASumItems.exe -xmlfilename 20120919_062026_000.ACH_Posting.xml -OwnerTag "postingRequest" -AmountTag "amount" -PropertyName myprop
```

### Sample job output

```console
Copyright Software Management and Associates - 2012
Version 1.0.0.0

-XMLFilename                  : 20120919_065009_000.WRG_ACH_Posting.xml
-OwnerTag                     : postingRequest
-AmountTag                    : amount
-PropertyName                 : myprop
Configuration
-------------
MSGINPath                     : .\
MSGINUser                     : ocadm
MSGINPassword                 : ********************

amount                        :          400.00
amount                        :            0.34
amount                        :          200.00
amount                        :          225.00

GrandTotal                    :          825.34
Finished
```

## FAQs

**What XML structure does SMASumItems expect?**
The application searches for all occurrences of the element specified by `-AmountTag` that are children of the element specified by `-OwnerTag`. It sums every numeric value found and writes the total to the OpCon property named by `-PropertyName`.

**What permissions does the OpCon user need?**
The user specified in `MSGINUser` must have permission to submit `$PROPERTY:ADD` events through the MSGIN directory.

**Can I sum fields that are not monetary amounts?**
Yes. The application sums any numeric value in the specified tag, regardless of the data type. Ensure the values in the XML file are numeric strings.

**Where is the result stored?**
The computed total is written to an OpCon global property named by the `-PropertyName` argument. Downstream jobs in the schedule can reference the property using standard OpCon property syntax.

## Glossary

**MSGIN** — The OpCon message input directory. `SMASumItems` submits `$PROPERTY:ADD` events through this directory to store the computed total as an OpCon property.

**OpCon property** — A named variable in OpCon that stores a value accessible to jobs and schedules. `SMASumItems` creates or updates a property with the summed total.

**OwnerTag** — The XML element name of the parent record. Only `AmountTag` values that are children of this element are included in the sum.

**AmountTag** — The XML element name of the numeric field to sum across all matching parent records.
