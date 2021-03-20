---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/21/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 44afd8ea4ef2ab06ec31b7528e9776faebc3b4dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98689921"
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
| .NET     | [EventHubConnectionOptions.TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype) propriedade com [EventHubsTransportType.AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) ou [EventHubsTransportType.AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) |
| Java     | [com.microsoft.azure.eventhubs.EventProcessorClientBuilder.transporttype](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype) com [AmqpTransportType.AMQP](/java/api/com.azure.core.amqp.amqptransporttype) ou [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Nó  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions) tem uma `webSocketOptions` propriedade. |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient) com [TransportType.Amqp](/python/api/azure-eventhub/azure.eventhub.transporttype) ou [TransportType.AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Que endereços IP preciso de permitir?
Quando está a trabalhar com o Azure, por vezes tem de permitir que intervalos de endereços IP específicos ou URLs na sua firewall corporativa ou procuração acedam a todos os serviços Azure que está a usar ou a tentar utilizar. Verifique se o tráfego é permitido nos endereços IP utilizados pelos Centros de Eventos. Para endereços IP utilizados pelos Azure Event Hubs: consulte [gamas IP Azure e Tags de Serviço - Nuvem Pública](https://www.microsoft.com/download/details.aspx?id=56519).

Além disso, verifique se o endereço IP para o seu espaço de nome é permitido. Para encontrar os endereços IP certos para permitir as suas ligações, siga estes passos:

1. Executar o seguinte comando a partir de um pedido de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Note o endereço IP devolvido em `Non-authoritative answer` . 

Se utilizar a **redundância** da zona para o seu espaço de nome, tem de fazer alguns passos extras: 

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

### <a name="what-client-ips-are-sending-events-to-or-receiving-events-from-my-namespace"></a>Que clientes iPs estão enviando eventos ou recebendo eventos do meu espaço de nome?
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

> [!NOTE]
> Atualmente, não é possível determinar a origem IP de uma mensagem ou evento individual. 