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

When we want to update DB through ESQL code, then PASSTHROUGH needs to be used
```
PASSTHROUGH(sqlQuery)
```
<img width="481" height="47" alt="image" src="https://github.com/user-attachments/assets/a0b0160d-36f4-4c79-b423-aa5502be727e" />


