---
title: Configurar registos de diagnóstico - Azure Event Hub [ Azure Event Hub ] Microsoft Docs
description: Aprenda a configurar registos de atividade e registos de diagnóstico para centros de eventos em Azure.
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
ms.openlocfilehash: 68aa62ad34f8db531d439a581ef024862da0f90c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162315"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Configurar registos de diagnósticos para um hub de eventos do Azure

Pode ver dois tipos de registos para hubs de eventos azure:

* **[Registos de atividade](../azure-monitor/platform/platform-logs-overview.md)**: Estes registos têm informações sobre operações realizadas num trabalho. Os registos estão sempre ativados.
* **[Registos de diagnóstico:](../azure-monitor/platform/platform-logs-overview.md)** Pode configurar registos de diagnóstico para uma visão mais rica de tudo o que acontece com um trabalho. Os registos de diagnóstico cobrem atividades a partir do momento em que o trabalho é criado até que o trabalho seja eliminado, incluindo atualizações e atividades que ocorrem durante o trabalho em execução.

## <a name="enable-diagnostic-logs"></a>Ativar registos de diagnóstico

Os registos de diagnóstico são desativados por defeito. Para ativar os registos de diagnóstico, siga estes passos:

1.  No [portal Azure,](https://portal.azure.com)sob **monitorização + gestão,** clique em **registos de Diagnóstico**.

    ![Pane navegação para registos de diagnóstico](./media/event-hubs-diagnostic-logs/image1.png)

2.  Clique no recurso que pretende monitorizar.

3.  Clique em **Ativar diagnósticos**.

    ![Ligue os registos de diagnóstico](./media/event-hubs-diagnostic-logs/image2.png)

4.  Para **o Estado,** clique **em**.

    ![Alterar o estado dos registos de diagnóstico](./media/event-hubs-diagnostic-logs/image3.png)

5.  Definir o alvo de arquivo que deseja; por exemplo, uma conta de armazenamento, um hub de eventos ou registos do Monitor Azure.

6.  Guarde as novas definições de diagnóstico.

As novas definições têm efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo de arquivo configurado, no painel de registos de **diagnóstico.**

Para obter mais informações sobre a configuração dos diagnósticos, consulte a [visão geral dos registos de diagnóstico do Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Categorias de registos de diagnóstico

O Event Hubs captura registos de diagnóstico para duas categorias:

* **Registos de arquivo**: registos relacionados com arquivos de Centros de Eventos, especificamente, registos relacionados com erros de arquivo.
* **Registos Operacionais**: informações sobre o que está a acontecer durante as operações do Event Hubs, especificamente, o tipo de operação, incluindo a criação do hub de eventos, os recursos utilizados e o estado da operação.

## <a name="diagnostic-logs-schema"></a>Esquema de logs de diagnóstico

Todos os registos são armazenados no formato JavaScript Object Notation (JSON). Cada entrada tem campos de cordas que utilizam o formato descrito nas seguintes secções.

### <a name="archive-logs-schema"></a>Esquema de registos de arquivo

As cordas JSON de registo de arquivo incluem elementos listados na tabela seguinte:

Nome | Descrição
------- | -------
Nome de tarefa | Descrição da tarefa que falhou.
Atividadeid | Identificação interna, usada para rastrear.
trackingId | Identificação interna, usada para rastrear.
resourceId | ID de recurso do Gestor de Recursos Azure.
eventoHub | Nome completo do centro de eventos (inclui nome de espaço de nome).
partiçãoId | Partição do Centro de Eventos a ser escrita.
arquivoStep | ArchiveFlushWriter
startTime | Hora de início do fracasso.
falhas | O número de vezes que a falha ocorreu.
duraçãoInSeconds | Duração do fracasso.
message | Mensagem de erro.
categoria | ArquivoS

O seguinte código é um exemplo de uma cadeia JSON de registo de arquivo:

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

As cordas JSON de registo operacional incluem elementos listados na tabela seguinte:

Nome | Descrição
------- | -------
Atividadeid | Identificação interna, usada para rastrear o propósito.
EventName | nome da operação.  
resourceId | ID de recurso do Gestor de Recursos Azure.
SubscriptionId | ID de assinatura.
String de Tempo de Evento | Hora da operação.
Propriedades de Eventos | Propriedades de operação.
Estado | Estado de operação.
Autor da chamada | Chamada de operação (portal Azure ou cliente de gestão).
categoria | Registos Operacionais

O seguinte código é um exemplo de uma cadeia JSON de registo operacional:

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

## <a name="next-steps"></a>Passos seguintes
- [Introdução aos Centros de Eventos](event-hubs-what-is-event-hubs.md)
- [Descrição geral da API dos Hubs de Eventos](event-hubs-api-overview.md)
- Introdução ao Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Pitão](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
