---
title: Configurar registos de diagnóstico - Azure Event Hub / Microsoft Docs
description: Saiba como configurar registos de atividades e registos de diagnóstico para centros de eventos em Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 976d8a7127438164c8b807b6f14d3ae877f44b65
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322462"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Configurar registos de diagnósticos para um hub de eventos do Azure

Pode ver dois tipos de registos para Azure Event Hubs:

* **[Registos de atividades](../azure-monitor/platform/platform-logs-overview.md)**: Estes registos têm informações sobre operações feitas num trabalho. Os registos estão sempre ativados. Pode ver as entradas de registo de atividade selecionando **o registo de atividade** no painel esquerdo para o seu espaço de nome do centro de eventos no portal Azure. Por exemplo: "Criar ou atualizar o espaço de nomes", "Criar ou atualizar o Centro de Eventos".

    ![Registo de atividades para um espaço de nomes de Centros de Eventos](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Registos de diagnóstico](../azure-monitor/platform/platform-logs-overview.md)**: Os registos de diagnóstico fornecem informações mais ricas sobre operações e ações que são conduzidas contra o seu espaço de nome utilizando a API, ou através de clientes de gestão no SDK de língua. 
    
    A seguinte secção mostra-lhe como ativar registos de diagnóstico para um espaço de nomes de Centros de Eventos.

## <a name="enable-diagnostic-logs"></a>Ativar registos de diagnóstico
Os registos de diagnóstico são desativado por predefinição. Para ativar registos de diagnóstico, siga estes passos:

1.  No [portal Azure,](https://portal.azure.com)navegue para o seu espaço de nomes Desempaco. 
2. Selecione **as definições de Diagnóstico** em **Monitorização** no painel esquerdo e, em seguida, selecione **+ Adicione a definição de diagnóstico**. 

    ![Página de definições de diagnóstico - adicione a definição de diagnóstico](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. Na secção de detalhes da **categoria,** selecione os **tipos de registos de diagnóstico** que pretende ativar. Você encontrará detalhes sobre estas categorias mais tarde neste artigo. 
5. Na secção de detalhes do **Destino,** desaponte o alvo de arquivo (destino) que deseja; por exemplo, uma conta de armazenamento, um centro de eventos ou um espaço de trabalho Log Analytics.

    ![Adicionar página de definições de diagnóstico](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  **Selecione Guarde** na barra de ferramentas para guardar as definições de diagnóstico.

    As novas definições fazem efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo de arquivo configurado, no painel de **registos de diagnóstico.**

    Para obter mais informações sobre a configuração dos diagnósticos, consulte a [visão geral dos registos de diagnóstico do Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Categorias de registos de diagnóstico

O Event Hubs captura registos de diagnóstico para as seguintes categorias:

| Categoria | Descrição | 
| -------- | ----------- | 
| Registos de Arquivo | Captura informações sobre operações [de captura de Centros de Eventos,](event-hubs-capture-overview.md) especificamente, registos relacionados com erros de captura. |
| Registos Operacionais | Capture todas as operações de gestão que são realizadas no espaço de nomes Azure Event Hubs. As operações de dados não são capturadas, devido ao elevado volume de operações de dados que são realizadas nos Hubs de Eventos Azure. |
| Troncos de escala automática | Captura operações de auto-insuflado feitas num espaço de nomes de Event Hubs. |
| Troncos coordenadores kafka | Captura operações de coordenador kafka relacionadas com centros de eventos. |
| Registos de erro do utilizador kafka | Captura informações sobre APIs kafka chamadas em Centros de Eventos. |
| Evento de ligação de rede virtual (VNet) | Captura informações sobre endereços IP e redes virtuais enviando tráfego para Os Centros de Eventos. |
| Registos de utilizadores geridos pelo cliente | Captura operações relacionadas com a chave gerida pelo cliente. |


Todos os registos são armazenados no formato JavaScript Object Notation (JSON). Cada entrada tem campos de cordas que utilizam o formato descrito nas seguintes secções.

## <a name="archive-logs-schema"></a>Esquema de registos de arquivo

As cordas JSON do log de arquivo incluem elementos listados na tabela seguinte:

Name | Descrição
------- | -------
Nome de tarefa | Descrição da tarefa que falhou
ActivityId | ID interno, usado para rastreio
trackingId | ID interno, usado para rastreio
resourceId | Azure Resource Manager resource ID
eventHub | Nome completo do centro de eventos (inclui nome de espaço de nome)
partitionId | Partição do Centro de Eventos sendo escrita para
archiveStep | valores possíveis: ArchiveFlushWriter, DestinationInit
startTime | Hora de início de avaria
falhas | Número de vezes que a falha ocorreu
duraçãoSso segundos | Duração da falha
message | Mensagem de erro
categoria | ArchiveLogs

O seguinte código é um exemplo de uma cadeia JSON de log de arquivo:

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
ActivityId | ID interno, usado para fins de rastreio |
EventName | Nome da operação |
resourceId | Azure Resource Manager resource ID |
SubscriptionId | ID da subscrição |
EventTimeString | Tempo de funcionamento |
EventProperties | Propriedades de operação |
Estado | Estado da operação |
Autor da chamada | Chamador de operação (portal Azure ou cliente de gestão) |
Categoria | Logística |

O seguinte código é um exemplo de uma cadeia JSON de log operacional:

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

## <a name="autoscale-logs-schema"></a>Esquema de registos de escala automática
O registo de escala automática JSON inclui elementos listados na tabela seguinte:

| Name | Descrição |
| ---- | ----------- | 
| TrackingId | ID interno, que é usado para fins de rastreio |
| ResourceId | Identificação de recursos do Azure Resource Manager. |
| Mensagem | Mensagem informativa, que fornece detalhes sobre a ação de auto-inflação. A mensagem contém o valor anterior e atual da unidade de produção para um determinado espaço de nome e o que desencadeou a insuflação do TU. |

Aqui está um evento de autoescala exemplo: 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

## <a name="kafka-coordinator-logs-schema"></a>O coordenador kafka regista o esquema
O registo de coordenador da Kafka JSON inclui elementos listados na tabela seguinte:

| Name | Descrição |
| ---- | ----------- | 
| RequestId | ID do pedido, que é usado para fins de rastreio |
| ResourceId | Azure Resource Manager resource ID |
| Operação | Nome da operação que é feita durante a coordenação do grupo |
| ClientId | ID de Cliente |
| NomespaceName | Nome do espaço de nomes | 
| SubscriptionId | ID de assinatura Azure |
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

## <a name="kafka-user-error-logs-schema"></a>Esquema de registos de erros do utilizador kafka
O registo de erros do utilizador Kafka JSON inclui elementos listados na tabela seguinte:

| Name | Descrição |
| ---- | ----------- |
| TrackingId | Identificação de rastreio, que é usada para fins de rastreio. |
| NomespaceName | Nome do espaço de nomes |
| Eventhub | Nome do hub de eventos |
| PartitionId | ID de Partição |
| GroupId | ID do Grupo |
| ClientId | ID de Cliente |
| ResourceId | Identificação de recursos do Azure Resource Manager. |
| Mensagem | Mensagem informativa, que fornece detalhes sobre um erro |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Esquema de eventos de ligação de rede virtual Hubs

Evento de ligação de rede virtual (VNet) evento de ligação de rede virtual JSON inclui elementos listados na tabela seguinte:

| Name | Descrição |
| ---  | ----------- | 
| SubscriptionId | ID de assinatura Azure |
| NomespaceName | Nome do espaço de nomes |
| IPAddress | Endereço IP de um cliente que se conecta ao serviço Event Hubs |
| Ação | Ação feita pelo serviço Event Hubs ao avaliar pedidos de ligação. As ações apoiadas são **Aceitar Conexão** e **Negar Conexão.** |
| Razão | Fornece uma razão pela qual a ação foi feita |
| Contagem | Número de ocorrências para a ação dada |
| ResourceId | Identificação de recursos do Azure Resource Manager. |

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

## <a name="customer-managed-key-user-logs"></a>Registos de utilizadores geridos pelo cliente
O registo de utilizador chave gerido pelo cliente JSON inclui elementos listados na tabela seguinte:

| Name | Descrição |
| ---- | ----------- | 
| Categoria | Tipo de categoria para uma mensagem. É um dos seguintes valores: **erro** e **informação** |
| ResourceId | ID de recursos internos, que inclui ID de subscrição Azure e nome de espaço de nome |
| KeyVault | Nome do recurso Key Vault |
| Chave | Nome da chave do cofre. |
| Versão | Versão da chave do cofre |
| Operação | O nome de uma operação feita para servir pedidos |
| Código | Código de estado |
| Mensagem | Mensagem, que fornece detalhes sobre um erro ou mensagem informativa |



## <a name="next-steps"></a>Próximos passos
- [Introdução aos Centros de Eventos](event-hubs-what-is-event-hubs.md)
- [Amostras de Centros de Eventos](sdks.md)
- Introdução ao Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
