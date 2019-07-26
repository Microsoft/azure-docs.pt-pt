---
title: 'Backup do Azure: Fazer backup de VMs do Azure usando a API REST'
description: Gerenciar operações de backup do backup de VM do Azure usando a API REST
author: pvrk
manager: shivamg
keywords: API REST; Backup de VM do Azure; Restauração de VM do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: e78c7ca9e5b39beb160aeef96dbbf6bce07613e4
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466843"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Fazer backup de uma VM do Azure usando o backup do Azure via API REST

Este artigo descreve como gerenciar backups para uma VM do Azure usando o backup do Azure por meio da API REST. Configure a proteção pela primeira vez para uma VM do Azure desprotegida anteriormente, dispare um backup sob demanda para uma VM do Azure protegida e modifique as propriedades de backup de uma VM de backup por meio da API REST, conforme explicado aqui.

Consulte os tutoriais sobre [criar cofre](backup-azure-arm-userestapi-createorupdatevault.md) e [criar política](backup-azure-arm-userestapi-createorupdatepolicy.md) REST da API para criar novos cofres e políticas.

Vamos supor que você deseja proteger uma VM "testVM" em um grupo de recursos "testRG" para um cofre dos serviços de recuperação "testVault", presente no grupo de recursos "testVaultRG", com a política padrão (denominada "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Configurar o backup para uma VM do Azure desprotegida usando a API REST

### <a name="discover-unprotected-azure-vms"></a>Descobrir VMs do Azure desprotegidas

Primeiro, o cofre deve ser capaz de identificar a VM do Azure. Isso é disparado usando a [operação de atualização](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). É uma operação *post* assíncrona que garante que o cofre obtenha a lista mais recente de todas as VMs desprotegidas na assinatura atual e os ' caches '. Depois que a VM for ' armazenada em cache ', os serviços de recuperação poderão acessar a VM e protegê-la.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

O URI da postagem `{vaultName}`tem `{vaultresourceGroupName}` `{subscriptionId}`, `{fabricName}` ,, parâmetros. O `{fabricName}` é "Azure". De acordo com nosso exemplo `{vaultName}` , é "testVault" `{vaultresourceGroupName}` e é "testVaultRG". Como todos os parâmetros necessários são fornecidos no URI, não há necessidade de um corpo de solicitação separado.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Responses

A operação ' refresh ' é uma [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que essa operação cria outra operação que precisa ser controlada separadamente.

Ele retorna duas respostas: 202 (aceito) quando outra operação é criada e 200 (OK) quando essa operação é concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|204 sem conteúdo     |         |  OK sem nenhum conteúdo retornado      |
|202 aceito     |         |     Aceite    |

##### <a name="example-responses"></a>Respostas de exemplo

Depois que a solicitação *post* é enviada, uma resposta 202 (aceita) é retornada.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Acompanhar a operação resultante usando o cabeçalho "local" com um comando *Get* simples

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
```

Depois que todas as VMs do Azure forem descobertas, o comando GET retornará uma resposta 204 (sem conteúdo). O cofre agora é capaz de descobrir qualquer VM na assinatura.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>Selecionando a VM do Azure relevante

 Você pode confirmar que "caching" é feito [listando todos os itens](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) que podem ser protegidos na assinatura e localizar a VM desejada na resposta. [A resposta dessa operação](#example-responses-1) também fornece informações sobre como os serviços de recuperação identificam uma VM.  Quando estiver familiarizado com o padrão, você poderá ignorar essa etapa e prosseguir diretamente para [habilitar a proteção](#enabling-protection-for-the-azure-vm).

Esta operação é uma operação *Get* .

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

O URI *Get* tem todos os parâmetros necessários. Nenhum corpo de solicitação adicional é necessário.

##### <a name="responses-1"></a>Response

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

##### <a name="example-responses-1"></a>Respostas de exemplo

Depois que a solicitação *Get* é enviada, uma resposta 200 (OK) é retornada.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> O número de valores em uma resposta *Get* é limitado a 200 para uma ' page '. Use o campo ' nextLink ' para obter a URL para o próximo conjunto de respostas.

A resposta contém a lista de todas as VMs do Azure desprotegidas `{value}` e cada uma contém todas as informações exigidas pelo serviço de recuperação do Azure para configurar o backup. Para configurar o backup, observe `{name}` o campo e `{virtualMachineId}` o campo `{properties}` na seção. Construa duas variáveis com base nesses valores de campo, conforme mencionado abaixo.

- containerName = "iaasvmcontainer;"+`{name}`
- protectedItemName = "vm;"+ `{name}`
- `{virtualMachineId}`é usado posteriormente no [corpo da solicitação](#example-request-body)

No exemplo, os valores acima são convertidos em:

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Habilitando a proteção para a VM do Azure

Depois que a VM relevante for "armazenada em cache" e "identificada", selecione a política a ser protegida. Para saber mais sobre as políticas existentes no cofre, confira [API de política de lista](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Em seguida, selecione a [política relevante](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) fazendo referência ao nome da política. Para criar políticas, consulte o [tutorial criar política](backup-azure-arm-userestapi-createorupdatepolicy.md). "DefaultPolicy" é selecionado no exemplo abaixo.

Habilitar a proteção é uma operação *Put* assíncrona que cria um ' item protegido '.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

Os `{containerName}` e`{protectedItemName}` são conforme construídos acima. O `{fabricName}` é "Azure". Para nosso exemplo, isso se traduz em:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

#### <a name="create-the-request-body"></a>Criar o corpo da solicitação

Para criar um item protegido, veja a seguir os componentes do corpo da solicitação.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Propriedades do recurso ProtectedItem         |

Para obter a lista completa de definições do corpo da solicitação e outros detalhes, consulte o [documento criar API REST do item protegido](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Corpo da solicitação de exemplo

O corpo da solicitação a seguir define as propriedades necessárias para criar um item protegido.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

O `{sourceResourceId}` é o `{virtualMachineId}` mencionado acima da [resposta da lista de itens protegíveis](#example-responses-1).

#### <a name="responses"></a>Responses

A criação de um item protegido é uma [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que essa operação cria outra operação que precisa ser controlada separadamente.

Ele retorna duas respostas: 202 (aceito) quando outra operação é criada e 200 (OK) quando essa operação é concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 aceito     |         |     Aceite    |

##### <a name="example-responses"></a>Respostas de exemplo

Depois de enviar a solicitação *Put* para a criação ou atualização de itens protegidos, a resposta inicial é 202 (aceita) com um cabeçalho de local ou Azure-Async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Em seguida, acompanhe a operação resultante usando o cabeçalho Location ou o cabeçalho Azure-AsyncOperation com um comando *Get* simples.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

Quando a operação for concluída, ela retornará 200 (OK) com o conteúdo do item protegido no corpo da resposta.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Isso confirma que a proteção está habilitada para a VM e o primeiro backup será disparado de acordo com o agendamento da política.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Disparar um backup sob demanda para uma VM do Azure protegida

Depois que uma VM do Azure é configurada para backup, os backups acontecem de acordo com o agendamento da política. Você pode aguardar o primeiro backup agendado ou disparar um backup sob demanda a qualquer momento. A retenção de backups sob demanda é separada da retenção da política de backup e pode ser especificada para uma determinada data/hora. Se não for especificado, será considerado 30 dias a partir do dia do gatilho do backup sob demanda.

Disparar um backup sob demanda é uma operação *post* .

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

Os `{containerName}` e`{protectedItemName}` são conforme construídos [acima](#responses-1). O `{fabricName}` é "Azure". Para nosso exemplo, isso se traduz em:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>Criar o corpo da solicitação

Para disparar um backup sob demanda, estes são os componentes do corpo da solicitação.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Propriedades de BackupRequestResource         |

Para obter a lista completa de definições do corpo da solicitação e outros detalhes, consulte o [documento disparar backups para itens protegidos da API REST](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Corpo da solicitação de exemplo

O corpo da solicitação a seguir define as propriedades necessárias para disparar um backup para um item protegido. Se a retenção não for especificada, ela será retida por 30 dias a partir da hora do gatilho do trabalho de backup.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Responses

Disparar um backup sob demanda é uma [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que essa operação cria outra operação que precisa ser controlada separadamente.

Ele retorna duas respostas: 202 (aceito) quando outra operação é criada e 200 (OK) quando essa operação é concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|202 aceito     |         |     Aceite    |

##### <a name="example-responses-3"></a>Respostas de exemplo

Depois de enviar a solicitação *post* para um backup sob demanda, a resposta inicial é 202 (aceito) com um cabeçalho de local ou Azure-Async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Em seguida, acompanhe a operação resultante usando o cabeçalho Location ou o cabeçalho Azure-AsyncOperation com um comando *Get* simples.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

Quando a operação for concluída, ela retornará 200 (OK) com a ID do trabalho de backup resultante no corpo da resposta.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Como o trabalho de backup é uma operação de execução demorada, ele precisa ser acompanhado conforme explicado no [documento monitorar trabalhos usando a API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Modificar a configuração de backup para uma VM do Azure protegida

### <a name="changing-the-policy-of-protection"></a>Alterando a política de proteção

Para alterar a política com a qual a VM está protegida, você pode usar o mesmo formato que [habilitar a proteção](#enabling-protection-for-the-azure-vm). Basta fornecer a nova ID de política no [corpo da solicitação](#example-request-body) e enviar a solicitação. Por exemplo: Para alterar a política de testVM de ' DefaultPolicy ' para ' ProdPolicy ', forneça a ID ' ProdPolicy ' no corpo da solicitação.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

A resposta seguirá o mesmo formato mencionado [para habilitar a proteção](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Interromper a proteção, mas manter os dados existentes

Para remover a proteção em uma VM protegida, mas manter os dados já armazenados em backup, remova a política no corpo da solicitação e envie a solicitação. Depois que a associação com a política é removida, os backups não são mais disparados e nenhum novo ponto de recuperação é criado.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

A resposta seguirá o mesmo formato mencionado [para disparar um backup sob demanda](#example-responses-3). O trabalho resultante deve ser acompanhado conforme explicado no [documento monitorar trabalhos usando a API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Interromper a proteção e excluir dados

Para remover a proteção em uma VM protegida e excluir os dados de backup também, execute uma operação de exclusão conforme detalhado [aqui](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

Interromper a proteção e excluir dados é uma operação de *exclusão* .

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

Os `{containerName}` e`{protectedItemName}` são conforme construídos [acima](#responses-1). `{fabricName}`é "Azure". Para nosso exemplo, isso se traduz em:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

### <a name="responses-2"></a>Response

A proteção de *exclusão* é uma [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que essa operação cria outra operação que precisa ser controlada separadamente.

Ele retorna duas respostas: 202 (aceito) quando outra operação é criada e 204 (NoContent) quando essa operação é concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 aceito     |         |     Aceite    |

## <a name="next-steps"></a>Passos seguintes

[Restaurar dados de um backup de máquina virtual do Azure](backup-azure-arm-userestapi-restoreazurevms.md).

Para obter mais informações sobre as APIs REST do backup do Azure, consulte os seguintes documentos:

- [API REST do provedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
