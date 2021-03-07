---
title: Rotas de eventos
titleSuffix: Azure Digital Twins
description: Entenda como encaminhar eventos dentro da Azure Digital Twins e para outros Serviços Azure.
author: baanders
ms.author: baanders
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ea412b695c12f3ff7fdfa6250e2a474b618b8032
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430926"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Rotas eventos dentro e fora de Azure Digital Twins

As gémeas Azure Digital usam rotas de **eventos** para enviar dados aos consumidores fora do serviço. 

Existem dois casos importantes para o envio de dados da Azure Digital Twins:
* Envio de dados de um gémeo no gráfico Azure Digital Twins para outro. Por exemplo, quando uma propriedade com um gémeo digital muda, pode querer notificar e atualizar outro gémeo digital em conformidade.
* Envio de dados para serviços de dados a jusante para armazenamento ou tratamento adicional (também conhecido como *saída de dados).* Por exemplo,
  - Um hospital pode querer enviar dados de eventos da Azure Digital Twins para [a Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md), para registar dados de séries de tempo de eventos relacionados com lavagem de mãos para análise a granel.
  - Um negócio que já está a usar [o Azure Maps](../azure-maps/about-azure-maps.md) pode querer usar a Azure Digital Twins para melhorar a sua solução. Eles podem rapidamente ativar um Mapa Azure depois de criar as Gémeas Digitais Azure, trazer as entidades do Azure Map para as Gémeas Digitais Azure como [gémeas digitais](concepts-twins-graph.md) no gráfico gémeo, ou executar consultas poderosas aproveitando os seus dados Azure Maps e Azure Digital Twins juntos.

As rotas dos eventos são usadas para ambos os cenários.

## <a name="about-event-routes"></a>Sobre rotas de eventos

Uma rota de evento permite enviar dados de eventos de gémeos digitais em Azure Digital Twins para pontos finais personalizados nas suas subscrições. Atualmente, três serviços da Azure são suportados para pontos finais: [Event Hub,](../event-hubs/event-hubs-about.md) [Event Grid](../event-grid/overview.md)e [Service Bus.](../service-bus-messaging/service-bus-messaging-overview.md) Cada um destes serviços Azure pode ser conectado a outros serviços e atua como intermediário, enviando dados junto para destinos finais como TSI ou Azure Maps para qualquer processamento que você precisa.

O diagrama a seguir ilustra o fluxo de dados do evento através de uma solução IoT maior com um aspeto Azure Digital Twins:

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Azure Digital Twins encaminha dados através de pontos finais para vários serviços a jusante" border="false":::

Alvos típicos a jusante para rotas de eventos são recursos como TSI, Azure Maps, armazenamento e soluções de análise.

### <a name="event-routes-for-internal-digital-twin-events"></a>Rotas de eventos para eventos digitais gémeos internos

As rotas do evento também são usadas para lidar com eventos dentro do gráfico gémeo e enviar dados de gémeos digitais para gémeos digitais. Isto é feito através da ligação de rotas de eventos através da Grade de Eventos para calcular recursos, tais como [Azure Functions](../azure-functions/functions-overview.md). Estas funções definem então como os gémeos devem receber e responder aos eventos. 

Quando um recurso compute quer modificar o gráfico gémeo com base num evento que recebeu através da rota do evento, é útil que saiba qual o gémeo que quer modificar com antecedência. 

Em alternativa, a mensagem do evento também contém o ID do gémeo de origem que enviou a mensagem, para que o recurso computacional possa usar consultas ou cruzar relações para encontrar um gémeo alvo para a operação desejada. 

O recurso computacional também precisa de estabelecer permissões de segurança e acesso de forma independente.

Para percorrer o processo de criação de uma função Azure para processar eventos gémeos digitais, consulte [*Como-a-fazer: Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md).

## <a name="create-an-endpoint"></a>Criar um ponto final

Para definir uma rota de evento, primeiro os programadores têm de definir pontos finais. Um **ponto final** é um destino fora da Azure Digital Twins que suporta uma ligação de rota. Os destinos suportados incluem:
* Tópicos personalizados da Grelha de Eventos
* Hub de Eventos
* Service Bus

Para criar um ponto final, pode utilizar as [APIs DE REST, comandos CLI](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)ou o [portal Azure](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

Ao definir um ponto final, terá de fornecer:
* O nome do ponto final
* O tipo de ponto final (Event Grid, Event Hub ou Service Bus)
* A cadeia de ligação primária e a cadeia de ligação secundária para autenticar 
* O caminho tópico do ponto final, como *your-topic.westus2.eventgrid.azure.net*

As APIs de ponta que estão disponíveis no plano de controlo são:
* Criar ponto final
* Obtenha a lista de pontos finais
* Obter ponto final pelo nome
* Eliminar ponto final pelo nome

## <a name="create-an-event-route"></a>Criar uma rota de eventos
 
Para criar uma rota de eventos, pode utilizar as APIs de REPOUSO Azure Digital [Twins, comandos CLI](how-to-manage-routes-apis-cli.md#create-an-event-route)ou o [portal Azure](how-to-manage-routes-portal.md#create-an-event-route).

Aqui está um exemplo de criação de uma rota de evento dentro de uma aplicação de cliente, usando a chamada `CreateOrReplaceEventRouteAsync` [.NET (C#) SDK:](/dotnet/api/overview/azure/digitaltwins/client) 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::

1. Primeiro, um `DigitalTwinsEventRoute` objeto é criado, e o construtor toma o nome de um ponto final. Este `endpointName` campo identifica um ponto final como um Event Hub, Event Grid ou Service Bus. Estes pontos finais devem ser criados na sua subscrição e anexados à Azure Digital Twins utilizando APIs do plano de controlo antes de escrutinar esta chamada de registo.

2. O objeto de rota do evento também tem um campo [**filtro,**](how-to-manage-routes-apis-cli.md#filter-events) que pode ser usado para restringir os tipos de eventos que seguem esta rota. Um filtro `true` permite a rota sem filtragem adicional (um filtro de `false` desativa a rota). 

3. Este objeto de rota do evento é então passado para `CreateOrReplaceEventRouteAsync` , juntamente com um nome para a rota.

> [!TIP]
> Todas as funções SDK vêm em versões sincronizadas e assíncronos.

## <a name="dead-letter-events"></a>Eventos de cartas mortas

Quando um ponto final não consegue entregar um evento dentro de um determinado período de tempo ou depois de tentar entregar o evento um certo número de vezes, pode enviar o evento não entregue para uma conta de armazenamento. Este processo é conhecido como **letra morta.** A Azure Digital Twins vai escrever um evento quando **uma das seguintes** condições for cumprida. 

* Evento não é entregue dentro do período de tempo a viver
* O número de tentativas para entregar o evento excedeu o limite

Se uma das condições for cumprida, o evento é abandonado ou sem carta. Por defeito, cada ponto final **não** liga letras mortas. Para o ativar, tem de especificar uma conta de armazenamento para realizar eventos não entregues ao criar o ponto final. Em seguida, pode retirar eventos desta conta de armazenamento para resolver as entregas.

Antes de definir o local da letra morta, deve ter uma conta de armazenamento com um recipiente. Forneça o URL para este recipiente ao criar o ponto final. A letra morta é fornecida como URL de contentor com um token SAS. Esse símbolo só precisa de `write` permissão para o contentor de destino dentro da conta de armazenamento. O URL totalmente formado será no formato de: `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

Para saber mais sobre fichas SAS, consulte: [ *Conceder acesso limitado aos recursos de Armazenamento Azure usando assinaturas de acesso partilhado (SAS)*](../storage/common/storage-sas-overview.md)

Para aprender a configurar um ponto final com letras [*mortas, consulte Como-a-: Gerir pontos finais e rotas em Azure Digital Twins (APIs e CLI)*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering).

### <a name="types-of-event-messages"></a>Tipos de mensagens de eventos

Diferentes tipos de eventos no IoT Hub e no Azure Digital Twins produzem diferentes tipos de mensagens de notificação, conforme descrito abaixo.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>Passos seguintes

Veja como configurar e gerir uma rota de eventos:
* [*Como fazer: Gerir pontos finais e rotas*](how-to-manage-routes-apis-cli.md)

Ou, veja como usar as Funções Azure para encaminhar eventos dentro da Azure Digital Twins:
* [*Como fazer: Criar uma função Azure para o processamento de dados*](how-to-create-azure-function.md)