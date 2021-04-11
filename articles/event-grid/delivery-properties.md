---
title: Azure Event Grid - Definir cabeçalhos personalizados em eventos entregues
description: Descreve como pode definir cabeçalhos personalizados (ou propriedades de entrega) em eventos entregues.
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: e2eff6b0d1dc78f0d558bb8e4e1ad79c62c52657
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630529"
---
# <a name="delivery-with-custom-headers"></a>Entrega com cabeçalhos personalizados
As subscrições de eventos permitem-lhe configurar cabeçalhos http que estão incluídos em eventos entregues. Esta capacidade permite-lhe definir cabeçalhos personalizados que são necessários por um destino. Pode configurar até 10 cabeçalhos ao criar uma subscrição de eventos. Cada valor do cabeçalho não deve ser superior a 4.096 bytes (4K).

Pode definir cabeçalhos personalizados sobre os eventos que são entregues nos seguintes destinos:

- Webhooks
- Tópicos e filas de autocarros da Azure Service
- Azure Event Hubs
- Conexões híbridas de retransmissão

Ao criar uma subscrição de eventos no portal Azure, pode utilizar o separador **Propriedades de Entrega** para definir cabeçalhos http personalizados. Esta página permite definir valores fixos e dinâmicos do cabeçalho.

## <a name="setting-static-header-values"></a>Definição de valores de cabeçalho estático
Para definir cabeçalhos com valor fixo, forneça o nome do cabeçalho e o seu valor nos campos correspondentes:

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Propriedades de entrega - estática":::

Pode querer verificar **É secreto ao** fornecer dados confidenciais. Os dados sensíveis não serão apresentados no portal Azure. 

## <a name="setting-dynamic-header-values"></a>Definição de valores dinâmicos do cabeçalho
Você pode definir o valor de um cabeçalho com base em uma propriedade em um evento de entrada. Utilize a sintaxe JsonPath para se referir ao valor de propriedade de um evento a ser usado como o valor de um cabeçalho em pedidos de saída. Por exemplo, para definir o valor de um cabeçalho chamado **Channel** utilizando o valor do **sistema** de propriedade de eventos recebidos nos dados do evento, configurar a subscrição do seu evento da seguinte forma:

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Propriedades de entrega - dinâmica":::

## <a name="examples"></a>Exemplos
Esta secção dá-lhe alguns exemplos de utilização de propriedades de entrega.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Definir o cabeçalho de autorização com um símbolo do portador (exemplo não normativo)

Desaperte um valor para um cabeçalho de autorização para identificar o pedido com o seu manipulador Webhook. Um cabeçalho de autorização pode ser definido se não estiver [a proteger o seu Webhook com o Azure Ative Directory](secure-webhook-delivery.md).

| Nome do cabeçalho   | Tipo de cabeçalho | Valor do cabeçalho |
| :--           | :--         | :--            |
|`Authorization` | Estático | `BEARER SlAV32hkKG...`|

Os pedidos de saída devem agora conter o cabeçalho definido na subscrição do evento:

```console
GET /home.html HTTP/1.1

Host: acme.com

User-Agent: <user-agent goes here>

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> Definir cabeçalhos de autorização é uma opção sensata quando o seu destino é um Webhook. Não deve ser utilizado para [funções subscritas com um id de recursos,](/rest/api/eventgrid/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination)Service Bus, Event Hubs e Conexões Híbridas, uma vez que estes destinos suportam os seus próprios esquemas de autenticação quando utilizados com a Grade de Eventos.

### <a name="service-bus-example"></a>Exemplo de ônibus de serviço
A Azure Service Bus suporta a utilização de um [cabeçalho HTTP BrokerProperties](/rest/api/servicebus/message-headers-and-properties#message-headers) para definir propriedades de mensagens ao enviar mensagens únicas. O valor do `BrokerProperties` cabeçalho deve ser fornecido no formato JSON. Por exemplo, se precisar de definir propriedades de mensagens ao enviar uma única mensagem para o Service Bus, desafase o cabeçalho da seguinte forma:

| Nome do cabeçalho | Tipo de cabeçalho | Valor do cabeçalho |
| :-- | :-- | :-- |
|`BrokerProperties` | Estático     | `BrokerProperties:  { "MessageId": "{701332E1-B37B-4D29-AA0A-E367906C206E}", "TimeToLive" : 90}` |


### <a name="event-hubs-example"></a>Exemplo de Centros de Eventos

Se precisar de publicar eventos para uma partição específica dentro de um centro de eventos, defina um [cabeçalho HTTP brokerProperties](/rest/api/eventhub/event-hubs-runtime-rest#common-headers) na subscrição do evento para especificar a chave de partição que identifica a partição do centro do evento alvo.

| Nome do cabeçalho | Tipo de cabeçalho | Valor do cabeçalho                                  |
| :-- | :-- | :-- |
|`BrokerProperties` | Estático | `BrokerProperties: {"PartitionKey": "0000000000-0000-0000-0000-000000000000000"}`  |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Configurar tempo para viver em eventos de saída para as filas de armazenamento Azure
Para o destino Azure Storage Queues, só pode configurar o tempo de vida que a mensagem de saída terá depois de entregue numa fila de Armazenamento Azure. Se não for dado tempo, o tempo de vida por defeito da mensagem é de 7 dias. Também pode definir o evento para nunca expirar.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Propriedades de entrega - fila de armazenamento":::

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a entrega do evento, consulte o seguinte artigo:

- [Entrega e tentativa](delivery-and-retry.md)
- [Entrega de eventos de webhook](webhook-event-delivery.md)
- [Filtro de eventos](event-filtering.md)
