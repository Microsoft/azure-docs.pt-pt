---
title: Gerir trabalhos de backup usando a API REST
description: Neste artigo, aprenda a rastrear e gerir backups e restaurar os trabalhos de Backup Azure usando a Rest API.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: 628569c547aa776ec2fbb7ec7e32edad7c1fe7dd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273530"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Rastrear backup e restaurar postos de trabalho usando a API REST

O serviço de backup Azure desencadeia trabalhos que funcionam em segundo plano em vários cenários, tais como desencadear backup, restaurar operações, desativar o backup. Estes trabalhos podem ser rastreados usando as suas identificações.

## <a name="fetch-job-information-from-operations"></a>Buscar informações sobre emprego das operações

Uma operação como o dedisparomento de cópia de segurança irá sempre devolver um JOBID. Por exemplo: A resposta final de uma operação de [backup de gatilho REST API](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3) é a seguinte:

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

O trabalho de backup Azure VM é identificado por campo "jobId" e pode ser rastreado como mencionado [aqui](https://docs.microsoft.com/rest/api/backup/jobdetails/) usando um simples pedido *GET.*

## <a name="tracking-the-job"></a>Rastreando o trabalho

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

O `{jobName}` é "jobId" mencionado acima. A resposta é sempre de 200 OK com o campo "status" indicando o estado atual do trabalho. Uma vez "Concluído" ou "CompletedWithWarnings", a secção 'ExtendedInfo' revela mais detalhes sobre o trabalho.

### <a name="response"></a>Resposta

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     | [Recurso de Emprego](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Resposta de exemplo

Uma vez que o *GET* URI é submetido, uma resposta de 200 (OK) é devolvida.

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
