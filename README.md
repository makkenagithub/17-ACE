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







