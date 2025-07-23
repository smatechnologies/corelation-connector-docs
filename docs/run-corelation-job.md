# SMARunCorelationJob

This application starts and monitors a job that has been defined in the Corelation database.  If the job is initiated and finishes with no exceptions, this application exits with a value of 0.

## Usage

`SMARunCorelationJob.exe -jobname myJob`

### Command line options

- **-configuration**: The name of the configuration file to use.  It is an optional parameter.  If no configuration file is specified, SMARunCorelationJob.ini (in the same directory as SMARunCorelationJob.exe) is assumed.

- **-batchservername**: An optional argument to allow the specification of the batch server.  If this is not specified, the name “Batch Server” will be used.

- **-batchqueuename**: An optional argument to allow the specification of the batch queue to use.  If this is not specified, the name “Main Batch Queue” will be used.  This must be a valid queue name on the specified (or default) batch server.
  
  - `leastbusy` is now available as an additional option for the **batchqueuename** parameter
    - `leastbusy` allows the connector to query the Corelation API to find the batch queue with the fewest jobs and enqueue the new job there
    - If multiple queues are equally empty, the first returned by the API is selected
  
:::note

- The Corelation Sub-Type configuration in Solution Manager now includes a checkbox to enable this functionality.
- This checkbox ensures the process runs with **-BatchQueueName** `leastbusy`.

Version Requirements:

- Solution Manager v25.0+: Required to see and use the checkbox in the UI.
- Corelation Connector v22.4.0+: Required to use the `leastbusy` option via command line.
- Latest Connector Version (22.4.3): Includes a bug fix ensuring only “Open” queues are considered.

:::

- **-jobname**: The jobname to start.  Either the jobname or the jobserial must be specified.

- **-jobserial**: The jobserial to start.  Either the jobname or the jobserial must be specified.

- **-dumpxml**: An optional argument Specifies that XML messages between SMARunCorelationJob and the Corelation server are included in the output.

- **-dumpjobdetails**: This option directs SMARunCorelationJob to dump the job details and exit.  The –dumpxml directive should be specified in order to see the job details and their parameters (propterties).

- **-propertyowner**: This is a temporary option to nest options under the specified property name.  

:::info Example
For example, “Verafin Extract” expects XML in the format:

```xml
     <property name="INTERFACE_OPTIONS"> 
          <property name="DATE_0"> 
               <contents>2017-01-01</contents> 
          </property> 
          <property name="DATE_1"> 
               <contents>2017-01-04</contents> 
          </property> 
     </property> 
```

This only affects properties specified with -batchoptions.
:::

- **-showjoblist**: Instructs SMARunCorelationJob display all of the batch jobs that are defined in the Corelation database.

- **-showviewsubmit**: This option instructs SMARunCorelationJob to show the response to the VIEW_SUBMIT of the job (Optional).   The response to the actual submit is always shown (when –dumpxml has been selected.)

- **-arrayofparameters**: This string consists of an array name and its values.  

:::info Example
If this is specified:

`-arrayofparameters="ArrayName|Value1|Value2”`

The resultant XML would look like:

```xml
<property name="ArrayName" index="0" count="2">
  <property name="ARGUMENT">
    <contents>Value1</contents>
  </property>
</property>
<property name="ArrayName" index="1" count="2">
  <property name="ARGUMENT">
    <contents>Value2</contents>
  </property>
</property>
```

:::

- **-batchoptions**: This string is composed of one or more property name/value combinations.  Each item is separated by a vertical pipe symbol.  

:::info Example
For example, the string to specify the value of mytestfile.txt for a property called IMPORT_FILE_NAME would look like:

`“IMPORT_FILE_NAME|mytestfile.txt”`

A second property (if required) could be added on by specifying:

`“IMPORT_FILE_NAME|mytestfile.txt|Prop2|Value2”`

The specification (above) would generate:

```xml
     <property name="IMPORT_FILE_NAME">
       <contents>mytestfile.txt</contents>
     </property>
     <property name="Prop2">
       <contents>Value2</contents>
     </property>
```

These options can be ‘grouped’ by specifying -propertyowner.

If the user ALSO specified `-propertyowner=”InterfaceOptions”`, the XML generated would look like:

```xml
          <property name="InterfaceOptions">
       <property name="IMPORT_FILE_NAME">
         <contents>mytestfile.txt</contents>
       </property>
       <property name="Prop2">
         <contents>Value2</contents>
       </property>
     </property>
```

:::

- **-param1param99** This is an optional way of specifying parameters.   Some job parameters are “nested”.  Each parameter consists of multiple arguments.  The simplest format is “tag|value” (similar to batchoptions).  If this is a nested parameter, then the “parents” are specified as well.  

:::info Example

For example, to create the following structure:

```xml
          <batch>
      <batchOptions>
   <property name="alpha">
     <property name="beta">
            <property name="gamma">
              <contents>value</contents>
            </property>
          </property>
        </property>
      </batchOptions>
    </batch>
```

You could specify:

`-param1=”apha|beta|gamma|value”`
:::

- **-debug**: This option turns on all debug print.  All XML is displayed.  This includes the series of queries and responses for the job status.

## Configuration file

## Configuration file parameters

- **CorelationIPAddress**: The DNS resolvable name or IP address of the Corelation server.

- **CorelationPort**: The port number to use for communications.  This value must match the port number configured in the Corelation server.

- **UseSSL**: Configures the connection to use SSL. `true` and `false` are valid values.

- **CorelationServerName**: When `UseSSL=true`, this value must be provided and match the server name on the certificate of the Corelation server.

- **CorelationUser**: The user credentials to use to log into the Corelation server.

- **CorelationPassword**: The password of the CorelationUser specified above.  Alternately, this can be the path and filename to an encrypted file (See [SMACreateCorelationPassword](create-password-file)).

- **CorelationDeviceName**: The device name associated with the Corelation server to log into.

- **CorelationNameSpace**: The name of the XML namespace used by Corelation.  This value should not be changed unless SMA requests it to be changed.

- **MillisecondsBetweenRetries**: If a connection cannot be made to a Corelation server, it will be retried MaximumNumberOfRetries times.  MillisecondsBetweenRetries specifies how long to “rest” (in Milliseconds) between connection attempts.  If this parameter is NOT specified in the configuration file, 60000 (one minute) will be used.

- **MaximumNumberOfRetries**: If a connection cannot be made to a Corelation server, it will be retried MaximumNumberOfRetries times.  If this parameter is NOT specified in the configuration file, 30 will be used.

### Sample configuration file

```ini
#======================================================================
# These are the parameters to drive SMARunCorelationJob.
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
