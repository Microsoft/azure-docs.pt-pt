---
title: Restaurar VMs Azure usando REST API
description: Neste artigo, aprenda a gerir as operações de restauro da Cópia de Segurança Virtual da Azure utilizando a API REST.
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 260c78af39c46e493ebb79c26ff1c55153a41c1d
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174030"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Restaurar máquinas virtuais Azure usando REST API

Uma vez concluída a cópia de segurança de uma máquina virtual Azure que utiliza o Azure Backup, pode-se restaurar máquinas ou discos ou ficheiros Azure Inteiros a partir da mesma cópia de cópia de segurança. Este artigo descreve como restaurar um VM Azure ou discos usando REST API.

Para qualquer operação de restauro, primeiro é preciso identificar o ponto de recuperação relevante.

## <a name="select-recovery-point"></a>Selecione ponto de recuperação

Os pontos de recuperação disponíveis de um item de backup podem ser listados usando o ponto de recuperação da [lista REST API](/rest/api/backup/recoverypoints/list). É uma simples operação *GET* com todos os valores relevantes.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

Os `{containerName}` e `{protectedItemName}` são construídos [aqui.](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation) `{fabricName}` é "Azure".

O *GET* URI tem todos os parâmetros necessários. Não há necessidade de um corpo de pedido adicional.

### <a name="responses"></a>Respostas

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Resposta de exemplo

Uma vez que o *GET* URI é submetido, uma resposta de 200 (OK) é devolvida.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

O ponto de recuperação é identificado com o `{name}` campo na resposta acima.

## <a name="restore-operations"></a>Restaurar operações

Depois de selecionar o [ponto de restauro relevante,](#select-recovery-point)proceda ao acionamento da operação de restauro.

***Todas as operações de restauro no item de backup são realizadas com a mesma *API POST.* Apenas o corpo pedido muda com os cenários de restauro.***

> [!IMPORTANT]
> Todos os detalhes sobre várias opções de restauro e suas dependências são mencionados [aqui.](./backup-azure-arm-restore-vms.md#restore-options) Por favor, reveja antes de começar a desencadear estas operações.

Desencadear operações de restauro é um pedido *de CORREIO.* Para saber mais sobre a API, consulte a [API DE "gatilho restaurador".](/rest/api/backup/restores/trigger)

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Os `{containerName}` e `{protectedItemName}` são construídos [aqui.](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation) `{fabricName}` é "Azure" e `{recoveryPointId}` é o campo do ponto de `{name}` recuperação [acima](#example-response)mencionado .

Uma vez obtido o ponto de recuperação, precisamos de construir o corpo de pedido para o cenário de restauro relevante. As secções seguintes descrevem o corpo de pedido para cada cenário.

- [Restaurar discos](#restore-disks)
- [Substituir discos](#replace-disks-in-a-backed-up-virtual-machine)
- [Restaurar como uma nova máquina virtual](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>Restaurar resposta

O desencadeamento de qualquer operação de restauro é uma [operação assíncronea](../azure-resource-manager/management/async-operations.md). Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada e depois 200 (OK) quando essa operação estiver concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|202 Aceito     |         |     Aceite    |

#### <a name="example-responses"></a>Respostas de exemplo

Uma vez submetida o *POST* URI para ativar os discos de restauro, a resposta inicial é 202 (Aceite) com um cabeçalho de localização ou cabeçalho Azure-async.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Em seguida, rastreia a operação resultante utilizando o cabeçalho de localização ou Azure-AsyncOperation cabeçalho com um simples comando *GET.*

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Uma vez concluída a operação, retorna 200 (OK) com o ID do trabalho de restauro resultante no organismo de resposta.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Uma vez que o trabalho de restauro é uma operação de longa duração, deve ser rastreado como explicado nos trabalhos de monitorização utilizando o [documento REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="restore-disks"></a>Restaurar discos

Se houver necessidade de personalizar a criação de um VM a partir dos dados de backup, pode simplesmente restaurar os discos numa conta de armazenamento escolhida e criar um VM a partir desses discos de acordo com os seus requisitos. A conta de armazenamento deve estar na mesma região que o cofre dos Serviços de Recuperação e não deve ser redundante. Os discos, bem como a configuração do VM de reserva ("vmconfig.jsligado"), serão armazenados na conta de armazenamento dada. Como [explicado acima,](#restore-operations)o organismo de pedido relevante para restaurar os discos é fornecido abaixo.

#### <a name="create-request-body"></a>Criar corpo de pedido

Para ativar uma restauração do disco a partir de uma cópia de segurança Azure VM, seguem-se os componentes do corpo de pedido.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|propriedades     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestaurarRequestResourceProperties     |

Para obter a lista completa das definições do organismo de pedido e outros detalhes, consulte o [documento API do Restore REST](/rest/api/backup/restores/trigger#request-body).

##### <a name="example-request"></a>Pedido de exemplo

O seguinte pedido do corpo define as propriedades necessárias para desencadear uma restauração do disco.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>Restaurar os discos seletivamente

Se estiver [a fazer o backup seletivo](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)dos discos, a lista de discos com suporte atual é fornecida no resumo do ponto de [recuperação](#select-recovery-point) e [na resposta detalhada](/rest/api/backup/recoverypoints/get). Também pode restaurar seletivamente os discos e mais detalhes são fornecidos [aqui.](selective-disk-backup-restore.md#selective-disk-restore) Para restaurar seletivamente um disco entre a lista de discos retroseridos, encontre o LUN do disco a partir da resposta do ponto de recuperação e adicione a propriedade **restauradaDiskLunList** ao [corpo de pedido acima,](#example-request) como mostrado abaixo.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

Uma vez que rastreia a resposta como [explicado acima](#responses), e o trabalho de longa duração está completo, os discos e a configuração da máquina virtual de backed ("VMConfig.json") estarão presentes na conta de armazenamento dada.

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>Substitua os discos numa máquina virtual com apoio

Enquanto os discos de restauração criam discos a partir do ponto de recuperação, substitua os discos de substituição dos discos atuais do VM de arranque com os discos do ponto de recuperação. Tal como [explicado acima,](#restore-operations)o organismo de pedidos relevante para a substituição dos discos é fornecido abaixo.

#### <a name="create-request-body"></a>Criar corpo de pedido

Para ativar uma substituição de disco de uma cópia de segurança Azure VM, seguem-se os componentes do corpo de pedido.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|propriedades     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestaurarRequestResourceProperties     |

Para obter a lista completa das definições do organismo de pedido e outros detalhes, consulte o [documento API do Restore REST](/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Pedido de exemplo

O seguinte pedido do corpo define as propriedades necessárias para desencadear uma restauração do disco.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>Restaurar como outra máquina virtual

Como [explicado acima,](#restore-operations)o corpo de pedido que se segue define as propriedades necessárias para desencadear uma restauração da máquina virtual.

```json
{
  "parameters": {
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "vaultName": "testVault",
        "fabricName": "Azure",
        "containerName": "IaasVMContainer;iaasvmcontainerv2;testRG;testVM",
        "protectedItemName": "VM;iaasvmcontainerv2;testRG;testVM",
        "recoveryPointId": "348916168024334",
        "api-version": "2019-05-13",
      "parameters": {
        "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
        }
      }
    }
}
```

A resposta deve ser tratada da mesma forma [que explica acima para restaurar os discos.](#responses)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as APIs de backup Azure, consulte os seguintes documentos:

- [Prestador de Serviços de Recuperação Azure REST API](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)