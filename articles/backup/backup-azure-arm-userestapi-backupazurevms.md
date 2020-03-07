---
title: Back up VMs Azure usando REST API
description: Neste artigo, aprenda a configurar, iniciar e gerir operações de backup do Azure VM Backup utilizando a Rest API.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 4789ef1e0e09df521f8cab539d972e9e669e0a58
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395591"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Faça backup num Azure VM utilizando o Azure Backup via REST API

Este artigo descreve como gerir backups para um Azure VM usando O Backup Azure via REST API. Configure a proteção pela primeira vez para um VM Azure anteriormente desprotegido, desencadeie uma cópia de segurança a pedido de um VM Azure protegido e modifique as propriedades de backup de um VM apoiado através da API REST, como explicado aqui.

Consulte a criação de [abóbadas](backup-azure-arm-userestapi-createorupdatevault.md) e [crie](backup-azure-arm-userestapi-createorupdatepolicy.md) tutoriais de API DE REPOUSO para criar novos cofres e políticas.

Vamos assumir que pretende proteger um VM "testVM" no âmbito de um grupo de recursos "testRG" para um cofre de serviços de recuperação "testVault", presente no grupo de recursos "testVaultRG", com a política predefinida (denominada "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Configure a cópia de segurança para um Azure VM desprotegido utilizando a Rest API

### <a name="discover-unprotected-azure-vms"></a>Descubra VMs Azure desprotegidos

Primeiro, o cofre deve ser capaz de identificar o VM Azure. Isto é acionado com a operação de [atualização](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Trata-se de uma operação *postais* assíncronas que garante que o cofre obtém a mais recente lista de todos os VM desprotegidos na subscrição atual e 'caches' deles. Uma vez que o VM esteja 'cached', os serviços de recuperação poderão aceder ao VM e protegê-lo.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

O POST URI tem `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`, `{fabricName}` parâmetros. O `{fabricName}` é "Azure". De acordo com o nosso exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Como todos os parâmetros necessários são dados no URI, não há necessidade de um corpo de pedido separado.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Respostas

A operação "atualização" é uma [operação assíncrona.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada e depois 200 (OK) quando essa operação estiver concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|204 Sem Conteúdo     |         |  OK sem conteúdo devolvido      |
|202 Aceite     |         |     Aceite    |

##### <a name="example-responses"></a>Respostas de exemplo

Uma vez apresentado o pedido *do POST,* uma resposta de 202 (Aceite) é devolvida.

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
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Acompanhe a operação resultante utilizando o cabeçalho "Localização" com um simples comando *GET*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

Uma vez descobertos todos os VMs Azure, o comando GET devolve uma resposta 204 (Sem Conteúdo). O cofre é agora capaz de descobrir qualquer VM dentro da subscrição.

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

### <a name="selecting-the-relevant-azure-vm"></a>Selecionando o Azure VM relevante

 Pode confirmar que o "cache" é feito através da listagem de [todos os itens protegidos](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) ao abrigo da subscrição e localizar o VM desejado na resposta. [A resposta desta operação](#example-responses-1) também lhe dá informações sobre como os Serviços de Recuperação identificam um VM.  Uma vez familiarizado com o padrão, pode saltar este passo e proceder diretamente à [proteção.](#enabling-protection-for-the-azure-vm)

Esta operação é uma operação *GET.*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

O *GET* URI tem todos os parâmetros necessários. Não é necessário nenhum corpo de pedido adicional.

#### <a name="responses-1"></a>Respostas

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     | [Lista de Recursos DeItemProtectable de Carga de Carga](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses-1"></a>Respostas de exemplo

Uma vez que o pedido *GET* é submetido, uma resposta de 200 (OK) é devolvida.

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
> O número de valores numa resposta *GET* é limitado a 200 para uma 'página'. Utilize o campo 'nextLink' para obter o URL para o próximo conjunto de respostas.

A resposta contém a lista de todos os VMs Azure desprotegidos e cada `{value}` contém todas as informações necessárias pelo Serviço de Recuperação do Azure para configurar a cópia de segurança. Para configurar a cópia de segurança, note o campo `{name}` e o campo `{virtualMachineId}` na secção `{properties}`. Construa duas variáveis a partir destes valores de campo, como mencionado abaixo.

- nome do recipiente = "iaasvmcontainer;" +`{name}`
- protectedItemName = "vm;" + `{name}`
- `{virtualMachineId}` é usado mais tarde [no corpo de pedido](#example-request-body)

No exemplo, os valores acima traduzidos para:

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Habilitar a proteção para o Azure VM

Depois de o VM relevante ser "cached" e "identificado", selecione a política a proteger. Para saber mais sobre as políticas existentes no cofre, consulte a [lista Política API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Em [seguida,](/rest/api/backup/protectionpolicies/get) selecione a política relevante referindo-se ao nome da apólice. Para criar políticas, consulte a criação de [tutoriais políticos.](backup-azure-arm-userestapi-createorupdatepolicy.md) "DefaultPolicy" é selecionado no exemplo abaixo.

Permitir a proteção é uma operação *put* assíncrona que cria um "item protegido".

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Os `{containerName}` e `{protectedItemName}` são construídos acima. O `{fabricName}` é "Azure". Para o nosso exemplo, isto traduz-se em:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Criar o corpo de pedido

Para criar um item protegido, seguem-se os componentes do organismo de pedido.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|propriedades     | AzureIaaSVMProtectedItem        |Propriedades de recursos ProtegidosItem         |

Para a lista completa de definições do organismo de pedido e outros detalhes, consulte a criação do [documento rest API do artigo protegido](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Corpo de pedido de exemplo

O seguinte órgão de pedido define as propriedades necessárias para criar um item protegido.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

O `{sourceResourceId}` é o `{virtualMachineId}` acima mencionado da [resposta dos itens protegidos da lista.](#example-responses-1)

#### <a name="responses"></a>Respostas

A criação de um item protegido é uma [operação assíncrona.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada e depois 200 (OK) quando essa operação estiver concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 Aceite     |         |     Aceite    |

##### <a name="example-responses"></a>Respostas de exemplo

Uma vez apresentado o pedido *PUT* para criação ou atualização de itens protegidos, a resposta inicial é 202 (Aceite) com um cabeçalho de localização ou cabeçalho Azure-assync.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Em seguida, rastreie a operação resultante utilizando o cabeçalho de localização ou o cabeçalho da Operação Azure-Asynccom um simples comando *GET.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Uma vez concluída a operação, devolve 200 (OK) com o teor de item protegido no organismo de resposta.

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

Isto confirma que a proteção está ativada para o VM e que o primeiro backup será acionado de acordo com o calendário de apólices.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Desencadear um backup a pedido para um VM Azure protegido

Uma vez que um VM Azure é configurado para backup, os backups acontecem de acordo com o calendário de política. Pode esperar pelo primeiro backup agendado ou acionar um reforço a qualquer momento. A retenção de cópias de segurança a pedido é separada da retenção da política de backup e pode ser especificada para uma data-data específica. Se não for especificado, presume-se que seja a 30 dias do dia do gatilho do reforço a pedido.

Desencadear uma cópia de segurança a pedido é uma operação *POST.*

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

Os `{containerName}` e `{protectedItemName}` são tão construídos [acima.](#responses-1) O `{fabricName}` é "Azure". Para o nosso exemplo, isto traduz-se em:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>Criar o corpo de pedido

Para desencadear uma cópia de segurança a pedido, seguem-se os componentes do organismo de pedido.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|propriedades     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Propriedades de BackupRequestResource         |

Para obter a lista completa de definições do organismo de pedido e outros detalhes, consulte o gatilho de cópias de [segurança para itens protegidos REST API documento](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Corpo de pedido de exemplo

O seguinte órgão de pedido define as propriedades necessárias para desencadear uma cópia de segurança para um item protegido. Se a retenção não for especificada, será retida durante 30 dias a partir do momento do gatilho do trabalho de reserva.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Respostas

Desencadear uma cópia de segurança a pedido é uma [operação assíncrona.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada e depois 200 (OK) quando essa operação estiver concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|202 Aceite     |         |     Aceite    |

#### <a name="example-responses-3"></a>Respostas de exemplo

Uma vez apresentado o pedido *de post* para um backup a pedido, a resposta inicial é 202 (Aceito) com um cabeçalho de localização ou cabeçalho Azure-async.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Em seguida, rastreie a operação resultante utilizando o cabeçalho de localização ou o cabeçalho da Operação Azure-Asynccom um simples comando *GET.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Uma vez concluída a operação, devolve 200 (OK) com a identificação do trabalho de backup resultante no organismo de resposta.

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

Uma vez que o trabalho de reserva é uma operação de longa duração, tem de ser rastreado, tal como explicado nos trabalhos de [monitorização utilizando o documento REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Modificar a configuração de backup para um VM Azure protegido

### <a name="changing-the-policy-of-protection"></a>Alteração da política de proteção

Para alterar a política com a qual o VM está protegido, pode utilizar o mesmo formato que [permitir a proteção](#enabling-protection-for-the-azure-vm). Basta fornecer a nova identificação [política no órgão de pedido](#example-request-body) e submeter o pedido. Por exemplo: Para alterar a política de testeVM de 'DefaultPolicy' para 'ProdPolicy', fornecer o ID 'ProdPolicy' no organismo de pedido.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

A resposta seguirá o mesmo formato que mencionado [para permitir a proteção](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Parar a proteção, mas reter os dados existentes

Para remover a proteção de um VM protegido, mas reter os dados já apoiados, remova a política no órgão de pedido e submeta o pedido. Uma vez removida a associação com a política, os backups já não são desencadeados e não são criados novos pontos de recuperação.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

A resposta seguirá o mesmo formato que mencionado [para desencadear uma cópia de segurança a pedido.](#example-responses-3) O trabalho resultante deve ser acompanhado, tal como explicado nos trabalhos de [controlo utilizando o documento REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Parar a proteção e eliminar dados

Para remover a proteção num VM protegido e eliminar também os dados de cópia de segurança, execute uma operação de eliminação conforme descrito [aqui](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

Parar a proteção e eliminar dados é uma operação *DELETE.*

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Os `{containerName}` e `{protectedItemName}` são tão construídos [acima.](#responses-1) `{fabricName}` é "Azure". Para o nosso exemplo, isto traduz-se em:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-2"></a>Respostas

*A* proteção DELETE é uma [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada e depois 204 (NoContent) quando essa operação estiver concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 Aceite     |         |     Aceite    |

> [!IMPORTANT]
> Para proteger contra cenários de eliminação acidental, existe uma [funcionalidade de eliminação suave disponível](use-restapi-update-vault-properties.md#soft-delete-state) para o cofre dos serviços de recuperação. Se o estado de eliminação suave do cofre estiver programado para ativar, então a operação de eliminação NÃO eliminará imediatamente os dados. Será mantido por 14 dias e depois permanentemente purgado. O cliente não é cobrado pelo armazenamento para este período de 14 dias. Para desfazer a operação de eliminação, consulte a [secção de desapagamento .](#undo-the-stop-protection-and-delete-data)

### <a name="undo-the-stop-protection-and-delete-data"></a>Desfazer a proteção stop e apagar dados

Desfazer a eliminação acidental é semelhante a criar o item de reserva. Depois de desfazer a eliminação, o item é retido, mas não são acionados futuros backups.

A eliminação do desfazer é uma operação *PUT* muito semelhante à [alteração da política](#changing-the-policy-of-protection) e/ou à [proteção](#enabling-protection-for-the-azure-vm). Basta fornecer a intenção de desfazer a eliminação com a variável *isRehydrate* no órgão de [pedido](#example-request-body) e submeter o pedido. Por exemplo: Para desfazer a supressão do testeVM, deve ser utilizado o seguinte organismo de pedido.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

A resposta seguirá o mesmo formato que mencionado [para desencadear uma cópia de segurança a pedido.](#example-responses-3) O trabalho resultante deve ser acompanhado, tal como explicado nos trabalhos de [controlo utilizando o documento REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Passos seguintes

[Restaurar os dados de uma cópia de segurança da máquina Azure Virtual.](backup-azure-arm-userestapi-restoreazurevms.md)

Para obter mais informações sobre as APIs DE REPOUSO DE Backup Azure, consulte os seguintes documentos:

- [Prestador de serviços de recuperação azure REST API](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
