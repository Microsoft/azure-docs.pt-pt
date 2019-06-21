---
title: 'Cópia de segurança do Azure: Cópia de segurança de VMs do Azure com a REST API'
description: Gerir as operações de cópia de segurança do Azure VM Backup com a REST API
services: backup
author: pvrk
manager: shivamg
keywords: API DE REST; Cópia de segurança VM do Azure; Restauro de VMS do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 295c4fed9ab674f0c9e812c02f6b82ee53ef1b91
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274864"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Fazer cópias de segurança de VM do Azure com o Azure Backup através da REST API

Este artigo descreve como gerir cópias de segurança para uma VM do Azure utilizando o Azure Backup através da REST API. Configurar a proteção pela primeira vez para uma VM do Azure não anteriormente protegido, acionar uma cópia de segurança a pedido para uma VM protegida do Azure e modificar propriedades de cópia de segurança de uma cópia de segurança de VM através da REST API, conforme explicado aqui.

Consulte a [criar cofre](backup-azure-arm-userestapi-createorupdatevault.md) e [criar política](backup-azure-arm-userestapi-createorupdatepolicy.md) tutoriais de REST API para criar novos cofres e políticas.

Vamos supor que pretende proteger uma VM "testVM" num grupo de recursos "testRG" para um cofre de serviços de recuperação "testVault", presente no grupo de recursos "testVaultRG", com a política predefinida (com o nome "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Configurar a cópia de segurança para uma VM do Azure não protegido com a REST API

### <a name="discover-unprotected-azure-vms"></a>Detetar VMs não protegida do Azure

Em primeiro lugar, o cofre deve ser capaz de identificar a VM do Azure. Isso é acionado com o [operação de atualização](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). É assíncrona *POST* operação que certifica-se de que o Cofre obtém a lista mais recente da VM tudo desprotegido na subscrição atual e "armazena em cache'-los. Assim que a VM é "em cache', serviços de recuperação será capazes de aceder à VM e protegê-lo.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

O URI de POST tem `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`, `{fabricName}` parâmetros. O `{fabricName}` é "Azure". De acordo com o nosso exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Como todos os parâmetros necessários são fornecidos no URI, não é necessário para um corpo de pedido separada.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Responses

A operação de "Atualizar" é uma [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que esta operação cria outra operação que tem de ser registados em separado.

Devolve duas respostas: 202 (aceite) quando outra operação é criada e, em seguida, 200 (OK) quando essa operação é concluída.

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|204-sem conteúdo     |         |  OK com nenhum conteúdo devolvido      |
|Aceite 202     |         |     Aceite    |

##### <a name="example-responses"></a>Respostas de exemplo

Uma vez a *POST* pedido ser submetido, é devolvida uma resposta de 202 (aceite).

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

Controlar a operação de resultante utilizando o cabeçalho "Location" com um simples *obter* comando

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
```

Depois de todas as VMs do Azure são detetadas, o comando GET retorna uma resposta de (sem conteúdo) 204. O Cofre está agora capaz de descobrir qualquer VM na subscrição.

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

### <a name="selecting-the-relevant-azure-vm"></a>Selecionar a VM do Azure relevantes

 Pode confirmar que "colocação em cache" é feita [listar todos os itens susceptíveis de proteção](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) sob a subscrição e localize a VM pretendida na resposta. [A resposta a esta operação](#example-responses-1) também fornece informações sobre a forma como os serviços de recuperação identifica uma VM.  Depois de se familiarizar com o padrão, pode ignorar este passo e avançar diretamente para [ativar a proteção](#enabling-protection-for-the-azure-vm).

Esta operação é uma *obter* operação.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

O *obter* URI tem todos os parâmetros necessários. Não é necessário nenhum corpo de pedido adicionais.

##### <a name="responses-1"></a>Respostas

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

##### <a name="example-responses-1"></a>Respostas de exemplo

Uma vez a *obter* pedido ser submetido, é devolvida uma resposta 200 (OK).

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
> O número de valores numa *obter* resposta está limitada a 200 para uma página. Utilize o campo de "nextLink" para obter o URL para o próximo conjunto de respostas.

A resposta contém a lista de todas as VMs do Azure não protegido e cada `{value}` contém todas as informações necessárias pelo serviço de recuperação do Azure para configurar a cópia de segurança. Para configurar a cópia de segurança, tenha em atenção a `{name}` campo e o `{virtualMachineId}` campo no `{properties}` secção. Construa duas variáveis destes valores de campo, conforme mencionado abaixo.

- containerName = "iaasvmcontainer;"+`{name}`
- protectedItemName = "vm;"+ `{name}`
- `{virtualMachineId}` é utilizada mais adiante [o corpo do pedido](#example-request-body)

No exemplo, os valores acima traduzir para:

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Ativar a proteção para a VM do Azure

Depois da VM relevante é "em cache" e "identificada", selecione a política para proteger. Para saber mais sobre as políticas existentes no cofre, consulte [política de API de lista](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Em seguida, selecione o [política relevante](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) ao referir-se para o nome da política. Para criar políticas, consulte [criar tutorial política](backup-azure-arm-userestapi-createorupdatepolicy.md). "DefaultPolicy" está selecionado no exemplo abaixo.

Ativar a proteção é assíncrona *colocar* operação que cria um "item protegido".

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

O `{containerName}` e `{protectedItemName}` são como construído acima. O `{fabricName}` é "Azure". No nosso exemplo, isto traduz-se:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

#### <a name="create-the-request-body"></a>Criar o corpo do pedido

Para criar um item protegido, seguem-se os componentes do corpo do pedido.

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Propriedades de recurso de ProtectedItem         |

Para obter a lista completa das definições do corpo do pedido e outros detalhes, consulte [criar documentos de REST API do item protegido](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Corpo do pedido de exemplo

O corpo do pedido seguinte define as propriedades necessárias para criar um item protegido.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

O `{sourceResourceId}` é o `{virtualMachineId}` mencionadas acima a partir do [resposta suscetíveis de proteção de itens de lista](#example-responses-1).

#### <a name="responses"></a>Responses

A criação de um item protegido é um [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que esta operação cria outra operação que tem de ser registados em separado.

Devolve duas respostas: 202 (aceite) quando outra operação é criada e, em seguida, 200 (OK) quando essa operação é concluída.

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|Aceite 202     |         |     Aceite    |

##### <a name="example-responses"></a>Respostas de exemplo

Assim que submeter o *colocar* pedido para a criação de item protegido ou atualização, a resposta inicial é 202 (aceite) com um cabeçalho de localização ou o cabeçalho de async do Azure.

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

Em seguida, controlar a operação resultante com o cabeçalho de localização ou o cabeçalho do Azure-AsyncOperation com um simples *obter* comando.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

Depois de concluída a operação, devolverá 200 (OK) com o conteúdo do item protegido no corpo da resposta.

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

Isto confirma que a proteção está ativada para a VM e a primeira cópia de segurança será acionada de acordo com o agendamento de política.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Acionar uma cópia de segurança a pedido para uma VM protegida do Azure

Depois de uma VM do Azure está configurada para cópia de segurança, as cópias de segurança ocorrem de acordo com o agendamento de política. Pode aguardar que a primeira cópia de segurança agendada ou acionar uma cópia de segurança a pedido em qualquer altura. Período de retenção de cópias de segurança a pedido é separado da retenção da política de cópia de segurança e pode ser especificado para uma determinada data e hora. Se não for especificada, presume-se para ser 30 dias a partir do dia do acionador de cópia de segurança a pedido.

Acionar uma cópia de segurança a pedido é uma *POST* operação.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

O `{containerName}` e `{protectedItemName}` são como construído [acima](#responses-1). O `{fabricName}` é "Azure". No nosso exemplo, isto traduz-se:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>Criar o corpo do pedido

Para acionar uma cópia de segurança a pedido, seguem-se os componentes do corpo do pedido.

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Propriedades de BackupRequestResource         |

Para obter a lista completa das definições do corpo do pedido e outros detalhes, consulte [acionar cópias de segurança para o documento da REST API de itens protegidos](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Corpo do pedido de exemplo

O corpo do pedido seguinte define as propriedades necessárias para acionar uma cópia de segurança para um item protegido. Se o período de retenção não for especificado, será retida durante 30 dias a partir da hora de Acionador da tarefa de cópia de segurança.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Responses

Acionar uma cópia de segurança a pedido é uma [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que esta operação cria outra operação que tem de ser registados em separado.

Devolve duas respostas: 202 (aceite) quando outra operação é criada e, em seguida, 200 (OK) quando essa operação é concluída.

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|Aceite 202     |         |     Aceite    |

##### <a name="example-responses-3"></a>Respostas de exemplo

Assim que submeter o *POST* pedido para uma cópia de segurança a pedido, a resposta inicial é 202 (aceite) com um cabeçalho de localização ou o cabeçalho de async do Azure.

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

Em seguida, controlar a operação resultante com o cabeçalho de localização ou o cabeçalho do Azure-AsyncOperation com um simples *obter* comando.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

Depois de concluída a operação, devolverá 200 (OK) com o ID da tarefa de cópia de segurança resultante no corpo da resposta.

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

Uma vez que a tarefa de cópia de segurança é uma operação de longa execução, ele precisa ser controlado, conforme explicado no [monitorizar tarefas usando o documento da REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Modificar a configuração de cópia de segurança para uma VM protegida do Azure

### <a name="changing-the-policy-of-protection"></a>A alteração da política de proteção

Para alterar a política com a qual a VM está protegida, pode utilizar o mesmo formato que [ativar a proteção](#enabling-protection-for-the-azure-vm). Basta indicar o novo ID de política na [corpo do pedido](#example-request-body) e submeter o pedido. Para, por exemplo: Para alterar a política de testVM de 'DefaultPolicy' para "ProdPolicy", forneça o id de "ProdPolicy" no corpo do pedido.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

A resposta seguirá o mesmo formato, conforme mencionado [para ativar a proteção](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Parar a proteção, mas mantiver os dados existentes

Para remover a proteção numa VM protegida, mas manter os dados já uma cópia de segurança, remova a política no corpo do pedido e submeter o pedido. Assim que a associação com a política for removida, as cópias de segurança já não são ativadas e são criados novos pontos de recuperação.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

A resposta seguirá o mesmo formato, conforme mencionado [para acionar uma cópia de segurança a pedido](#example-responses-3). A tarefa resultante deve ser controlada, conforme explicado no [monitorizar tarefas usando o documento da REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Pare a proteção e eliminar dados

Para remover a proteção numa VM protegida e eliminar os dados de cópia de segurança também, realizar uma operação de eliminação conforme detalhado [aqui](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

A parar a proteção e a eliminação de dados é um *eliminar* operação.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

O `{containerName}` e `{protectedItemName}` são como construído [acima](#responses-1). `{fabricName}` é o "Azure". No nosso exemplo, isto traduz-se:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

### <a name="responses-2"></a>Respostas

*ELIMINAR* proteção é um [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que esta operação cria outra operação que tem de ser registados em separado.

Devolve duas respostas: 202 (aceite) quando outra operação é criada e, em seguida, 204 (NoContent) quando essa operação é concluída.

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|Aceite 202     |         |     Aceite    |

## <a name="next-steps"></a>Passos Seguintes

[Restaurar dados a partir de uma cópia de segurança de Máquina Virtual do Azure](backup-azure-arm-userestapi-restoreazurevms.md).

Para obter mais informações sobre as APIs de REST de cópia de segurança do Azure, consulte os seguintes documentos:

- [API de REST do fornecedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
