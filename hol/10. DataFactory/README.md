# Data Factory #
## Using Visual Studio/Hive ##

## Overview ##
- Create the data factory. A data factory can contain one or more data pipelines that move and process data.
- Create the linked services. You create a linked service to link a data store or a compute service to the data factory. A data store such as Azure Storage holds input/output data of activities in the pipeline. A compute service such as Azure HDInsight processes/transforms data.
- Create input and output datasets. An input dataset represents the input for an activity in the pipeline and an output dataset represents the output for the activity.
- Create the pipeline. A pipeline can have one or more activities. These can be things like Copy Activities (to copy data from a source to a destination) (or) HDInsight Hive Activity to transform input data using a Hive script to produce output data. We will use the HDInsight Hive activity that runs a Hive script. The script first creates an external table that references the raw web log data stored in Azure blob storage and then partitions the raw data by year and month.
- Your first pipeline, called <initials>Pipeline, uses a Hive activity to transform and analyze a web log that you upload to the inputdata folder in adf container (adf/inputdata) in your Azure blob storage.


## Create a Storage Account ##
1. New -> Data + Storage -> Storage account
 - Give it a name that is unique!
 - Leave the defaults
 - Create a new resource group - give it a unique name!

## Create HQL script file ##

1. Using your favorite text editor create a new file called weblogs.hql, in the directory of your choosing, and paste in the following.


```
set hive.exec.dynamic.partition.mode=nonstrict;

DROP TABLE IF EXISTS WebLogsRaw;
CREATE TABLE WebLogsRaw (
  date  date,
  time  string,
  ssitename string,
  csmethod  string,
  csuristem  string,
  csuriquery string,
  sport int,
  susername string,
  cipcsUserAgent string,
  csCookie string,
  csReferer string,
  cshost  string,
  scstatus  int,
  scsubstatus  int,
  scwin32status  int,
  scbytes int,
  csbytes int,
  timetaken int
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
LINES TERMINATED BY '\n'
tblproperties ("skip.header.line.count"="2");

LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;

DROP TABLE IF EXISTS WebLogsPartitioned ;
create external table WebLogsPartitioned (  
  date  date,
  time  string,
  ssitename string,
  csmethod  string,
  csuristem  string,
  csuriquery string,
  sport int,
  susername string,
  cipcsUserAgent string,
  csCookie string,
  csReferer string,
  cshost  string,
  scstatus  int,
  scsubstatus  int,
  scwin32status  int,
  scbytes int,
  csbytes int,
  timetaken int
)
partitioned by ( year int, month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '${hiveconf:partitionedtable}';

INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month)
SELECT
  date,
  time,
  ssitename,
  csmethod,
  csuristem,
  csuriquery,
  sport,
  susername,
  cipcsUserAgent,
  csCookie,
  csReferer,
  cshost,
  scstatus,
  scsubstatus,
  scwin32status,
  scbytes,
  csbytes,
  timetaken,
  year(date),
  month(date)
FROM WebLogsRaw
```
Save and close.

## Create a sample input file ##
1. Again using your favorite text editor create a file named input.log in a folder of your choosing. Add the following:

```
#Software: Microsoft Internet Information Services 8.0
#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2014-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
```
Save a close the file.

## Upload input file to your Azure Blob Storage ##

1. Download the latest version of [AzCopy](http://aka.ms/downloadazcopy)

1. After installing make this environment change:
```
set path=%path%;C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
```

1. Change to the directory where your data file is located and execute the following:
*** Your Storage Key is Found in Access Keys for your storage account ***

```
AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adf/data /DestKey:<storagekey>  /Pattern:input.log
```
You will see confirmation that the file uploaded successfully. You can also check your storage account blob.

## Visual Studio ##
1. You will need to install the [Azure Data Factory Plugin](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)

## Create your project ##
1. Launch Visual Studio 2015. Click File, New, and  Project. You should see the New Project dialog box.
1. In the New Project dialog, select the DataFactory template, and click Empty Data Factory Project.
1. Enter a name for the project, location, the solution, and click OK.

## Create Linked Services ##
A data factory can have one or more pipelines. A pipeline can have one or more activities in it. You will specify the name and settings for the data factory later when you publish.

1. Right-click Linked Services in the solution explorer, point to Add, and click New Item.
1. In the Add New Item dialog box, select Azure Storage Linked Service from the list, and click Add.
1. Replace accountname and accountkey with the name of your Azure storage account and its key.
1. Save the AzureStorageLinkedService1.json file.

## Create Azure HDInsight linked service ##
Next we will create and add an on-demand HDInsight cluster to your data factory. The HDInsight cluster is automatically created at runtime and deleted after it is done processing and idle for the specified amount of time

1. In the Solution Explorer, right-click Linked Services, point to Add, and click New Item.

1. Select HDInsight On Demand Linked Service, and click Add.

1. Replace the JSON with the following:

```
{
  "$schema": "http://datafactories.schema.management.azure.com/schemas/2015-09-01/Microsoft.DataFactory.LinkedService.json",
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterSize": 2,
      "linkedServiceName": "AzureStorageLinkedService1",
      "osType": "linux",
      "timeToLive": "00:30:00",
      "version": "3.2"
    }
  }
}
```
## DataSets ##
Next we create datasets to represent the input and output data for Hive processing. These datasets refer to the AzureStorageLinkedService1 you have created earlier. The linked service points to an Azure Storage account and datasets specify container, folder, file name in the storage that holds input and output data.

1. In the Solution Explorer, right-click Tables, click Add, and  New Item
1. Select Azure Blob from the list, change the name of the file to InputDataSet.json, and click Add.
1. Replace the JSON in the editor with the following:
```
{
  "$schema": "http://datafactories.schema.management.azure.com/schemas/2015-09-01/Microsoft.DataFactory.Table.json",
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "AzureStorageLinkedService1",
    "typeProperties": {
      "fileName": "input.log",
      "folderPath": "adf/data",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ","
      }
    },
    "availability": {
      "frequency": "Month",
      "interval": 1
    },
    "external": true,
    "policy": { }
  }
}
```

## Ourput Dataset ##
Now, we create the output dataset to represent the output data stored in the Azure Blob storage.

1. In the Solution Explorer, right-click tables, click Add, and  New Item.
1. Select Azure Blob from the list, change the name of the file to OutputDataset.json, and click Add.
1. Replace the JSON in the editor with the following:
```
{
  "$schema": "http://datafactories.schema.management.azure.com/schemas/2015-09-01/Microsoft.DataFactory.Table.json",
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "AzureStorageLinkedService1",
    "typeProperties": {
      "folderPath": "adf/partitioneddata",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ","
      }
    },
    "availability": {
      "frequency": "Month",
      "interval": 1
    }
  }
}
```
## Create pipeline ##
Next we create the  pipeline with a HDInsightHive activity. Note that input slice is available monthly (frequency: Month, interval: 1), output slice is produced monthly, and the scheduler property for the activity is also set to monthly. The settings for the output dataset and the activity scheduler must match. Currently, output dataset is what drives the schedule, so you must create an output dataset even if the activity does not produce any output. If the activity doesn't take any input, you can skip creating the input dataset. The properties used in the following JSON are explained at the end of this section.

1. In the Solution Explorer, right-click Pipelines, point to Add, and click New Item.
1. Select Hive Transformation Pipeline from the list, and click Add.
1. Replace the JSON with the following snippet.
```
{
  "$schema": "http://datafactories.schema.management.azure.com/schemas/2015-09-01/Microsoft.DataFactory.Pipeline.json",
  "name": "MyFirstPipeline",
  "properties": {
    "description": "My first Azure Data Factory pipeline",
    "activities": [
      {
        "type": "HDInsightHive",
        "typeProperties": {
          "defines": {
            "inputtable": "wasb://adf@sbuxjkstore.blob.core.windows.net/data",
            "partitionedtable": "wasb://adf@sbuxjkstore.blob.core.windows.net/partitioneddata"
          },
          "scriptLinkedService": "AzureStorageLinkedService1",
          "scriptPath": "adf/scripts/partweblogs.hql"
        },
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "policy": {
          "concurrency": 1,
          "retry": 3
        },
        "scheduler": {
          "frequency": "Month",
          "interval": 1
        },
        "name": "RunSampleHiveActivity",
        "linkedServiceName": "HDInsightOnDemandLinkedService"
      }
    ],
    "start": "2016-04-01T00:00:00Z",
    "end": "2016-04-02T00:00:00Z",
    "isPaused": false
  }
}
```
## Dependencies ##
Add input.log as a dependency

1. Right-click Dependencies in the Solution Explorer window, select Add, and click Existing Item.
1. Navigate to partitionweblogs.hql and select.

## Publish ##

1. Right-click project in the Solution Explorer, and click Publish.
***If you see Sign in to your Microsoft account dialog box, enter your credentials for the account that has Azure subscription, and click sign in.***
1. In the Configure data factory page, do the following:
 - select Create New Data Factory option.
 - Enter <initials>FirstDataFactoryUsingVS for Name
1. Verify the other selections (Subscription, resourece group, region)
1. Select next to review and next again to publish.

## Monitor pipeline ##
1. Login to the Azure Portal
1. Browse to Data Factories
1. Select Diagram to see a visual of the pipeline
1. In the Diagram View, double-click the dataset AzureBlobInput. Confirm that the slice is in Ready state.
1. In the Diagram View, double-click the dataset AzureBlobOutput. You see that the slice that is currently being processed.
1. Monitor to completion
