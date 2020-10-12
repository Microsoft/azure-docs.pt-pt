---
title: Faça o back up ações de ficheiros Azure com a REST API
description: Saiba como usar a API REST para apoiar as ações de ficheiros Azure no cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 8d2d8ed88da133986540a293185c8e37000ab87b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88824870"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Backup Azure partilha de ficheiros usando Azure Backup via Rest API

Este artigo descreve como apoiar uma partilha de Ficheiros Azure usando a Azure Backup via REST API.

Este artigo pressupõe que já criou um cofre dos Serviços de Recuperação e uma política para configurar a cópia de segurança para a sua parte do ficheiro. Se não o fez, consulte o [cofre de criação](./backup-azure-arm-userestapi-createorupdatevault.md) e crie tutoriais [de API de política](./backup-azure-arm-userestapi-createorupdatepolicy.md) REST para criar novos cofres e políticas.

Para este artigo, usaremos os seguintes recursos:

- **RecoveryServicesVault**: *azurefilesvault*

- **Política:** *horário1*

- **Grupo de recursos**: *azurefiles*

- **Conta de Armazenamento**: *testvault2*

- **Partilha de Ficheiros**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Configurar backup para uma partilha de ficheiros Azure desprotegida usando a REST API

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Descubra contas de armazenamento com ações de ficheiros Azure desprotegidas

O cofre precisa de descobrir todas as contas de armazenamento da Azure na subscrição com ações de ficheiros que podem ser apoiadas até ao cofre dos Serviços de Recuperação. Isto é acionado utilizando a [operação de atualização](/rest/api/backup/protectioncontainers/refresh). É uma operação ASSíncrona *POST* que garante que o cofre obtém a mais recente lista de todas as ações de arquivo azure desprotegidas na subscrição atual e 'caches' delas. Uma vez que a partilha de ficheiros é 'cached', os serviços de recuperação podem aceder à partilha de ficheiros e protegê-la.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

O POST URI `{subscriptionId}` `{vaultName}` tem, `{vaultresourceGroupName}` e `{fabricName}` parâmetros. No nosso exemplo, o valor para os diferentes parâmetros será o seguinte:

- `{fabricName}` é *Azure*

- `{vaultName}` é *azurefilesvault*

- `{vaultresourceGroupName}` é *azurefiles*

- $filter=backupManagementType eq 'AzureStorage'

Uma vez que todos os parâmetros necessários são dados no URI, não há necessidade de um corpo de pedido separado.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>Respostas à operação de atualização

A operação "refresh" é uma [operação assíncronea.](../azure-resource-manager/management/async-operations.md) Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada, e 200 (OK) quando essa operação termina.

##### <a name="example-responses-to-the-refresh-operation"></a>Exemplo de respostas à operação de atualização

Uma vez apresentado o pedido *do CORREIO,* é devolvida uma resposta 202 (Aceite).

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Acompanhe a operação resultante utilizando o cabeçalho "Localização" com um simples comando *GET*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Uma vez descobertas todas as contas de Armazenamento Azure, o comando GET devolve uma resposta de 200 (Sem Conteúdo). O cofre é agora capaz de descobrir qualquer conta de armazenamento com ações de ficheiro que podem ser apoiadas dentro da subscrição.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Obtenha lista de contas de armazenamento com ações de arquivo que podem ser apoiadas com cofre de Serviços de Recuperação

Para confirmar que o "caching" está feito, liste todas as contas de armazenamento na subscrição com ações de ficheiros que podem ser apoiadas com o cofre dos Serviços de Recuperação. Em seguida, localize a conta de armazenamento desejada na resposta. Isto é feito utilizando a operação [GET ProtectableContainers.](/rest/api/backup/protectablecontainers/list)

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

O *GET* URI tem todos os parâmetros necessários. Não é necessário nenhum corpo de pedido adicional.

Exemplo do corpo de resposta:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Uma vez que podemos localizar a conta de armazenamento *testvault2* no corpo de resposta com o nome amigável, a operação de atualização realizada acima foi bem sucedida. O cofre dos Serviços de Recuperação pode agora descobrir com sucesso contas de armazenamento com ficheiros desprotegidos na mesma subscrição.

### <a name="register-storage-account-with-recovery-services-vault"></a>Conta de armazenamento registre com cofre dos Serviços de Recuperação

Este passo só é necessário se não registar a conta de armazenamento com o cofre mais cedo. Pode registar o cofre através da [operação ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register).

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Definir as variáveis para o URI da seguinte forma:

- {resourceGroupName} - *azurefiles*
- {fabricName} - *Azure*
- {abóbadaName} - *azurefilesvault*
- {containerName} - Este é o atributo de nome no corpo de resposta da operação GET ProtectableContainers.
   No nosso exemplo, é *StorageContainer; Armazenamento; AzureFiles;testvault2*

>[!NOTE]
> Pegue sempre no atributo de nome da resposta e preencha-o neste pedido. Não crie códigos rígidos nem crie o formato de nome de recipiente. Se o criar ou codificar, a chamada API falhará se o formato do nome do contentor mudar no futuro.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

O órgão de pedido de criação é o seguinte:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

Para obter a lista completa das definições do organismo de pedido e outros detalhes, consulte o [ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register#azurestoragecontainer).

Trata-se de uma operação assíncrona e devolve duas respostas: "202 Aceitos" quando a operação é aceite e "200 OK" quando a operação estiver concluída.  Para acompanhar o estado da operação, utilize o cabeçalho de localização para obter o estado mais recente da operação.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Exemplo do corpo de resposta quando a operação estiver concluída:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

Pode verificar se o registo foi bem sucedido a partir do valor do parâmetro *de inscrição* no organismo de resposta. No nosso caso, mostra o estado registado para *testvault2,* pelo que a operação de registo foi bem sucedida.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Inquirir todas as ações de ficheiros desprotegidas sob uma conta de armazenamento

Pode inquirir sobre itens protegidos numa conta de armazenamento utilizando a operação [Protection Containers-Inquire.](/rest/api/backup/protectioncontainers/inquire) É uma operação assíncronea e os resultados devem ser rastreados usando o cabeçalho de localização.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Definir as variáveis para o URI acima:

- {abóbadaName} - *azurefilesvault*
- {fabricName} - *Azure*
- {containerName}- Consulte o atributo de nome no corpo de resposta da operação GET ProtectableContainers. No nosso exemplo, é *StorageContainer; Armazenamento; AzureFiles;testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Uma vez que o pedido é bem sucedido, ele devolve o código de estado "OK"

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Selecione a partilha de ficheiros que pretende fazer back-up

Pode listar todos os itens protegidos sob a subscrição e localizar a partilha de ficheiros pretendida para ser apoiada utilizando a operação [de proteção de backup GET.](/rest/api/backup/backupprotectableitems/list)

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Construa o URI da seguinte forma:

- {abóbadaName} - *azurefilesvault*
- {$filter} - *backupManagementType eq 'AzureStorage'*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Resposta da amostra:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

A resposta contém a lista de todas as ações de ficheiros desprotegidas e contém todas as informações necessárias pelo Serviço de Recuperação Azure para configurar a cópia de segurança.

### <a name="enable-backup-for-the-file-share"></a>Ativar a cópia de segurança para a partilha de ficheiros

Depois de a partilha de ficheiros relevante ser "identificada" com o nome amigável, selecione a política para proteger. Para saber mais sobre as políticas existentes no cofre, consulte a [lista de API de Política.](/rest/api/backup/backuppolicies/list) Em seguida, selecione a [política relevante](/rest/api/backup/protectionpolicies/get) referindo-se ao nome da apólice. Para criar políticas, consulte a [criação de tutoriais políticos.](./backup-azure-arm-userestapi-createorupdatepolicy.md)

Permitir a proteção é uma operação *PUT* assíncronea que cria um "item protegido".

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Deite o **nome de recipiente** e proteja as variáveis de **nome** usando o atributo ID no corpo de resposta do funcionamento do protetor de segurança GET.

No nosso exemplo, a ID de partilha de ficheiros que queremos proteger é:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {containername} - *storagecontainer;storage;azurefiles;testvault2*
- {protectedItemName} - *azurefileshare;testshare*

Ou pode consultar o **atributo de nome** do recipiente de proteção e respostas de produto protegido.

>[!NOTE]
>Pegue sempre no atributo de nome da resposta e preencha-o neste pedido. Não crie códigos rígidos nem crie o formato de nome de recipiente ou o formato de nome de produto protegido. Se o criar ou codificar, a chamada API falhará se o formato do nome do contentor ou o formato de nome do item protegido mudar no futuro.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Criar um corpo de pedido:

O seguinte órgão de pedido define as propriedades necessárias para criar um item protegido.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

A **fonteResourceId** é o **parentcontainerFabricID** em resposta ao sistema de proteção de backup GET.

Resposta de Amostra

A criação de um item protegido é uma operação assíncronea, que cria outra operação que precisa de ser rastreada. Devolve duas respostas: 202 (Aceite) quando outra operação é criada e 200 (OK) quando essa operação estiver concluída.

Uma vez que envie o pedido *PUT* para criação ou atualização de artigos protegidos, a resposta inicial é 202 (Aceito) com um cabeçalho de localização.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Em seguida, rastreia a operação resultante utilizando o cabeçalho de localização ou Azure-AsyncOperation cabeçalho com um comando *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Uma vez concluída a operação, devolve 200 (OK) com o conteúdo do artigo protegido no organismo de resposta.

Corpo de resposta à amostra:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Isto confirma que a proteção está ativada para a partilha de ficheiros e que a primeira cópia de segurança será ativada de acordo com o calendário da política.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Desencadear uma cópia de segurança a pedido para a partilha de ficheiros

Uma vez que uma partilha de ficheiros Azure é configurada para cópia de segurança, as cópias de segurança são executadas de acordo com o calendário da apólice. Pode esperar pelo primeiro backup programado ou desencadear uma cópia de segurança a pedido a qualquer momento.

Desencadear uma cópia de segurança a pedido é uma operação POST.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} e {protectedItemName} são construídos acima, permitindo a cópia de segurança. Para o nosso exemplo, isto traduz-se em:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Criar corpo de pedido

Para desencadear uma cópia de segurança a pedido, seguem-se os componentes do corpo de pedido.

| Nome       | Tipo                       | Descrição                       |
| ---------- | -------------------------- | --------------------------------- |
| Propriedades | AzurefilesharebackupReques | BackupRequestResource propriedades |

Para obter a lista completa das definições do organismo de pedido e outros detalhes, consulte as [cópias de segurança para itens protegidos REST Documento API](/rest/api/backup/backups/trigger#request-body).

Pedido Exemplo corpo

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>Respostas à operação de backup a pedido

Desencadear uma cópia de segurança a pedido é uma [operação assíncronea.](../azure-resource-manager/management/async-operations.md) Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada e 200 (OK) quando essa operação estiver concluída.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>Respostas de exemplo à operação de backup a pedido

Uma vez que envie o pedido *DEM* para um backup a pedido, a resposta inicial é 202 (Aceito) com um cabeçalho de localização ou cabeçalho Azure-async.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Em seguida, rastreia a operação resultante utilizando o cabeçalho de localização ou Azure-AsyncOperation cabeçalho com um comando *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Uma vez concluída a operação, retorna 200 (OK) com o ID do trabalho de backup resultante no organismo de resposta.

#### <a name="sample-response-body"></a>Corpo de resposta à amostra

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Uma vez que o trabalho de backup é uma operação de longa duração, tem de ser rastreado como explicado nos trabalhos de monitorização utilizando o [documento REST API](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Passos seguintes

- Saiba como [restaurar as ações de ficheiros Azure utilizando a API rest](restore-azure-file-share-rest-api.md).
