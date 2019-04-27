---
title: 'Cópia de segurança do Azure: Gerir tarefas de cópia de segurança com a REST API'
description: gerir cópias de segurança e restaurar trabalhos de Backup do Azure com a REST API
services: backup
author: pvrk
manager: shivamg
keywords: API DE REST; Cópia de segurança VM do Azure; Restauro de VMS do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: eb8b7dc77d180eb56c2585e93e60a36742f6c84c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646627"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Controlar as tarefas de cópia de segurança e restauro com a REST API

O serviço de cópia de segurança do Azure aciona tarefas que são executadas em segundo plano em vários cenários, como acionar a cópia de segurança, restaurar as operações, desativar a cópia de segurança. Estas tarefas podem ser controladas com suas IDs.

## <a name="fetch-job-information-from-operations"></a>Obter informações da tarefa de operações

Uma operação como acionar a cópia de segurança devolverá sempre uma jobID. Para, por exemplo: A resposta final de um [acionar a operação de REST API de cópia de segurança](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3) é o seguinte:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

A tarefa de cópia de segurança de VM do Azure é identificada pelo campo "jobId" e podem ser controlada, como mencionado [aqui](https://docs.microsoft.com/rest/api/backup/jobdetails/) usando uma simples *obter* pedido.

## <a name="tracking-the-job"></a>A tarefa de controlo

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2017-07-01
```

O `{jobName}` "jobId" é mencionado acima. A resposta é sempre 200 OK com o campo de "status" indicando o estado atual da tarefa. Assim que é "Concluído" ou "CompletedWithWarnings", a seção 'extendedInfo' revela mais detalhes sobre a tarefa.

### <a name="response"></a>Resposta

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     | [JobResource](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Resposta de exemplo

Uma vez a *obter* URI é submetido, é devolvida uma resposta 200 (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```