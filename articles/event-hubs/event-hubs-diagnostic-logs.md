---
title: Configurar registos de diagnóstico - Hub de eventos do Azure | Documentos da Microsoft
description: Saiba como configurar registos de atividades e os registos de diagnóstico dos hubs de eventos no Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c8f2dba8ff30ceae4085d96640623a01b6784b1e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957526"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Configurar registos de diagnóstico para um hub de eventos do Azure

Pode ver dois tipos de registos para os Hubs de eventos do Azure:

* **[Registos de atividades](../azure-monitor/platform/activity-logs-overview.md)**: Estes registos têm informações sobre as operações executadas numa tarefa. Os registos são sempre ativados.
* **[Os registos de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md)**: Pode configurar os registos de diagnóstico para uma vista mais avançada de tudo o que acontece com uma tarefa. Atividades de capa registos de diagnóstico desde o momento, que a tarefa é criada até que o trabalho é eliminado, incluindo atualizações e as atividades que ocorrem enquanto a tarefa está em execução.

## <a name="enable-diagnostic-logs"></a>Ativar registos de diagnóstico

Os registos de diagnóstico estão desativados por predefinição. Para ativar os registos de diagnóstico, siga estes passos:

1.  Na [portal do Azure](https://portal.azure.com), em **monitorização + gestão**, clique em **registos de diagnóstico**.

    ![Navegação do painel para registos de diagnóstico](./media/event-hubs-diagnostic-logs/image1.png)

2.  Clique em recursos que pretende monitorizar.

3.  Clique em **Ativar diagnósticos**.

    ![Ativar registos de diagnóstico](./media/event-hubs-diagnostic-logs/image2.png)

4.  Para **Status**, clique em **no**.

    ![Alterar o estado dos registos de diagnóstico](./media/event-hubs-diagnostic-logs/image3.png)

5.  Definir o destino de arquivo que pretende; Por exemplo, uma conta de armazenamento, um hub de eventos ou do Azure Monitor registos.

6.  Guarde as novas definições de diagnóstico.

Novas definições entrem em vigor em cerca de 10 minutos. Depois disso, registos de constar no destino arquivamento configurado, o **registos de diagnóstico** painel.

Para obter mais informações sobre como configurar diagnósticos, consulte a [descrição geral dos registos de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Categorias de registos de diagnóstico

Os Hubs de eventos captura os registos de diagnóstico para duas categorias:

* **Arquivar registos**: registos relacionados com os Hubs de eventos de arquivos, especificamente, os registos relacionados com erros de arquivo.
* **Registos operacionais**: informações sobre o que está a acontecer durante as operações de Hubs de eventos, especificamente, o tipo de operação, incluindo a criação do hub de eventos, os recursos utilizados e o estado da operação.

## <a name="diagnostic-logs-schema"></a>Esquema de registos de diagnóstico

Todos os registos são armazenados no formato de JavaScript Object Notation (JSON). Cada entrada tem campos de cadeia de caracteres que utilizam o formato descrito nas seções a seguir.

### <a name="archive-logs-schema"></a>Esquema de registos de arquivo

Cadeias de caracteres do arquivo log JSON incluem elementos listados na tabela a seguir:

Nome | Descrição
------- | -------
TaskName | Descrição da tarefa que falhou.
ActivityId | ID interno, utilizado para o controlo.
trackingId | ID interno, utilizado para o controlo.
resourceId | ID de recurso de Gestor de recursos do Azure.
eventHub | Hub de eventos nome completo (inclui o espaço de nomes).
partitionId | Partição do Hub de eventos a ser escrita.
archiveStep | ArchiveFlushWriter
startTime | Hora de início de falha.
falhas | Número de vezes que ocorreu uma falha.
durationInSeconds | Duração de falha.
message | Mensagem de erro.
categoria | ArchiveLogs

O código a seguir é um exemplo de um cadeia de caracteres do JSON de Arquivar registo:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>Esquema de registos operacionais

Cadeias de caracteres JSON de registo operacional incluem elementos listados na tabela a seguir:

Nome | Descrição
------- | -------
ActivityId | ID interno, utilizado para controlar o objetivo.
EventName | Nome da operação.  
resourceId | ID de recurso de Gestor de recursos do Azure.
SubscriptionId | ID da subscrição.
EventTimeString | Tempo de operação.
EventProperties | Propriedades de operação.
Estado | Estado da operação.
Autor da chamada | Chamador de operação (cliente do Azure de portal ou de gestão).
categoria | OperationalLogs

O código a seguir é um exemplo de uma cadeia de caracteres do JSON de registo operacional:

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Passos Seguintes
* [Introdução aos Hubs de eventos](event-hubs-what-is-event-hubs.md)
* [Descrição geral da API dos Hubs de Eventos](event-hubs-api-overview.md)
* [Introdução ao Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
