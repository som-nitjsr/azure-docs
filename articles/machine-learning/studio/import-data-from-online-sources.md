---
title: Import data into Machine Learning Studio from online data sources | Microsoft Docs
description: How to import your training data Azure Machine Learning Studio from various online sources.
keywords: import data,data format,data types,data sources,training data
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: "(previous ms.author=hshapiro, author=heatherbshapiro)"
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 701b93fe-765b-4d15-a1cf-9b607f17add6
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
---
# Import data into Azure Machine Learning Studio from various online data sources with the Import Data module
This article describes the support for importing online data from various sources and the information needed to move data from these sources into an Azure Machine Learning experiment.

> [!NOTE]
> This article provides general information about the [Import Data][import-data] module. For more detailed information about the types of data you can access, formats, parameters, and answers to common questions, see the module reference topic for the [Import Data][import-data] module.
> 
> 

## Introduction
By using the [Import Data][import-data] module, you can access data from one of several online data sources while your experiment is running in [Azure Machine Learning Studio](https://studio.azureml.net/Home):

* A Web URL using HTTP
* Hadoop using HiveQL
* Azure blob storage
* Azure table
* Azure SQL database or SQL Server on Azure VM
* On-premises SQL Server database
* A data feed provider, OData currently
* Azure Cosmos DB

To access online data sources in your Studio experiment, add the [Import Data][import-data] module to your, select the **Data source**, and then provide the parameters needed to access the data. The online data sources that are supported are itemized in the table below. This table also summarizes the file formats that are supported and parameters that are used to access the data.

Note that because this training data is accessed while your experiment is running, it's only available in that experiment. By comparison, data that has been stored in a dataset module are available to any experiment in your workspace.

> [!IMPORTANT]
> Currently, the [Import Data][import-data] and [Export Data][export-data] modules can read and write data only from Azure storage created using the Classic deployment model. In other words, the new Azure Blob Storage account type that offers a hot storage access tier or cool storage access tier is not yet supported. 
> 
> Generally, any Azure storage accounts that you might have created before this service option became available should not be affected. 
> If you need to create a new account, select **Classic** for the Deployment model, or use Resource manager and select **General purpose** rather than **Blob storage** for **Account kind**. 
> 
> For more information, see [Azure Blob Storage: Hot and Cool Storage Tiers](../../storage/blobs/storage-blob-storage-tiers.md).
> 
> 

## Supported online data sources
Azure Machine Learning **Import Data** module supports the following data sources:

| Data Source | Description | Parameters |
| --- | --- | --- |
| Web URL via HTTP |Reads data in comma-separated values (CSV), tab-separated values (TSV), attribute-relation file format (ARFF), and Support Vector Machines (SVM-light) formats, from any web URL that uses HTTP |<b>URL</b>: Specifies the full name of the file, including the site URL and the file name, with any extension. <br/><br/><b>Data format</b>: Specifies one of the supported data formats: CSV, TSV, ARFF, or SVM-light. If the data has a header row, it is used to assign column names. |
| Hadoop/HDFS |Reads data from distributed storage in Hadoop. You specify the data you want by using HiveQL, a SQL-like query language. HiveQL can also be used to aggregate data and perform data filtering before you add the data to Machine Learning Studio. |<b>Hive database query</b>: Specifies the Hive query used to generate the data.<br/><br/><b>HCatalog server URI </b> : Specified the name of your cluster using the format *&lt;your cluster name&gt;.azurehdinsight.net.*<br/><br/><b>Hadoop user account name</b>: Specifies the Hadoop user account name used to provision the cluster.<br/><br/><b>Hadoop user account password</b> : Specifies the credentials used when provisioning the cluster. For more information, see [Create Hadoop clusters in HDInsight](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Location of output data</b>: Specifies whether the data is stored in a Hadoop distributed file system (HDFS) or in Azure. <br/><ul>If you store output data in HDFS, specify the HDFS server URI. (Be sure to use the HDInsight cluster name without the HTTPS:// prefix). <br/><br/>If you store your output data in Azure, you must specify the Azure storage account name, Storage access key and Storage container name.</ul> |
| SQL database |Reads data that is stored in an Azure SQL database or in a SQL Server database running on an Azure virtual machine. |<b>Database server name</b>: Specifies the name of the server on which the database is running.<br/><ul>In case of Azure SQL Database enter the server name that is generated. Typically it has the form *&lt;generated_identifier&gt;.database.windows.net.* <br/><br/>In case of a SQL server hosted on a Azure Virtual machine enter *tcp:&lt;Virtual Machine DNS Name&gt;, 1433*</ul><br/><b>Database name </b>: Specifies the name of the database on the server. <br/><br/><b>Server user account name</b>: Specifies a user name for an account that has access permissions for the database. <br/><br/><b>Server user account password</b>: Specifies the password for the user account.<br/><br/><b>Database query</b>:Enter a SQL statement that describes the data you want to read. |
| On-premises SQL database |Reads data that is stored in an on-premises SQL database. |<b>Data gateway</b>: Specifies the name of the Data Management Gateway installed on a computer where it can access your SQL Server database. For information about setting up the gateway, see [Perform advanced analytics with Azure Machine Learning using data from an on-premises SQL server](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Database server name</b>: Specifies the name of the server on which the database is running.<br/><br/><b>Database name </b>: Specifies the name of the database on the server. <br/><br/><b>Server user account name</b>: Specifies a user name for an account that has access permissions for the database. <br/><br/><b>User name and password</b>: Click <b>Enter values</b> to enter your database credentials. You can use Windows Integrated Authentication or SQL Server Authentication depending upon how your on-premises SQL Server is configured.<br/><br/><b>Database query</b>:Enter a SQL statement that describes the data you want to read. |
| Azure Table |Reads data from the Table service in Azure Storage.<br/><br/>If you read large amounts of data infrequently, use the Azure Table Service. It provides a flexible, non-relational (NoSQL), massively scalable, inexpensive, and highly available storage solution. |The options in the **Import Data** change depending on whether you are accessing public information or a private storage account that requires login credentials. This is determined by the <b>Authentication Type</b> which can have value of "PublicOrSAS" or "Account", each of which has its own set of parameters. <br/><br/><b>Public or Shared Access Signature (SAS) URI</b>: The parameters are:<br/><br/><ul><b>Table URI</b>: Specifies the Public or SAS URL for the table.<br/><br/><b>Specifies the rows to scan for property names</b>: The values are <i>TopN</i> to scan the specified number of rows, or <i>ScanAll</i> to get all rows in the table. <br/><br/>If the data is homogeneous and predictable, it is recommended that you select *TopN* and enter a number for N. For large tables, this can result in quicker reading times.<br/><br/>If the data is structured with sets of properties that vary based on the depth and position of the table, choose the *ScanAll* option to scan all rows. This ensures the integrity of your resulting property and metadata conversion.<br/><br/></ul><b>Private Storage Account</b>: The parameters are: <br/><br/><ul><b>Account name</b>: Specifies the name of the account that contains the table to read.<br/><br/><b>Account key</b>: Specifies the storage key associated with the account.<br/><br/><b>Table name</b> : Specifies the name of the table that contains the data to read.<br/><br/><b>Rows to scan for property names</b>: The values are <i>TopN</i> to scan the specified number of rows, or <i>ScanAll</i> to get all rows in the table.<br/><br/>If the data is homogeneous and predictable, we recommend that you select *TopN* and enter a number for N. For large tables, this can result in quicker reading times.<br/><br/>If the data is structured with sets of properties that vary based on the depth and position of the table, choose the *ScanAll* option to scan all rows. This ensures the integrity of your resulting property and metadata conversion.<br/><br/> |
| Azure Blob Storage |Reads data stored in the Blob service in Azure Storage, including images, unstructured text, or binary data.<br/><br/>You can use the Blob service to publicly expose data, or to privately store application data. You can access your data from anywhere by using HTTP or HTTPS connections. |The options in the **Import Data** module change depending on whether you are accessing public information or a private storage account that requires login credentials. This is determined by the <b>Authentication Type</b> which can have a value either of "PublicOrSAS" or of "Account".<br/><br/><b>Public or Shared Access Signature (SAS) URI</b>: The parameters are:<br/><br/><ul><b>URI</b>: Specifies the Public or SAS URL for the storage blob.<br/><br/><b>File Format</b>: Specifies the format of the data in the Blob service. The supported formats are CSV, TSV, and ARFF.<br/><br/></ul><b>Private Storage Account</b>: The parameters are: <br/><br/><ul><b>Account name</b>: Specifies the name of the account that contains the blob you want to read.<br/><br/><b>Account key</b>: Specifies the storage key associated with the account.<br/><br/><b>Path to container, directory, or blob </b> : Specifies the name of the blob that contains the data to read.<br/><br/><b>Blob file format</b>: Specifies the format of the data in the blob service. The supported data formats are CSV, TSV, ARFF, CSV with a specified encoding, and Excel. <br/><br/><ul>If the format is CSV or TSV, be sure to indicate whether the file contains a header row.<br/><br/>You can use the Excel option to read data from Excel workbooks. In the <i>Excel data format</i> option, indicate whether the data is in an Excel worksheet range, or in an Excel table. In the <i>Excel sheet or embedded table </i>option, specify the name of the sheet or table that you want to read from.</ul><br/> |
| Data Feed Provider |Reads data from a supported feed provider. Currently only the Open Data Protocol (OData) format is supported. |<b>Data content type</b>: Specifies the OData format.<br/><br/><b>Source URL</b>: Specifies the full URL for the data feed. <br/>For example, the following URL reads from the Northwind sample database: http://services.odata.org/northwind/northwind.svc/ |

## Next steps

[Deploying Azure ML web services that use Data Import and Data Export modules](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
