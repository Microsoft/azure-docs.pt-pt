---
title: Restaurar as partilhas de ficheiros Azure com a REST API
description: Saiba como utilizar a REST API para restaurar as partilhas de ficheiros Azure ou ficheiros específicos a partir de um ponto de restauro criado pela Azure Backup
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 1c3160491ef92c62745af1468556e7d5c30437fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252509"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>Restaurar as partilhas de ficheiros Azure usando a API REST

Este artigo explica como restaurar uma partilha de ficheiros inteira ou ficheiros específicos a partir de um ponto de restauro criado pela [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) utilizando a API REST.

No final deste artigo, aprenderá a realizar as seguintes operações utilizando a Rest API:

* Ver pontos de restauro para uma partilha de ficheiros Azure apoiada.
* Restaurar uma parte completa do ficheiro Azure.
* Restaurar ficheiros ou pastas individuais.

## <a name="prerequisites"></a>Pré-requisitos

Assumimos que já tem uma parte de ficheiro supértido que quer restaurar. Se não o fizer, verifique a partilha de [ficheiros Backup Azure utilizando a API REST](backup-azure-file-share-rest-api.md) para aprender a criar uma.

Para este artigo, usaremos os seguintes recursos:

* **RecoveryServicesVault**: *azurefilesvault*
* **Grupo de recursos**: *ficheiros azurefiles*
* **Conta de Armazenamento**: *afsaccount*
* **Partilha de Ficheiros**: *ficheiros azurefiles*

## <a name="fetch-containername-and-protecteditemname"></a>Buscar o Nome do Contentor e o Nome protegido

Para a maioria das chamadas API relacionadas com restauro, é necessário passar valores para os parâmetros {containerName} e {protectedItemName} URI. Utilize o atributo DE ID no corpo de resposta da operação de [backupprotectitems](https://docs.microsoft.com/rest/api/backup/protecteditems/get) GET para recuperar valores para estes parâmetros. No nosso exemplo, a identificação da parte do ficheiro que queremos proteger é:

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

Assim, os valores traduzem-se da seguinte forma:

* {nome de contentor} - *recipiente de armazenamento;armazenamento;ficheiros em azul;afsaccount*
* {protectedItemName} - *azurefileshare;azurefiles*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>Buscar pontos de recuperação para a parte de ficheiros azure apoiada

Para restaurar qualquer partilha de ficheiros ou ficheiros de ficheiros apoiados, primeiro selecione um ponto de recuperação para executar a operação de restauro. Os pontos de recuperação disponíveis de um item back-up podem ser listados usando a chamada REST API recovery [Point-List.](https://docs.microsoft.com/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) É uma operação GET com todos os valores relevantes.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

Desloque os valores URI da seguinte forma:

* {fabricName}: *Azure*
* {vaultName}: *cofre azurefilesvault*
* {nome do contentor}: *recipiente de armazenamento;armazenamento;ficheiros azurefiles;afsaccount*
* {protectedItemName}: *azurefileshare;azurefiles*
* {ResourceGroupName}: *ficheiros em azul*

O GET URI tem todos os parâmetros necessários. Não há necessidade de um corpo de pedido adicional.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response"></a>Resposta de exemplo

Uma vez que o GET URI é submetido, uma resposta de 200 é devolvida:

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

O ponto de recuperação é identificado com o campo {name} na resposta acima.

## <a name="full-share-recovery-using-rest-api"></a>Recuperação total de ações usando a Rest API

Utilize esta opção de restauro para restaurar a parte completa do ficheiro no local original ou alternativo.
O desativação do restauro é um pedido post e pode executar esta operação utilizando o [gatilho restaurar](https://docs.microsoft.com/rest/api/backup/restores/trigger) a API REST.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Os valores {containerName} e {protectedItemName} são definidos [aqui](#fetch-containername-and-protecteditemname) e recoveryPointID é o campo {name} do ponto de recuperação acima mencionado.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Criar corpo de pedido

Para desencadear uma restauração para uma partilha de ficheiros Azure, seguem-se os componentes do organismo de pedido:

Nome |  Tipo   |   Descrição
--- | ---- | ----
Propriedades | Pedido de Restauro de Partilha de Ficheiros AzureFile | Propriedades do RestoreRequestResource

Para obter a lista completa de definições do organismo de pedido e outros detalhes, consulte o documento de restabelecimento do [gatilho REST API](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location"></a>Restaurar a localização original

#### <a name="request-body-example"></a>Solicitar exemplo de corpo

O seguinte órgão de pedido define as propriedades necessárias para desencadear uma restauração de partilha de ficheiros Azure:

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>Restaurar para localização alternativa

Especifique os seguintes parâmetros para a recuperação alternativa da localização:

* **targetResourceId**: A conta de armazenamento à qual o conteúdo back-up é restaurado. A conta de armazenamento alvo deve estar no mesmo local que o cofre.
* **nome**: A parte de ficheiro dentro da conta de armazenamento-alvo à qual o conteúdo de back-up é restaurado.
* **targetFolderPath**: A pasta sob a partilha de ficheiros para a qual os dados são restaurados.

#### <a name="request-body-example"></a>Solicitar exemplo de corpo

O organismo de pedido seguinte restaura a parte do ficheiro *azurefiles* na conta de armazenamento da *afsaccount* na conta de ficheiro *sinuosa* na conta de armazenamento *afaccount1.*

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>Resposta

O desencadeamento de uma operação de restauro é uma [operação assíncrona.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Esta operação cria outra operação que precisa de ser rastreada separadamente.
Devolve duas respostas: 202 (Aceite) quando outra operação é criada, e 200 (OK) quando essa operação estiver concluída.

#### <a name="response-example"></a>Exemplo de resposta

Uma vez que submeta o *POST* URI para desencadear um restauro, a resposta inicial é 202 (Aceite) com um cabeçalho de localização ou cabeçalho Azure-assync.

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

Em seguida, rastreie a operação resultante utilizando o cabeçalho de localização ou o cabeçalho da Operação Azure-Asynccom um comando GET.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

Uma vez concluída a operação, devolve 200 (OK) com a identificação do trabalho de restauro resultante no corpo de resposta.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Para a recuperação alternativa da localização, o corpo de resposta será assim:

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Uma vez que o trabalho de reserva é uma operação de longa duração, deve ser rastreado como explicado nos trabalhos de [monitorização utilizando o documento REST API](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job).

## <a name="item-level-recovery-using-rest-api"></a>Recuperação do nível do item utilizando a API REST

Pode utilizar esta opção de restauro para restaurar ficheiros ou pastas individuais no local original ou alternativo.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Os valores {containerName} e {protectedItemName} são definidos [aqui](#fetch-containername-and-protecteditemname) e recoveryPointID é o campo {name} do ponto de recuperação acima mencionado.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Criar corpo de pedido

Para desencadear uma restauração para uma partilha de ficheiros Azure, seguem-se os componentes do organismo de pedido:

Nome |  Tipo   |   Descrição
--- | ---- | ----
Propriedades | Pedido de Restauro de Partilha de Ficheiros AzureFile | Propriedades do RestoreRequestResource

Para obter a lista completa de definições do organismo de pedido e outros detalhes, consulte o documento de restabelecimento do [gatilho REST API](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location"></a>Restaurar a localização original

O seguinte organismo de pedido deve restaurar o ficheiro *Restoretest.txt* na parte de ficheiro sinuosa na conta de armazenamento da *afsaccount.* *azurefiles*

Criar corpo de pedido

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location"></a>Restaurar para localização alternativa

O seguinte organismo de pedido deve restaurar o ficheiro *Restoretest.txt* na parte *de* ficheiro sida na conta de armazenamento da *afsaccount* na pasta de *dados de restauro* da parte de ficheiro *sinuosa* da conta de armazenamento *afaccount1.*

Criar corpo de pedido

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

A resposta deve ser tratada da mesma forma que acima explicada para [a restituição total](#full-share-recovery-using-rest-api)das ações .

## <a name="next-steps"></a>Passos seguintes

* Saiba como gerir as partilhas de [ficheiros Azure utilizando](manage-azure-file-share-rest-api.md)a Rest API .
