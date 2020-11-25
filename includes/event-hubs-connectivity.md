---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dac82692c76d9d36b1f25d7b93b5c3a2e2400672
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002801"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Que portas preciso para abrir na firewall? 
Pode utilizar os seguintes protocolos com os Azure Event Hubs para enviar e receber eventos:

- Protocolo avançado de fila de mensagens 1.0 (AMQP)
- Protocolo de Transferência de Hipertexto 1.1 com TLS (HTTPS)
- Apache Kafka

Consulte a tabela seguinte para as portas de saída que precisa de abrir para utilizar estes protocolos para comunicar com os Azure Event Hubs. 

| Protocolo | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte o [guia de protocolo amQP](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | Esta porta é utilizada para a API HTTP/REST e para amqp-over-WebSockets. |
| Kafka | 9093 | Ver [Use Event Hubs a partir de aplicações kafka](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)

A porta HTTPS é necessária para comunicação de saída também quando a AMQP é utilizada sobre o porto 5671, porque várias operações de gestão realizadas pelos SDKs clientes e a aquisição de fichas da Azure Ative Directory (quando utilizada) atropelam HTTPS. 

Os Azure SDKs oficiais geralmente usam o protocolo AMQP para o envio e receção de eventos de Event Hubs. A opção de protocolo AMQP-over-WebSockets passa por cima do porto TCP 443 tal como a API HTTP, mas é de outra forma funcionalmente idêntica com AMQP simples. Esta opção tem maior latência inicial de ligação devido a viagens de ida e volta extra de aperto de mão e um pouco mais de sobrecarga como compensação pela partilha do porto HTTPS. Se este modo for selecionado, a porta TCP 443 é suficiente para a comunicação. As seguintes opções permitem selecionar o modo amQP simples ou amqp WebSockets:

| Linguagem | Opção   |
| -------- | ----- |
| .NET     | [EventHubConnectionOptions.TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype?view=azure-dotnet&preserve-view=true) propriedade com [EventHubsTransportType.AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) ou [EventHubsTransportType.AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) |
| Java     | [com.microsoft.azure.eventhubs.EventProcessorClientBuilder.transporttype](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype?view=azure-java-stable&preserve-view=true) com [AmqpTransportType.AMQP](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) ou [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) |
| Nó  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions?view=azure-node-latest&preserve-view=true) tem uma `webSocketOptions` propriedade. |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient?view=azure-python&preserve-view=true) com [TransportType.Amqp](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python) ou [TransportType.AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python&preserve-view=true) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Que endereços IP preciso de permitir?
Quando está a trabalhar com o Azure, por vezes tem de permitir que intervalos de endereços IP específicos ou URLs na sua firewall corporativa ou procuração acedam a todos os serviços Azure que está a usar ou a tentar utilizar. Verifique se o tráfego é permitido nos endereços IP utilizados pelos Centros de Eventos. Para endereços IP utilizados pelos Azure Event Hubs: consulte [gamas IP Azure e Tags de Serviço - Nuvem Pública](https://www.microsoft.com/download/details.aspx?id=56519).

Além disso, verifique se o endereço IP para o seu espaço de nome é permitido. Para encontrar os endereços IP certos para permitir as suas ligações, siga estes passos:

1. Executar o seguinte comando a partir de um pedido de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Note o endereço IP devolvido em `Non-authoritative answer` . 

Se utilizar a **redundância** da zona para o seu espaço de nome, tem de fazer alguns passos adicionais: 

1. Primeiro, corres nslookup no espaço de nomes.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Note o nome na secção **de resposta não autorizada,** que se encontra num dos seguintes formatos: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Executar nslookup para cada um com sufixos s1, s2 e s3 para obter os endereços IP dos três casos em execução em três zonas de disponibilidade, 

    > [!NOTE]
    > O endereço IP devolvido pelo `nslookup` comando não é um endereço IP estático. No entanto, permanece constante até que a implementação subjacente seja eliminada ou transferida para um cluster diferente.

### <a name="where-can-i-find-client-ip-sending-or-receiving-messages-to-my-namespace"></a>Onde posso encontrar o IP do cliente a enviar ou a receber mensagens para o meu espaço de nome?
Em primeiro lugar, ative [a filtragem IP](../articles/event-hubs/event-hubs-ip-filtering.md) no espaço de nomes. 

Em seguida, Ative os registos de diagnóstico para [eventos de ligação de rede virtual do Event Hubs](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) seguindo instruções nos [registos de diagnóstico Ativar](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Verá o endereço IP para o qual a ligação é negada.

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

> [!IMPORTANT]
> Os registos de rede virtuais só são gerados se o espaço de nome permitir o acesso a partir de **endereços IP específicos (regras** de filtro IP). Se não quiser restringir o acesso ao seu espaço de identificação utilizando estas funcionalidades e ainda pretender obter registos de rede virtuais para rastrear endereços IP de clientes que se ligam ao espaço de nomes dos Centros de Eventos, poderá utilizar a seguinte solução: Ativar a filtragem IP e adicionar a gama total iPv4 endereçada (1.0.0/1 - 255.0.0.0/1). O Event Hubs não suporta intervalos de endereços IPv6. 
