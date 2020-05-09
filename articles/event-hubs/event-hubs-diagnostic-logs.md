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
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 0fb5da965a9b13667b8a128e83a5a4cd2c2b28d7
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691846"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Configurar registos de diagnósticos para um hub de eventos do Azure

Pode ver dois tipos de registos para hubs de eventos azure:

* **[Registos de atividade](../azure-monitor/platform/platform-logs-overview.md)**: Estes registos têm informações sobre operações feitas num trabalho. Os registos estão sempre ativados. Pode ver entradas de registo de atividade selecionando **registo de atividade** no painel esquerdo para o seu espaço de nome do centro de eventos no portal Azure. Por exemplo: "Criar ou atualizar o espaço de nomes", "Criar ou atualizar o Centro de Eventos".

    ![Log de atividade para um espaço de nome de Hubs de Eventos](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Registos de diagnóstico](../azure-monitor/platform/platform-logs-overview.md)**: Os registos de diagnóstico fornecem informações mais ricas sobre operações e ações que são conduzidas contra o seu espaço de nome utilizando a API, ou através de clientes de gestão no idioma SDK. 
    
    A secção seguinte mostra-lhe como ativar registos de diagnóstico para um espaço de nome de Event Hubs.

## <a name="enable-diagnostic-logs"></a>Ativar registos de diagnóstico
Os registos de diagnóstico são desativados por defeito. Para ativar os registos de diagnóstico, siga estes passos:

1.  No [portal Azure,](https://portal.azure.com)navegue para o seu espaço de nome Sem Nome Do Seu Evento Hubs. 
2. Selecione **definições de diagnóstico** sob **monitorização** no painel esquerdo e, em seguida, selecione **+ Adicione a definição de diagnóstico**. 

    ![Página de definições de diagnóstico - adicione definição de diagnóstico](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. Na secção de detalhes da **categoria,** selecione os **tipos de registos de diagnóstico** que pretende ativar. Você encontrará detalhes sobre estas categorias mais tarde neste artigo. 
5. Na secção de detalhes do **Destino,** detete o alvo de arquivo (destino) que deseja; por exemplo, uma conta de armazenamento, um centro de eventos ou um espaço de trabalho log Analytics.

    ![Adicionar página de definições de diagnóstico](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Selecione **Guardar** na barra de ferramentas para guardar as definições de diagnóstico.

    As novas definições têm efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo de arquivo configurado, no painel de registos de **diagnóstico.**

    Para obter mais informações sobre a configuração dos diagnósticos, consulte a [visão geral dos registos de diagnóstico do Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Categorias de registos de diagnóstico

O Event Hubs captura registos de diagnóstico para as seguintes categorias:

| Categoria | Descrição | 
| -------- | ----------- | 
| Registos de Arquivo | Captura informações sobre operações de captura de [Hubs](event-hubs-capture-overview.md) de Eventos, especificamente, registos relacionados com erros de captura. |
| Registos Operacionais | Capture todas as operações de gestão que são realizadas no espaço de nome do Azure Event Hubs. As operações de dados não são capturadas, devido ao elevado volume de operações de dados que são realizadas nos Hubs de Eventos Do Azure. |
| Registos de escala automática | Captura operações de auto-inflação feitas num espaço de nome do Event Hubs. |
| Registos coordenadores de Kafka | Captura operações de coordenador de Kafka relacionadas com Centros de Eventos. |
| Registos de erros do utilizador kafka | Captura informações sobre apis kafka chamadas em Centros de Eventos. |
| Evento Hubs evento de conexão virtual rede (VNet) | Captura informações sobre endereços IP e redes virtuais que enviam tráfego para Centros de Eventos. |
| Registos de utilizadores-chave geridos pelo cliente | Captura operações relacionadas com a chave gerida pelo cliente. |


Todos os registos são armazenados no formato JavaScript Object Notation (JSON). Cada entrada tem campos de cordas que utilizam o formato descrito nas seguintes secções.

## <a name="archive-logs-schema"></a>Esquema de registos de arquivo

As cordas JSON de registo de arquivo incluem elementos listados na tabela seguinte:

Name | Descrição
------- | -------
Nome de tarefa | Descrição da tarefa que falhou
Atividadeid | ID interno, usado para rastrear
trackingId | ID interno, usado para rastrear
resourceId | ID de recurso do Gestor de Recursos Azure
eventoHub | Nome completo do centro de eventos (inclui nome do espaço de nome)
partiçãoId | Partição do Hub de Eventos sendo escrita para
arquivoStep | valores possíveis: ArchiveFlushWriter, DestinationInit
startTime | Hora de início do insucesso
falhas | Número de vezes que ocorreu a falha
duraçãoInSeconds | Duração da avaria
message | Mensagem de erro
categoria | ArquivoS

O seguinte código é um exemplo de uma cadeia JSON de registo de arquivo:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>Esquema de registos operacionais

As cordas JSON de registo operacional incluem elementos listados na tabela seguinte:

Name | Descrição
------- | -------
Atividadeid | ID interno, usado para fins de rastreamento |
EventName | Nome da operação |
resourceId | ID de recurso do Gestor de Recursos Azure |
SubscriptionId | ID da subscrição |
String de Tempo de Evento | Tempo de operação |
Propriedades de Eventos | Propriedades de operação |
Estado | Estado da operação |
Autor da chamada | Chamada de operação (portal Azure ou cliente de gestão) |
Categoria | Registos Operacionais |

O seguinte código é um exemplo de uma cadeia JSON de registo operacional:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Esquema de logs de escala automática
O log de escala automática JSON inclui elementos listados na tabela seguinte:

| Name | Descrição |
| ---- | ----------- | 
| TrackingId | ID interno, que é usado para fins de rastreio |
| ResourceId | ID de recurso do Gestor de Recursos Azure. |
| Mensagem | Mensagem informativa, que fornece detalhes sobre a ação de auto-inflação. A mensagem contém o valor anterior e atual da unidade de entrada para um determinado espaço de nome e o que desencadeou a insuflação do TU. |

## <a name="kafka-coordinator-logs-schema"></a>Kafka coordenador logs schema
O log do coordenador kafka JSON inclui elementos listados na tabela seguinte:

| Name | Descrição |
| ---- | ----------- | 
| Requestid | Solicitar ID, que é usado para fins de rastreio |
| ResourceId | ID de recurso do Gestor de Recursos Azure |
| Operação | Nome da operação que é feita durante a coordenação do grupo |
| ClientId | ID de Cliente |
| Nome do espaço de nome | Nome do espaço de nomes | 
| SubscriptionId | ID de subscrição azure |
| Mensagem | Mensagem informativa ou de aviso, que fornece detalhes sobre as ações feitas durante a coordenação do grupo. |

### <a name="example"></a>Exemplo

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Erro do utilizador kafka regista esquema
O registo de erros do utilizador kafka JSON inclui elementos listados na tabela seguinte:

| Name | Descrição |
| ---- | ----------- |
| TrackingId | Identificação de rastreio, que é usada para fins de rastreio. |
| Nome do espaço de nome | Nome do espaço de nomes |
| Eventhub | Nome do hub de eventos |
| Partida | ID de Partição |
| GroupId | ID do Grupo |
| ClientId | ID de Cliente |
| ResourceId | ID de recurso do Gestor de Recursos Azure. |
| Mensagem | Mensagem informativa, que fornece detalhes sobre um erro |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Event Hubs virtual network connection event schema

Evento Hubs virtual rede (VNet) evento de conexão JSON inclui elementos listados na tabela seguinte:

| Name | Descrição |
| ---  | ----------- | 
| SubscriptionId | ID de subscrição azure |
| Nome do espaço de nome | Nome do espaço de nomes |
| IPAddress | Endereço IP de um cliente que liga ao serviço Event Hubs |
| Ação | Ação feita pelo serviço Event Hubs ao avaliar pedidos de ligação. As ações apoiadas são **a Aceitação de Ligação** e **Ligação De Negação**. |
| Razão | Fornece uma razão pela qual a ação foi feita |
| Contagem | Número de ocorrências para a determinada ação |
| ResourceId | ID de recurso do Gestor de Recursos Azure. |

### <a name="example"></a>Exemplo

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Registos de utilizadores-chave geridos pelo cliente
O registo de utilizador-chave gerido pelo cliente JSON inclui elementos listados na tabela seguinte:

| Name | Descrição |
| ---- | ----------- | 
| Categoria | Tipo de categoria para uma mensagem. É um dos seguintes valores: **erro** e **informação** |
| ResourceId | ID de recursos internos, que inclui ID de subscrição azure e nome do espaço de nome |
| KeyVault | Nome do recurso Key Vault |
| Chave | Nome da chave do cofre. |
| Versão | Versão da chave key vault |
| Operação | O nome de uma operação feita para servir pedidos |
| Código | Código de estado |
| Mensagem | Mensagem, que fornece detalhes sobre um erro ou mensagem informativa |



## <a name="next-steps"></a>Passos seguintes
- [Introdução aos Centros de Eventos](event-hubs-what-is-event-hubs.md)
- [Descrição geral da API dos Hubs de Eventos](event-hubs-api-overview.md)
- Introdução ao Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
