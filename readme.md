# Achieve Green IT targets by integrating Turbo with Envizi

This document explains the step-by-step instructions to pull green IT data from Turbonomic into Envizi via App Connect.

## Contents

- [1. Prequisite](#1-Prequisite)
- [2. Architecture](#2-Architecture)
- [3. Create Connectors](#3-Create-Connectors)
- [4. Import the Flows - Locations](#4-Import-the-Flows-Locations)
- [5. Import the Flows - Accounts](#5-Import-the-Flows-Accounts)
- [6. Execution](#6-Execution)

## 1. Prequisite

- Turbonomic v8.6.6 or higher 
- IBM App Connect SaaS or App Connect Enterprise on-prem
- Envizi's S3 bucket 

## 2. Architecture

Here is the archiecture that describes about this Turbo and Envizi integration.

We need to create App Connect flow that pulls the list of Cloud Regions and On-prem Data Centers from Turbo and sends it to Envizi's S3 bucket in a CSV file. This CSV file will be further processed by the Envizi internally.

<img src="images/arch.png">

## 3. Create Connectors

Need to create Amazon S3 connector and 2 HTTP connectors.

#### 3.1. Create Amazon S3 Connector

Need to create `Amazon S3` connector. 

1. Click on `Catalog > Amazon S3 > Add a New Account` 
<img src="images/01connector1.png">

2. Fill in the S3 details as below. (Credentials should have been given by Envizi)

```
Secret access key : Moxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
Access key ID : AKXXXXXXXXXXXXXXXXXX
```
<img src="images/01-connector-2.png">

3. The new connector account (ex: Account 2) might have been created.

<img src="images/01-connector-3.png">

#### 3.2. Create Http Connector 1

1. Type `http` in the filter

2. Click on `Http Connector > Add a New Account` 

<img src="images/01-connector-4.png">

3. Fill in details as below. 

- User Name :  `username` (as it is)
- API Key : Enter the Turbonomic credentials in this format `USERNAME&password=PASSWORD`.  **Example:** If the username is `test-user` and the password is `test-password`, enter `test-user&password=test-password`
- API Key Location :  `body URL encoded`

<img src="images/01-connector-5.png">
<img src="images/01-connector-6.png">

4. Click on `Connect` button.
 
5. The new connector account (ex: Account 3) might have been created.

<img src="images/01-connector-7-1-1.png">


#### 3.3. Create Http Connector 2

1. Click on `Http Connector > Add a New Account` 

<img src="images/01-connector-7-1-2.png">

2. Leave all the fileds as it is. 

<img src="images/01-connector-7-2.png">
<img src="images/01-connector-7-3.png">

3. Click on `Connect` button.
 
4. The new connector account (ex: Account 4) might have been created.

<img src="images/01-connector-7-4.png">

## 4. Import the Flows - Locations

Need to import the flow and configure connector, variables and schedule.

#### 4.1. Import the flow

1. Click on `Dashboard > Import Flow` 

<img src="images/02-flow-1.png">

2. Download the flow file [TurbonomicLocations.yaml](./files/appconnect-flows/TurbonomicLocations.yaml)

3. Drag and Drop the same file into the given place.

4. Click on `Import`

<img src="images/02-flow-2.png">

5. The flow should have been created.

<img src="images/flow1.png">

<img src="images/02-flow-3.png">

#### 4.2. Set Http Connector 1

1. Click on `HTTP` node with the label `Invoke Method`. The details are displayed in the bottom. 

<img src="images/03-flow-http1.png">

2. Select the first http account (Account 3) created above,

<img src="images/03-flow-http2.png">

#### 4.3. Set Http Connector 2

1. Click on `HTTP` node with the label `Invoke Method 2` and the details are displayed in the bottom. 

<img src="images/03-flow-http3-1.png">

2. Select the second http account (Account 4).

<img src="images/03-flow-http3-2.png">

#### 4.4. Set Http Connector 3

1. Click on `HTTP` node with the label `Invoke Method 3` and select the second http account (Account 4) for this also. 

<img src="images/03-flow-http3-3.png">

#### 4.5. Set S3 Connector

1. Here is the sample S3 bucket name called `envizi-data-load` created and available. This should have been given by Envizi.

<img src="images/04-flow-s3-1.png">

2. Click on `Amazon S3` node with the label `Create Object` and the details are displayed in the bottom. 

<img src="images/04-flow-s3-2.png">

3. Enter the S3 bucket name (ex: envizi-data-load).

<img src="images/04-flow-s3-3.png">

#### 4.6. Set Variables

1. Click on `Set Variable` node and the details are displayed in the bottom. 

- URL : The Turbonomic url
- Customer : Here 'My-Turbo-Locations' is given as an example.

<img src="images/05-flow-variable.png">

#### 4.7. Set Scheduler

1. Click on `Scheduler` node and the details are displayed in the bottom. 

2. The flow can be configured to run as per our need. Here it is configured to run every day at 00:05 hours.

3. The checkbox `Also run the flow, when it's first swtiched on` to be on, if you want to run the flow immediately after you start the flow.

<img src="images/06-flow-schedule.png">

#### 4.8. Dashboard

The flow is created and available.

<img src="images/07-flow-dashboard.png">

## 5. Import the Flows - Accounts

#### 5.1. Import the flow

1. Click on `Dashboard > Import Flow` 

2. Download the flow file [TurbonomicAccounts.yaml](./files/appconnect-flows/TurbonomicAccounts.yaml)

3. Drag and Drop the same file into the given place.

4. Click on `Import`

<img src="images/10-flow-1.png">

5. The flow should have been created.

<img src="images/flow2.png">

#### 5.2. Set Http Connector 1

1. Click on `HTTP` node with the label `Invoke Method` and the details are displayed in the bottom. 

<img src="images/11-flow-http1.png">

2. Select the first http account (Account 3) created above.

#### 5.3. Set Http Connector 2, 3 and 4

1. Click on `HTTP` nodes with the labels `Invoke Method 3` , `Invoke Method 2` and `Invoke Method 5`  and the details are displayed in the bottom

2. Select the Second http account (Account 4) created above.

<img src="images/11-flow-http2.png">
<img src="images/11-flow-http3.png">
<img src="images/11-flow-http5.png">

#### 5.4. Set S3 connector

1. Click on `Amazon S3` node with the label `Create Object` and the details are displayed in the bottom. 

2. Enter the S3 bucket name (ex: envizi-data-load).

<img src="images/12-flow-s3-1.png">

#### 5.5. Set Variables

1. Click on `Set Variable` node and the details are displayed in the bottom. 

- URL : The Turbonomic url
- Customer : Here 'My-Turbo-Accounts' is given as an example.
- OverrideStartDate : Start date of the period for which the details are required from Turbo
- OverrideEndDate : End date of the period for which the details are required from Turbo. This could be the current date.

<img src="images/13-flow-variable.png">

#### 5.6. Set Scheduler

1. Click on `Scheduler` node and the details are displayed in the bottom. 

2. The flow can be configured to run as per our need. Here it is configured to run every day at 00:15 hours.

3. The checkbox `Also run the flow, when it's first swtiched on` to be on, if you want to run the flow immediately after you start the flow.

<img src="images/14-flow-schedule.png">

#### 5.7. Dashboard

The flow is created and available.

<img src="images/15-flow-dashboard.png">

## 6. Execution

#### 6.1. Start the flow

1. Right click on the top of the flow tile and start the flow.

<img src="images/16-flow-start.png">

#### 6.2. Data in S3

The flows will pull the data from the Turbo and push it to S3. You can see the output like this in S3.

<img src="images/20-s3-1.png">

<img src="images/20-s3-2.png">

#### 6.3. Sample Data from S3

The sample data is available here.  [Accounts](./files/data/accounts/),  [Locations](./files/data/locations/).

#### 6.4. Processing S3 files in Envizi

The Envizi automatically pull the data from S3 and process it and creates/update the Turbonomic Performance Dashboard as below.

<img src="images/30-turbo-dashboard.png">

## Reference

Turbonomic - Envizi Integration https://github.com/IBM/turbonomic-envizi-appconnect-flows