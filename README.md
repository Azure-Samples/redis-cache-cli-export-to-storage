---
services: Azure Redis Cache
platforms: .Net
author: msonecode
---

# How to export premium Redis cache using cross-platform method 


## Introduction

Export allows you to export the data stored in Azure Redis Cache to Redis compatible RDB file(s). You can use this feature to move data from one Azure Redis Cache instance to another or to another Redis server. During the exporting process, a temporary file is created on the VM that hosts the Azure Redis Cache server instance, and the file is uploaded to the designated storage account. When the exporting operation ends in either a status of success or failure, the temporary file is deleted. Import/Export enables you to migrate between different Azure Redis Cache instances or to populate the cache with data before being used.

There are multiple methods to export Redis cache:

### 1. Azure Portal
You can [Export Redis to a Blob Container through Azure Portal](https://docs.microsoft.com/en-us/azure/redis-cache/cache-how-to-import-export-data#export), but this method is not suitable for programming implementation needs.
 
### 2. Azure PowerShell
On Windows platform, with the benefit from Azure PowerShell, we can [Export a Redis Cache through Azure PowerShell](https://docs.microsoft.com/en-us/azure/redis-cache/cache-howto-manage-redis-cache-powershell#to-export-a-redis-cache). However, as we know Azure PowerShell still not supported Linux environment. So this is not a cross-platform approach to meet our needs.

### 3. Azure CLI
Now we can use Azure CLI to [Export data stored in a redis cache](https://docs.microsoft.com/en-us/cli/azure/redis#export) to support cross-platform needs. The CLI command is available there, but there is no available example for users' reference. Developers may fail to run this command if there is any missing parameter. This post will provide you with an example and the possible issue scenario you may meet during your production.

### 4. REST API
Representational State Transfer (REST) APIs are service endpoints that support sets of HTTP operations (methods), which provide create/retrieve/update/delete access to the service's resources. [Export data from the redis cache to blobs in a container through REST API](https://docs.microsoft.com/en-us/rest/api/redis/redis#Redis_ExportData) is an elegant way to meet our cross-platform needs.

## Prerequisites

### 1. Azure Account
You need an Azure account. You can [open a free Azure account](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) or [Activate Visual Studio subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### 2. Azure CLI 2.0 (Preview)
[Install Azure CLI 2.0 (Preview)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2) on whatever platform you use. Azure CLI 2.0 is in preview and it works only with the resource manager deployment model. You can also install Azure CLI, which is released and works with all services.

## Detailed Steps
1.	On any platform you have Azure CLI installed, run **az** with no argument to verify the installation. 
    You should get a page like this:
    <img src="https://github.com/zhangdingsong/ExportRedisViaAzureCLI/raw/master/az.jpg">
2.	Run the login command.
    <img src="https://github.com/zhangdingsong/ExportRedisViaAzureCLI/raw/master/azlogin.jpg">
    You'll be prompted to open https://aka.ms/devicelogin and enter some code.
3.	Use a web browser to open the page https://aka.ms/devicelogin and enter the code to authenticate.
    You'll be prompted to log in using your credentials.
4.	Log in.
    Now you can run any command that accesses your account.
5.	 Run command **az redis export**
     Parameters, and we need to notice: 
     - --container: blob storage container full path name with SAS token.
       - Note: characters like colon mark ":" must be escaped, errors will appear if SAS token is not served properly. (you can generate valid SAS token through **Azure Storage Explorer**)
       - Please check Common Error section for details. 
     - --prefix:  any prefix name you prefer for output file.
     - --file-format: we can set RDB as default.
     - --ids: resource ID of Redis service. 
     （If provided, then there will be no need to specify.) 
     - --name-n: parameter.
     
Example: 
```shell
      az redis export --container "https://cloudservicexxx.blob.core.windows.net/redispersistence-redis-persistence?st=2017-01-09T06%3A22%3A00Z&se=2017-01-20T06%3A22%3A00Z&sp=rwdl&sv=2015-04-05&sr=c&sig=GUuXXXXXXXXXXXX%3D" --file-format rdb --prefix testexportredis --ids /subscriptions/d1xxxxxxxxxxxxxe9/resourceGroups/redisPersistenceRG/providers/Microsoft.Cache/Redis/redisPersistence
```

## Common Error

Error: Report SAS URIs poorly formatted.
<img src="https://github.com/zhangdingsong/ExportRedisViaAzureCLI/raw/master/SASError_Ink_LI.jpg">

The root cause of this issue: 
### 1. Colon mark ":" needs to escaped before being used here as a parameter. You can see the only difference is the escaped character.
 - Azure Portal generated SAS token
```shell
     sv=2015-12-11&ss=bfqt&srt=sco&sp=rwdlacup&se=2017-05-31T21:51:24Z&st=2017-01-09T13:51:24Z&spr=https,http&sig=llWaJ2TtAJWIhpm3j7PKead8%2BuHXp1IRUs4G%2B5dYcsQ%3D
```

 - Azure Storage Explorer generated SAS token
```shell
     st=2017-01-09T06%3A22%3A00Z&se=2017-01-20T06%3A22%3A00Z&sp=rwdl&sv=2015-04-05&sr=c&sig=GUuS3DZzZufB4y8OJR8%2FIrcSxbIZle10gEMqrhNMNsA%3D
```

### 2. Long parameters should be cupped by double quotation marks.

## Reference
Here are some useful links, you will need them before your real action.
Azure Portal: Export Redis to a Blob Container.
https://docs.microsoft.com/en-us/azure/redis-cache/cache-how-to-import-export-data#export

PowerShell: Export Redis to a Blob Container.
https://docs.microsoft.com/en-us/azure/redis-cache/cache-howto-manage-redis-cache-powershell#to-export-a-redis-cache 

Azure CLI: Export Redis to a Blob Container.
https://docs.microsoft.com/en-us/cli/azure/redis#export

REST API: Export Redis to a Blob Container.
https://docs.microsoft.com/en-us/rest/api/redis/redis#Redis_ExportData

Microsoft Azure Storage Explorer
http://storageexplorer.com/
