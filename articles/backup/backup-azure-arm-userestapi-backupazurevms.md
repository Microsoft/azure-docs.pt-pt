---
title: Back up VMs Azure usando REST API
description: Neste artigo, aprenda a configurar, iniciar e gerir operações de backup da Azure VM Backup utilizando a API REST.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 9ba22c51c7a6c26a232ed20aec21fc83d2c54b37
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171452"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Faça backup do Azure VM usando Azure Backup via REST API

Este artigo descreve como gerir backups para um Azure VM usando Azure Backup via REST API. Proteção de configuração pela primeira vez para um Azure VM anteriormente desprotegido, desencadeie uma cópia de segurança a pedido de um Azure VM protegido e modifique propriedades de backup de um VM apoiado através da API REST, como explicado aqui.

Consulte para [criar cofre](backup-azure-arm-userestapi-createorupdatevault.md) e criar tutoriais de API [de política](backup-azure-arm-userestapi-createorupdatepolicy.md) REST para criar novos cofres e políticas.

Vamos supor que pretende proteger um VM "testVM" sob um grupo de recursos "testRG" para um cofre de Serviços de Recuperação "testVault", presente no grupo de recursos "testVaultRG", com a política padrão (denominada "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Configure backup para um VM Azure desprotegido usando REST API

### <a name="discover-unprotected-azure-vms"></a>Descubra VMs Azure desprotegidos

Primeiro, o cofre deve ser capaz de identificar o Azure VM. Isto é acionado utilizando a [operação de atualização](/rest/api/backup/protectioncontainers/refresh). É uma operação *POST*  assíncronea que garante que o cofre recebe a mais recente lista de todos os VM desprotegidos na subscrição atual e 'caches' deles. Uma vez que o VM é 'cached', os serviços de recuperação poderão aceder ao VM e protegê-lo.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

O POST URI `{subscriptionId}` `{vaultName}` tem, `{vaultresourceGroupName}` , `{fabricName}` parâmetros. O `{fabricName}` é "Azure". De acordo com o nosso exemplo, `{vaultName}` é "testVault" e `{vaultresourceGroupName}` é "testVaultRG". Como todos os parâmetros necessários são dados no URI, não há necessidade de um corpo de pedido separado.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses-to-refresh-operation"></a>Respostas à operação de atualização

A operação "refresh" é uma [operação assíncronea.](../azure-resource-manager/management/async-operations.md) Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada e depois 200 (OK) quando essa operação estiver concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|204 Sem Conteúdo     |         |  OK sem nenhum conteúdo devolvido      |
|202 Aceito     |         |     Aceite    |

##### <a name="example-responses-to-refresh-operation"></a>Respostas de exemplo para atualização de funcionamento

Uma vez apresentado o pedido *do CORREIO,* é devolvida uma resposta 202 (Aceite).

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

 Pode confirmar que o "caching" é feito [listando todos os itens protegidos](/rest/api/backup/backupprotectableitems/list) sob a subscrição e localizar o VM desejado na resposta. [A resposta desta operação](#example-responses-to-get-operation) também lhe dá informações sobre como os Serviços de Recuperação identificam um VM.  Uma vez que esteja familiarizado com o padrão, pode saltar este passo e proceder diretamente à [proteção ativa.](#enabling-protection-for-the-azure-vm)

Esta operação é uma operação *GET.*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

O *GET* URI tem todos os parâmetros necessários. Não é necessário nenhum corpo de pedido adicional.

#### <a name="responses-to-get-operation"></a>Respostas para obter operação

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses-to-get-operation"></a>Respostas de exemplo para obter operação

Uma vez que o pedido *GET* é apresentado, uma resposta de 200 (OK) é devolvida.

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
> O número de valores numa resposta *GET* está limitado a 200 para uma 'página'. Utilize o campo 'nextLink' para obter o URL para o próximo conjunto de respostas.

A resposta contém a lista de todos os VMs Azure desprotegidos e cada um `{value}` contém todas as informações necessárias pelo Serviço de Recuperação Azure para configurar a cópia de segurança. Para configurar a cópia de segurança, observe o `{name}` campo e o campo na `{virtualMachineId}` `{properties}` secção. Construa duas variáveis a partir destes valores de campo, como mencionado abaixo.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "vm;" + `{name}`
- `{virtualMachineId}` é usado mais tarde [no corpo de pedido](#example-request-body)

No exemplo, os valores acima traduzidos traduzem-se em:

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Habilitação de proteção para o VM Azure

Depois de o VM relevante ser "cached" e "identificado", selecione a política para proteger. Para saber mais sobre as políticas existentes no cofre, consulte a [Lista API](/rest/api/backup/backuppolicies/list)política . Em seguida, selecione a [política relevante](/rest/api/backup/protectionpolicies/get) referindo-se ao nome da apólice. Para criar políticas, consulte a [criação de tutoriais políticos.](backup-azure-arm-userestapi-createorupdatepolicy.md) "DefaultPolicy" é selecionado no exemplo abaixo.

Permitir a proteção é uma operação *PUT* assíncronea que cria um "item protegido".

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Os `{containerName}` e `{protectedItemName}` são construídos acima. O `{fabricName}` é "Azure". Para o nosso exemplo, isto traduz-se em:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Criar o corpo de pedido

Para criar um item protegido, seguem-se os componentes do corpo de pedido.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|propriedades     | AzureIaaSVMProtectedItem        |Propriedades de recursos ProtectEdItem         |

Para obter a lista completa das definições do organismo de pedido e outros detalhes, consulte a [criação do documento API do artigo de proteção](/rest/api/backup/protecteditems/createorupdate#request-body).

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

O `{sourceResourceId}` que é mencionado acima da resposta de `{virtualMachineId}` [itens protegidos da lista](#example-responses-to-get-operation).

#### <a name="responses-to-create-protected-item-operation"></a>Respostas para criar operação de artigo protegido

A criação de um item protegido é uma [operação assíncronea.](../azure-resource-manager/management/async-operations.md) Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada e depois 200 (OK) quando essa operação estiver concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |    [ProtectEdItemResource](/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 Aceito     |         |     Aceite    |

##### <a name="example-responses-to-create-protected-item-operation"></a>Respostas de exemplo para criar operação de artigo protegido

Uma vez que envie o pedido *PUT* para criação ou atualização de artigos protegidos, a resposta inicial é 202 (Aceite) com um cabeçalho de localização ou cabeçalho Azure-async.

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

Em seguida, rastreia a operação resultante utilizando o cabeçalho de localização ou Azure-AsyncOperation cabeçalho com um simples comando *GET.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Uma vez concluída a operação, devolve 200 (OK) com o conteúdo do artigo protegido no organismo de resposta.

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

Isto confirma que a proteção está ativada para o VM e que o primeiro backup será acionado de acordo com o calendário da política.

### <a name="excluding-disks-in-azure-vm-backup"></a>Excluindo os discos na cópia de segurança Azure VM

O Azure Backup também fornece uma forma de fazer uma cópia de segurança seletiva de um subconjunto de discos em Azure VM. Mais detalhes são fornecidos [aqui.](selective-disk-backup-restore.md) Se pretender fazer uma cópia de segurança seletiva de alguns discos durante a proteção, o seguinte corte de código deve ser o [órgão de pedido durante a proteção ativante](#example-request-body).

```json
{
"properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "extendedProperties":  {
      "diskExclusionProperties":{
          "diskLunList":[0,1],
          "isInclusionList":true
        }
    }
}
}
```

No órgão de pedido acima, a lista de discos a apoiar é fornecida na secção de propriedades estendidas.

|Propriedade  |Valor  |
|---------|---------|
|diskLunList     | A lista LUN em disco é uma lista de *LUNs de discos de dados.* **O disco de so é sempre apoiado e não precisa de ser mencionado.**        |
|IsInclusionList     | Deve ser **verdade** para os LUNs serem incluídos durante o backup. Se for **falso,** os REFERIDOS LUNs serão excluídos.         |

Portanto, se a exigência é fazer backup apenas do disco de so, então _todos os_ discos de dados devem ser excluídos. Uma maneira mais fácil é dizer que nenhum disco de dados deve ser incluído. Assim, a lista lun disco estará vazia e a **IsInclusionList** será **verdadeira**. Da mesma forma, pense na forma mais fácil de selecionar um subconjunto: Alguns discos devem ser sempre excluídos ou alguns discos devem ser sempre incluídos. Escolha a lista LUN e o valor variável boolean em conformidade.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Desencadear uma cópia de segurança a pedido de um Azure VM protegido

Uma vez que um Azure VM é configurado para cópia de segurança, as cópias de segurança acontecem de acordo com o calendário da apólice. Pode esperar pelo primeiro backup programado ou desencadear uma cópia de segurança a pedido a qualquer momento. A retenção para cópias de segurança a pedido é separada da retenção da política de backup e pode ser especificada para uma determinada data. Se não for especificado, presume-se que esteja a 30 dias do dia do desencadeamento do backup a pedido.

Desencadear uma cópia de segurança a pedido é uma operação *POST.*

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

Os `{containerName}` e `{protectedItemName}` são construídos [acima.](#responses-to-get-operation) O `{fabricName}` é "Azure". Para o nosso exemplo, isto traduz-se em:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body-for-on-demand-backup"></a>Crie o corpo de pedido para backup a pedido

Para desencadear uma cópia de segurança a pedido, seguem-se os componentes do corpo de pedido.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|propriedades     | [IaaSVMBackupRequest](/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource propriedades         |

Para obter a lista completa das definições do organismo de pedido e outros detalhes, consulte as [cópias de segurança para itens protegidos REST Documento API](/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body-for-on-demand-backup"></a>Órgão de pedido de exemplo para backup a pedido

O seguinte órgão de pedido define as propriedades necessárias para acionar uma cópia de segurança para um item protegido. Se a retenção não for especificada, será mantida por 30 dias a partir do momento do desencadeamento do trabalho de reserva.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses-for-on-demand-backup"></a>Respostas para backup a pedido

Desencadear uma cópia de segurança a pedido é uma [operação assíncronea.](../azure-resource-manager/management/async-operations.md) Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada e depois 200 (OK) quando essa operação estiver concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|202 Aceito     |         |     Aceite    |

#### <a name="example-responses-for-on-demand-backup"></a>Respostas de exemplo para backup a pedido

Uma vez que envie o pedido *DEM* para um backup a pedido, a resposta inicial é 202 (Aceito) com um cabeçalho de localização ou cabeçalho Azure-async.

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

Em seguida, rastreia a operação resultante utilizando o cabeçalho de localização ou Azure-AsyncOperation cabeçalho com um simples comando *GET.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Uma vez concluída a operação, retorna 200 (OK) com o ID do trabalho de backup resultante no organismo de resposta.

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

Uma vez que o trabalho de backup é uma operação de longa duração, tem de ser rastreado como explicado nos trabalhos de monitorização utilizando o [documento REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Modifique a configuração de backup para um Azure VM protegido

### <a name="changing-the-policy-of-protection"></a>Alteração da política de proteção

Para alterar a política com a qual o VM está protegido, pode utilizar o mesmo formato [que permitir a proteção](#enabling-protection-for-the-azure-vm). Basta fornecer a nova identificação de política [no órgão de pedido](#example-request-body) e apresentar o pedido. Por exemplo: Para alterar a política de testVM de 'DefaultPolicy' para 'ProdPolicy', forneça o ID 'ProdPolicy' no organismo de pedido.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

A resposta seguirá o mesmo formato mencionado [para permitir a proteção](#responses-to-create-protected-item-operation)

#### <a name="excluding-disks-during-azure-vm-protection"></a>Excluindo os discos durante a proteção Azure VM

Se o VM Azure já estiver apoiado, pode especificar a lista de discos a apoiar ou excluir alterando a política de proteção. Basta preparar o pedido no mesmo formato que [excluindo discos durante a proteção](#excluding-disks-in-azure-vm-backup)

> [!IMPORTANT]
> O órgão de pedido acima é sempre a cópia final dos discos de dados a excluir ou incluído. Isto não *aumenta* a configuração anterior. Por exemplo: Se atualizar a proteção como "excluir o disco de dados 1" e depois repetir com "excluir o disco de dados 2", apenas o *disco de dados 2 está excluído* nas cópias de segurança subsequentes e o disco de dados 1 será incluído. Esta é sempre a lista final que será incluída/excluída nas cópias de segurança subsequentes.

Para obter a lista atual de discos excluídos ou incluídos, obtenha as informações de produto protegido, conforme [mencionado aqui.](/rest/api/backup/protecteditems/get) A resposta fornecerá a lista de LUNs de disco de dados e indica se estão incluídas ou excluídas.

### <a name="stop-protection-but-retain-existing-data"></a>Parar a proteção, mas reter os dados existentes

Para remover a proteção de um VM protegido, mas reter os dados já apoiados, remova a política no órgão de pedido e submeta o pedido. Uma vez que a associação com a política é removida, os backups já não são desencadeados e não são criados novos pontos de recuperação.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

A resposta seguirá o mesmo formato mencionado [para desencadear uma cópia de segurança a pedido](#example-responses-for-on-demand-backup). O trabalho resultante deve ser acompanhado, tal como explicado nos postos de controlo, utilizando o [documento REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Parar a proteção e apagar dados

Para remover a proteção de um VM protegido e eliminar os dados de cópia de segurança também, execute uma operação de eliminação conforme detalhado [aqui](/rest/api/backup/protecteditems/delete).

Parar a proteção e eliminar dados é uma operação *DELETE.*

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Os `{containerName}` e `{protectedItemName}` são construídos [acima.](#responses-to-get-operation) `{fabricName}` é "Azure". Para o nosso exemplo, isto traduz-se em:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-for-delete-protection"></a>Respostas para eliminar a proteção

*A* proteção DELETE é uma [operação assíncronea](../azure-resource-manager/management/async-operations.md). Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada e, em seguida, 204 (NoContent) quando essa operação estiver concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 Aceito     |         |     Aceite    |

> [!IMPORTANT]
> Para proteger contra cenários de eliminação acidental, existe uma [funcionalidade de eliminação suave disponível](use-restapi-update-vault-properties.md#soft-delete-state) para o cofre dos Serviços de Recuperação. Se o estado de eliminação suave do cofre estiver definido para ativar, a operação de eliminação não apagará imediatamente os dados. Será mantido por 14 dias e depois permanentemente purgado. Não é cobrado para armazenamento por este período de 14 dias. Para desfazer a operação de eliminação, consulte a [secção de eliminação](#undo-the-deletion).

### <a name="undo-the-deletion"></a>Desfazer a eliminação

Desfazer a eliminação acidental é semelhante à criação do item de reserva. Depois de desfazer a eliminação, o item é retido, mas não são acionados futuros backups.

A eliminação de desfazer é uma operação *PUT* muito semelhante a [alterar a política](#changing-the-policy-of-protection) e/ou permitir a [proteção](#enabling-protection-for-the-azure-vm). Basta fornecer a intenção de desfazer a supressão com a variável *é Reidratar*  no [órgão de pedido](#example-request-body) e apresentar o pedido. Por exemplo: Para desfazer a supressão para o testVM, deve ser utilizado o seguinte organismo de pedido.

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

A resposta seguirá o mesmo formato mencionado [para desencadear uma cópia de segurança a pedido](#example-responses-for-on-demand-backup). O trabalho resultante deve ser acompanhado, tal como explicado nos postos de controlo, utilizando o [documento REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Passos seguintes

[Restaurar os dados de uma cópia de segurança da máquina Virtual Azure](backup-azure-arm-userestapi-restoreazurevms.md).

Para obter mais informações sobre as APIs de backup Azure, consulte os seguintes documentos:

- [Prestador de Serviços de Recuperação Azure REST API](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)