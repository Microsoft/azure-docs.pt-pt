---
title: Gerir a Análise do Azure Data Lake com o Azure SDK para Node.js
description: Este artigo descreve como utilizar o SDK do Azure para Node.js, para gerir contas de Data Lake Analytics, origens de dados, tarefas e utilizadores.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 9de1bcf4-b15b-4d0b-9284-8889ecf0c438
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 0603a60ea73d47dd6107ee80afc5c776ff8c83bc
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047116"
---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Gerir a Análise do Azure Data Lake com o Azure SDK para Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerir contas do Azure Data Lake Analytics, origens de dados, utilizadores e tarefas com uma aplicação escrita através da SDK do Azure para Node.js. 

Suporta as seguintes versões:
* **Versão Node.js: 0.10.0 ou superior**
* **Versão de API REST para a Conta: 2015-10-01-preview**
* **Versão de API REST para o Catálogo: 2015-10-01-preview**
* **Versão de API REST para a Tarefa: 2016-03-20-preview**

## <a name="features"></a>Funcionalidades
* Gestão de contas: criar, obter, listar, atualizar e eliminar.
* Gestão de tarefas: submeter, obter, listar e cancelar.
* Gestão de catálogos: obter e listar.

## <a name="how-to-install"></a>Como instalar
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Autenticar com o Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Criar o cliente de Data Lake Analytics
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Criar uma conta de Data Lake Analytics
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>Obter uma lista de tarefas
```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Obter uma lista de bases de dados no Catálogo de Data Lake Analytics
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Consulte também
* [Microsoft Azure SDK para Node.js](https://github.com/azure/azure-sdk-for-node)
* [Microsoft Azure SDK para Node.js - Gestão do Data Lake Store](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)

