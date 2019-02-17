---
template: post
title: How to use Amazon Athena in Nodejs
slug: /2019/02/17/how-to-user-amazon-athena-in-nodejs
draft: false
date: 2019-02-17T10:22:25.211Z
description: >-
  Amazon Athena provides a platform which we can use for Standard SQL query and
  it uses Amazon Simple Storage (S3) for data storage...
category: AWS Services
tags:
  - Amazon Athena
  - Nodejs
  - AWS
---
## **What is Amazon Athena?**

Amazon Athena provides a platform which we can use for Standard SQL query and it uses Amazon Simple Storage (S3) for data storage.

![](/media/screen-shot-2019-02-17-at-10.16.58-pm.png)

## **Why should we use Athena?**

* Athena is serverless and there is no infrastructure for setup and manage.
* Pay only for queries we do run.
* Queries are faster even with large dataset.
* Parallel query makes system more faster.
* Drop NoSql db to S3 and use Amazon Athena to analyze data directly from S3.

## **Steps to use Amazon Athena -**

**Step 1: Create a Database**

You first need to create a database in Athena. To create a database -

i) Open the Athena console.

ii) In the Athena Query Editor, you see a query pane with an example query. Start typing your query anywhere in the query pane. To create a database named testdatabase, enter the following CREATE DATABASE statement, and then choose Run Query:

```
CREATE DATABASE testdatabase
```

**Step 2: Create a Table**

i) Make sure that testdatabase is selected for DATABASE and then choose New Query.

ii) In the query pane, enter the following CREATE TABLE statement, and then choose Run Query:

```
CREATE EXTERNAL TABLE IF NOT EXISTS test_table(
`Date` DATE,
 Time STRING,
 Location STRING,
 Bytes INT,
 RequestIP STRING,
 Method STRING,
 Host STRING,
 Uri STRING,
 Status INT,
 Referrer STRING,
 os STRING,
 Browser STRING,
BrowserVersion STRING
) ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
WITH SERDEPROPERTIES (
"input.regex" = "^(?!#)([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^
 ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+([^ ]+)\\s+[^\(]+[\(]
([^\;]+).*\%20([^\/]+)[\/](.*)$") LOCATION 's3://athena-examples-
myregion/cloudfront/plaintext/';
```

Change Location path of S3, where you are keeping your data.

**Step 3: Query Data in nodejs**

**i) Create Query Execution Id**

```js
function createQueryExecutionId(){
/** doing resultConfiguration, but we will not save query result there. */
   const params = {
     QueryString: 'Select * From test-table', /* required */
     ResultConfiguration: { /* required */
     OutputLocation: `s3://bucket-name/`, 
    /* required s3 output location path*/
    EncryptionConfiguration: {
      EncryptionOption: 'SSE_S3', /* required */
    }
  }
};

athena.startQueryExecution(params, (err, data) => {
   console.log(err, data);
});
}
```



**ii) Check status of Created Query Execution Id in last step.**

```
function checkQueryCreateStatus(){
   const params = {
      QueryExecutionId: this.QueryExecutionId 
      /* required generated in last step*/
   };
   athena.getQueryExecution(params, (err, data) => {
     if (err) {
      console.log(err, err.stack); // an error occurred
     }
     else{
        if(data && data.QueryExecution && data.QueryExecution.Status &&
 data.QueryExecution.Status.State && data.QueryExecution.Status.State === 
'RUNNING'){
             console.log("Athena Query status is running");
          }
          else{
            console.log("Atehna Query status is Active");
          }
     }
   });
}
```

**iii) Get Query Result, when Athena Query status is Active.**

```
function getQueryResultByExecutionId(queryExecutionId){
    const params = {
      QueryExecutionId: queryExecutionId
      /*queryExecutionId created in first step.*/
    };
    athena.getQueryResults(params, (err, data) => {
       console.log(err?err.stack:err, data);
     });
}
```

**iv) Stop Athena Query Execution Id**

```
function stopQueryExecutionId(queryExecutionId){
    const params = {
       QueryExecutionId: queryExecutionId
    };
    athena.stopQueryExecution(params, (err, data) => {
      console.log(err?err.stack:err, data);
    });
}
```

Feel free to download the full code for this post to see the full picture of how everything works together and customize it for your own needs. Have a look to repository for complete working code.
[source code on github](https://github.com/pandeysoni/athena-in-nodejs)
