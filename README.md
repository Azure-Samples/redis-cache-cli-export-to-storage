---
services: Azure Redis Cache
platforms: .Net
author: msonecode
---

# Cross-Platform Method to Export Premium Redis Cache


## Introduction

Export allows you to export the data stored in Azure Redis Cache to Redis compatible RDB file(s). You can use this feature to move data from one Azure Redis Cache instance to another or to another Redis server. During the export process, a temporary file is created on the VM that hosts the Azure Redis Cache server instance, and the file is uploaded to the designated storage account. When the export operation completes with either a status of success or failure, the temporary file is deleted. Import/Export enables you to migrate between different Azure Redis Cache instances or populate the cache with data before use.

There are multiple methods to export redis cache:

***1. Azure Portal***<br/>
You can [Export Redis to a Blob Container through Azure Portal](https://docs.microsoft.com/en-us/azure/redis-cache/cache-how-to-import-export-data#export), but this method is not suitable for programming implementation needs.
 
***2. Azure PowerShell***<br/>
On Windows platfrom, with the befinet from Azure PowerShell, we can [Export a Redis Cache through Azure PowerShell](https://docs.microsoft.com/en-us/azure/redis-cache/cache-howto-manage-redis-cache-powershell#to-export-a-redis-cache). However, as we know Azure PowerShell still not supported Linux environment. So this is not a cross-platform approach to meet our needs.

***3. Azure CLI***<br/>
Now we can use Azure CLI to [Export data stored in a redis cache](https://docs.microsoft.com/en-us/cli/azure/redis#export) to support cross-platfrom needs. The CLI command is available there, but there is no a working example for user reference. Developers may failed to run this command if missed some validation parameters. This post will help to give you an example and possible issue scenario you may met from your production.

***4. REST API***<br/>
Representational State Transfer (REST) APIs are service endpoints that support sets of HTTP operations (methods), which provide create/retrieve/update/delete access to the service's resources. [Export data from the redis cache to blobs in a container through REST API](https://docs.microsoft.com/en-us/rest/api/redis/redis#Redis_ExportData) is a pretty elegant way to met our cross-platfrom needs.


## Prerequisites

***1. Azure Account***<br/>
You need an Azure account. You can [open a free Azure account](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) or [Activate Visual Studio subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

***2. Azure CLI 2.0 (Preview)***<br/>
[Install Azure CLI 2.0 (Preview)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2) on whatever platform you use. Azure CLI 2.0 is in preview and it works only with the resource manager deployment model. You can also install Azure CLI, which is released and works with all services.

## Detailed Steps
1.	From any platform you have Azure CLI installed, run **az** with no arguments to verify the installation. 
    You should get a page like this:
    <img src="https://github.com/zhangdingsong/ExportRedisViaAzureCLI/raw/master/az.jpg">
2.	Run the login command.
    <img src="https://github.com/zhangdingsong/ExportRedisViaAzureCLI/raw/master/azlogin.jpg">
    You'll be prompted to open https://aka.ms/devicelogin and enter a code.
3.	Use a web browser to open the page https://aka.ms/devicelogin and enter the code to authenticate.
    You'll be prompted to log in using your credentials.
4.	Log in.
    Now you can run any command that accesses your account.
5.	 Run command **az redis export**
     Parameters we need to notice: 
     * --container: blob storage container full path name with SAS token.
     <br/>Note: characters like colon mark ":" must be escaped, there always failed with errors if SAS token is not ser properly. (you can generage valid SAS token through **Azure Storage Explorer**)
     <br/>Please check Common Error section for details. 
     * --prefix:  any prefix name you preferred for output file.
     * --file-format: we can set to rdb as default
     * --ids: resource ID of Redis service. 
     if provided, then no need to specify 
     * --name-n parameter.
     <br/><br/>
Example: 
```shell
      az redis export --container "https://cloudservicexxx.blob.core.windows.net/redispersistence-redis-persistence?st=2017-01-09T06%3A22%3A00Z&se=2017-01-20T06%3A22%3A00Z&sp=rwdl&sv=2015-04-05&sr=c&sig=GUuXXXXXXXXXXXX%3D" --file-format rdb --prefix testexportredis --ids /subscriptions/d1xxxxxxxxxxxxxe9/resourceGroups/redisPersistenceRG/providers/Microsoft.Cache/Redis/redisPersistence
```

## Common Error

Error: Report SAS URIs poorly formatted.
<img src="https://github.com/zhangdingsong/ExportRedisViaAzureCLI/raw/master/SASError_Ink_LI.jpg">

The root cause of this issue: <br/>
1. colon mark ":" need to escaped before use here as an parameter. You can see the only difference is escaped charactor.<br/>
 * Azure Portal generated SAS token<br/>
```shell
     sv=2015-12-11&ss=bfqt&srt=sco&sp=rwdlacup&se=2017-05-31T21:51:24Z&st=2017-01-09T13:51:24Z&spr=https,http&sig=llWaJ2TtAJWIhpm3j7PKead8%2BuHXp1IRUs4G%2B5dYcsQ%3D
```

 * Azure Storage Explorer generated SAS token<br/>
```shell
     st=2017-01-09T06%3A22%3A00Z&se=2017-01-20T06%3A22%3A00Z&sp=rwdl&sv=2015-04-05&sr=c&sig=GUuS3DZzZufB4y8OJR8%2FIrcSxbIZle10gEMqrhNMNsA%3D
```
<br/>
2. long parameter should cupping by double quotation marks.<br/>

## Reference
Below are some useful links, you will need them before your real action.<br/><br/>
Azure Portal: Export Redis to a Blob Container.<br/>
https://docs.microsoft.com/en-us/azure/redis-cache/cache-how-to-import-export-data#export

PowerShell: Export Redis to a Blob Container.<br/>
https://docs.microsoft.com/en-us/azure/redis-cache/cache-howto-manage-redis-cache-powershell#to-export-a-redis-cache 

Azure CLI: Export Redis to a Blob Container.<br/>
https://docs.microsoft.com/en-us/cli/azure/redis#export

REST API: Export Redis to a Blob Container.<br/>
https://docs.microsoft.com/en-us/rest/api/redis/redis#Redis_ExportData

Microsoft Azure Storage Explorer<br/>
http://storageexplorer.com/
