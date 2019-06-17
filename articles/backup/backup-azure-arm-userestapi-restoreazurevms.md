---
title: 'Cópia de segurança do Azure: Restaurar VMs do Azure com a REST API'
description: gerir as operações de restauro do Azure VM Backup com a REST API
services: backup
author: pvrk
manager: shivamg
keywords: API DE REST; Cópia de segurança VM do Azure; Restauro de VMS do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: pullabhk
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 4a65e8a855b9be797c1ceeacf4b74fea74697d00
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60646661"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Restaurar máquinas virtuais do Azure com a REST API

Depois de concluída a cópia de segurança de uma Máquina Virtual do Azure com o Azure Backup, um pode restaurar máquinas virtuais do Azure de todas ou discos ou ficheiros a partir da cópia de segurança mesmo. Este artigo descreve como restaurar uma VM do Azure ou discos com a REST API.

Para qualquer operação de restauro, é necessário identificar o ponto de recuperação relevantes pela primeira vez.

## <a name="select-recovery-point"></a>Selecione o ponto de recuperação

Os pontos de recuperação disponíveis de um item de cópia de segurança podem ser indicados, utilizando o [listar a REST API do ponto de recuperação](https://docs.microsoft.com/rest/api/backup/recoverypoints/list). É um simples *obter* operação com todos os valores relevantes.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2016-12-01
```

O `{containerName}` e `{protectedItemName}` são como construído [aqui](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` é o "Azure".

O *obter* URI tem todos os parâmetros necessários. Não é necessário para um corpo de pedido adicionais

### <a name="responses"></a>Responses

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Resposta de exemplo

Uma vez a *obter* URI é submetido, é devolvida uma resposta 200 (OK).

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

## <a name="restore-disks"></a>Restaurar discos

Se for necessário para personalizar a criação de uma VM a partir dos dados de cópia de segurança, um apenas pode restaurar discos para uma conta de armazenamento escolhida e criar uma VM a partir desses discos de acordo com seus requisitos. A conta de armazenamento deve estar na mesma região que o Cofre dos serviços de recuperação e não deve ser a redundância de zona. Os discos, bem como a configuração da cópia de segurança de VM ("vmconfig.json") será armazenada na conta de armazenamento específica.

O acionamento do restauro de discos é um *POST* pedido. Para saber mais sobre a operação de discos de restauro, veja a [API do REST de "acionar o restauro"](https://docs.microsoft.com/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2016-12-01
```

O `{containerName}` e `{protectedItemName}` são como construído [aqui](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` é o "Azure" e o `{recoveryPointId}` é o `{name}` campo dos pontos de recuperação mencionado [acima](#example-response).

### <a name="create-request-body"></a>Criar o corpo do pedido

Para acionar o restauro de discos de uma cópia de segurança de VM do Azure, seguem-se os componentes do corpo do pedido.

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Para obter a lista completa das definições do corpo do pedido e outros detalhes, consulte [acionar o documento da API do REST de restaurar](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Pedido de exemplo

O corpo do pedido seguinte define as propriedades necessárias para acionar o restauro de discos.

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

### <a name="response"></a>Resposta

O acionamento de um disco de restauro é um [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que esta operação cria outra operação que tem de ser registados em separado.

Devolve duas respostas: 202 (aceite) quando outra operação é criada e, em seguida, 200 (OK) quando essa operação é concluída.

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|Aceite 202     |         |     Aceite    |

#### <a name="example-responses"></a>Respostas de exemplo

Assim que submeter o *POST* URI para o acionamento do restauro de discos, a resposta inicial é 202 (aceite) com um cabeçalho de localização ou o cabeçalho de async do Azure.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Em seguida, controlar a operação resultante com o cabeçalho de localização ou o cabeçalho do Azure-AsyncOperation com um simples *obter* comando.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
```

Depois de concluída a operação, devolverá 200 (OK) com o ID da tarefa de restauro resultante no corpo da resposta.

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

Uma vez que a tarefa de cópia de segurança é uma operação de longa execução, ele deve haver um controle conforme explicado no [monitorizar tarefas usando o documento da REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

Depois de concluída a tarefa de longa execução, os discos e a configuração da máquina virtual com cópia de segurança ("VMConfig.json") estará presentes na conta de armazenamento específica.

## <a name="restore-as-another-virtual-machine"></a>Restaurar como outra máquina virtual

[Selecione o ponto de recuperação](#select-recovery-point) e criar o corpo do pedido conforme especificado abaixo para criar outra máquina Virtual do Azure com os dados a partir do ponto de recuperação.

O corpo do pedido seguinte define as propriedades necessárias para acionar o restauro de uma máquina virtual.

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
        "api-version": "2016-12-01",
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

A resposta deve ser processada da mesma forma como [explicado acima para restaurar discos](#response).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as APIs de REST de cópia de segurança do Azure, consulte os seguintes documentos:

- [API de REST do fornecedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)