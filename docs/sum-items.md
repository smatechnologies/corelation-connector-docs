# SMASumItems

This application sums all of the fields with a specified XML “tag”.  To further qualify the field, the user must supply the name tag of the owner record.  The sum is sent to OpCon via MSGIN to be stored as a property for use in other jobs.

## Usage

### Command line

`SMASumItems.exe  -xmlfilename xmlfilename -OwnerTag “tag”  -AmountTag "tag" -PropertyName property`

### Command line options

**-XMLFileName**: The name of the file containing the data to be parsed.

**-OwnerTag**: The name of the record that “owns” the fields that should be summed.

**-AmountTag**: The name of the field to be summed.

**-PropertyName**: The name of the OpCon property in which the sum should be stored.

## Configuration file

The configuration file is stored in the same directory as the application and is named `SMASumItems.ini`.

### Configuration file parameters

**MSGINPath**: The path to a MSGIN directory for OpCon events.

**MSGINUser**: An OpCon user with permissions to execute `$PROPERTY:ADD` events.

**MSGINPassword**: The external token for the provided *MSGINUser*.

## Example

### Sample XML file

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
    …
  </step>
  …
  <step type="a">
   <postingRequest type="c">
    …
    <amount>.34</amount>
    …
   </postingRequest>
   …
  </step>
  …
  <step type="a">
   <postingRequest type="c">
    …
    <amount>200.00</amount>
    …
   </postingRequest>
   …
  </step>
  …
  <step type="a">
   <postingRequest type="c">
    …
    <amount>225.00</amount>
    …
   </postingRequest>
   …
  </step>
</postingJournal>
```

### Sample command line

`SMASumItems.exe -xmlfilename 20120919_062026_000.ACH_Posting.xml -OwnerTag “postingRequest” -AmountTag "amount" -PropertyName myprop`

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
