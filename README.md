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
1. using commandline
2. using web UI of ace node / eg
3. drag and drop application in ace toolkit
4. drag and drop application bar file in ace toolkit



