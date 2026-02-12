# 17-ACE

### creating ace node and intergation server / eg
```
mqsicreatebroker <ace-node-name> -q <QM name>
mqsistart <ace-node-name>
mqsicreateexecutiongroup <ace-node-name> -e <EG-name>
```
mqsicreateexecutiongroup with this command EG will be created and started.


### to deploy bar file into integration server / eg
```
mqsideploy ACENODE -e <server/eg> -a <barfile.bar>
```
### to deploy bar file into standlone integration server / eg
```
mqsideploy ACENODE -i <localhost> -p <7601> -a <barfile.bar>
```
There are 4 ways we can deploy an application into ACE node /  standlone integration server / eg
1. using commandline with mqsideploy command
2. using web UI of ace node / eg
3. drag and drop application in ace toolkit
4. drag and drop application bar file in ace toolkit


## ESQL
https://www.notion.so/IBM-ACE13-1a29b46d6a2c8030bd38da3241c93a14


Concatination: we concatinate using || symbol. If we concatinate anything with 'null', entire expression becomes null.

COALESCE function checks the not null value in the expressions passed and return first not null value.

COALESCE(Expression1, Expression2, ..., ExpressionN)

We can use COALESCE function to provide default value to the filed when its null. when the expression is null, as below
```
SET outRef.Address = COALESCE(inRef.addressLine1,'')||' '
     ||COALESCE(inRef.addressLine2,'')||' '
     ||COALESCE(inRef.addressLine3,'')||' '
     ||COALESCE(inRef.addressLine4,'');
```

CASTING: to convert one data type to another. 

### DB connection to ACE node
Initially create a data source (DSN) with a name say "DATA_SOURCE_NAME" using ODBC data source administrator tool. Now we have associate this DSN with ACE now using below command

```
mqsisetdbparms <ACE_NODE> -n <DATA_SOURCE_NAME> -u <DB_user_name> -p <DB_password> 
```
<img width="299" height="40" alt="image" src="https://github.com/user-attachments/assets/3411c4eb-6662-4ea9-82ef-3fc4de3c5df5" />

Then check the DB connection
```
mqsicvp <ACE_NODE> -n <DATA_SOURCE_NAME>
```
<img width="426" height="93" alt="image" src="https://github.com/user-attachments/assets/bb09a7f4-17f2-4a11-b8be-56cd844a4cf2" />

After that RESTART IIB NODE

### PASSTHRU

When we want to update DB through ESQL code, then PASSTHRU needs to be used
```
PASSTHRU(sqlQuery);
```
<img width="481" height="47" alt="image" src="https://github.com/user-attachments/assets/a0b0160d-36f4-4c79-b423-aa5502be727e" />

### ROW
To get the DB details to a variable in esql, we use ROW variable
```
DECLARE DBDetails ROW;
SET sqlQuery = 'SELECT *FROM EMPLOYEE';
SET DBDetails.results[] = PASSTHRU(sqlQuery);
SET OutputRoot.JSON.Data.DBdata = DBDetails;
```
### PROPAGATE
To route the request to another subflow we can use PROPAGATE

<img width="191" height="89" alt="image" src="https://github.com/user-attachments/assets/f2d92070-8ab0-422f-a5a6-dc11c8ea12d0" />

### PATH
shared and static libraries are referred using PATH.

Eg:
```
PATH com.ibm.empdata.sharedlib
```

### Query string
We can pass the query directly in the url itself as http://localhost:7071/emp?id=14  instead of message input body.

So to get this information we can tick mark the query string option in http input node advanced properties. The information of id=14 will come in localEnverronment tree.

### parse timing:
Ondemand parsing, complete parsing, immediate parsing

### opaque parsing
<img width="493" height="122" alt="image" src="https://github.com/user-attachments/assets/11050e06-16c2-407b-a1bf-5b8e74097c41" />

opaque parsing works only in XML message

Opaque parsing can be used to select which all items in the xml mesage to not parse. Sometimes, we may have a large xml message, and we donot want to 
parse entire message to improve performance. In that case we can use opaque parsing , to parse only required part of message. In below case Items is not parsed.

<img width="236" height="95" alt="image" src="https://github.com/user-attachments/assets/1e1544ac-a090-45c0-9331-6128a0ed00d3" />

### message validation

for xml input data, if we know the expected xml input data and its data types, then we can generate validation xsd files from toolkit and use it to validate the message. If any fileds are missed in the input xml data or data types are wrong, then message validation throws exception.

### enableing/disabling user trace
enable:
```
mqsichangetrace ACENode -u -e <IntegrationServername> -l debug
```
-u indicates user trace

log path is 
<img width="361" height="239" alt="image" src="https://github.com/user-attachments/assets/08f81f44-5107-48fe-9bcc-56470237e12c" />


disable:
```
mqsichangetrace ACENode -u -e <IntegrationServername> -l none
```
We need to restart / reload the integration server after enable/disable
```
mqsireload ACENode -e <IntegrationServername> 
```

### REST api (web service)


### HTTPRequestNode
HTTPRequestNode is used to make a call to webservice(or a REST api)

Flows can ge created multiple ways , an example is 

input -> API1 -> HTTPRequestnode -> API2 -> output 

<img width="296" height="100" alt="image" src="https://github.com/user-attachments/assets/51e3ac6c-fdba-418f-9cf7-e2f03157a187" />

Here we usually pass an API url to HTTPRequestnode and it calls that API. We can pass url as below

<img width="442" height="70" alt="image" src="https://github.com/user-attachments/assets/8e61af1a-e90f-4a3e-8ea1-b158901551a2" />


```
In API1 we can give output as
SET OutpurLocalEnvironment.Destination.HTTP.RequestURL = 'http://localhost:7080/pincodeapi/v1/validateapi?pin='||pincode
```

If the requirement is like , multiple flows need to use same HTTP request node , then we can create HTTP request node as shared librarry and use it in all required flows.

### bar file override with proeprties file
```
mqsiapplybaroverride -b barfile.bar -p propertiesfile.properties -r
```
<img width="364" height="179" alt="image" src="https://github.com/user-attachments/assets/c25d3974-3823-4eb1-a9ab-06533fbfb268" />

command to deploy bar file
```
mqsideploy ACENODE -e IntegrationServer -a barfile.bar
```
we can not override to a bar file which already overidden. Means, only once we can override a bar file.

### external variables
We need to define an external vraible in user defined properties(UDP) in console with some name and its default value. (click on a flow from diagram and see UDP)

In esql flow file , we need to declare the external variable in the esql as below. And we cant modify the external varaible in the esql code.
```
DECLARE externalVar EXTERNAL CHAR ''
```
External variable can be of any data type except ROW.

How to modify these external variable ?
We need to create a properties file and in that file we need to provide the values to external variable , and then override the bar file with properties file.

### MQ flow
<img width="316" height="88" alt="image" src="https://github.com/user-attachments/assets/4e76ee32-05d2-4982-b441-a78f90d02224" />

In MQ flow we have MQ input node and MQ output node. And inbetween we can have compute node. We give queue names in MQ input and MQ output nodes.

When an application (RFH util) places messages to MQ input node , then messages comes to queue in MQ input node. 
Then goes to comupte node and then messages placed to MQ output queue in MQ output node. 
In case of any exception in compute node, we can handle through exception queue. In this case the message in MQ input queue will go to queue in Exception handler.

When we compare HHTP flow with MQ Flow, MQ flow is asynchronous. HTTP flow is synchronous. HTTP expects response in certain duration i.e. 60 sec or 180 sec. 
But MQ flow is asynchronous.

### transaction mode property in MQ flow
Assume we did not handle any exception handler queue as above.
If we set Transaction mode as "Yes", and if an exception occurs in compute node, then MQ flow looks for backout queue in MQ Input queue, 
if BOQ is there in message goes to BOQ. 
If BOQ does not exist, then message goes to dead letter queue when the exception occurs.
If no BOQ and DLQ, then an infinite loop kind of flow happens between MQ input node and compute node.

If we set Transaction mode as "No", then if there is no unhandled exception, then message will be lost.

If we set Transaction mode as "Automatic", then if the emssage is of persistent or queue property is persistent, 
then message wont be lost in case unhandled exception. Message exists in MQ input queue.

### MQ reply node
If we want send reply to the original message which comes, then we need to use the MQ reply node. Every message has message ID and corellation ID. 
When the reply node sends the response, then the corelation id contains the message id of the message to which its sending reply/response.

In the code we give the queue name for reply queue, as below

<img width="176" height="41" alt="image" src="https://github.com/user-attachments/assets/6a48d21a-37ea-4686-a53f-c7ece5744367" />

### Flow order node:

<img width="335" height="122" alt="image" src="https://github.com/user-attachments/assets/11438509-0710-4e38-8ec0-053dcf352e7f" />

If we want to send the message to MQ output node and also to MQ reply node, we use flow order node. 
ALso we can achieve this by connecting the compute node output terminals to mq reply node and mq output node.
So this way we can send reply to original requester and also plcing message to output node.

### Pub sub
<img width="467" height="132" alt="image" src="https://github.com/user-attachments/assets/077d56ab-e700-43d9-bc82-5065e493d96f" />



