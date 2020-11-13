---
title: O que é o Azure Event Grid?
description: Envie dados de eventos de uma fonte para os manipuladores com a Azure Event Grid. Construa aplicações baseadas em eventos e integre-se com os serviços Azure.
ms.topic: overview
ms.date: 09/24/2020
ms.openlocfilehash: a4f614a22fdfa7d2272c70751f56f19145a10fa6
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576687"
---
# <a name="what-is-azure-event-grid"></a>O que é o Azure Event Grid?

O Azure Event Grid permite-lhe criar facilmente aplicações com arquiteturas baseadas em eventos. Primeiro, selecione o recurso Azure a que gostaria de subscrever e, em seguida, dê ao responsável pelo evento ou ao ponto final WebHook para enviar o evento para. O Event Grid tem suporte incorporado para eventos provenientes de serviços do Azure, como os blobs de armazenamento e os grupos de recursos. O Event Grid também suporta os seus próprios eventos com tópicos personalizados. 

Pode utilizar filtros para encaminhar eventos específicos para diferentes pontos finais, fazer multicast para vários pontos finais e confirmar que os eventos são entregues de forma fiável.

A Azure Event Grid é implementada para maximizar a disponibilidade espalhando-se de forma nativa por vários domínios de falhas em todas as regiões, e através de zonas de disponibilidade (em regiões que as suportam). Para obter uma lista de regiões apoiadas pela Event Grid, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)

Este artigo mostra uma descrição geral do Azure Event Grid. Se quiser começar a utilizar o Event Grid, veja [Criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md). 

:::image type="content" source="./media/overview/functional-model.png" alt-text="Modelo de grelha de eventos de fontes e manipuladores" lightbox="./media/overview/functional-model.png":::

Esta imagem mostra como o Event Grid conecta fontes e manipuladores, e não é uma lista completa de integrações apoiadas.

## <a name="event-sources"></a>Origens de eventos

Atualmente, os seguintes serviços Azure apoiam o envio de eventos para a Grade de Eventos. Para mais informações sobre uma fonte na lista, selecione o link.

- [Configuração da Aplicação Azure](event-schema-app-configuration.md)
- [Armazenamento de Blobs do Azure](event-schema-blob-storage.md)
- [Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Hub IoT do Azure](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Serviços de Multimédia do Azure](event-schema-media-services.md)
- [Grupos de recursos do Azure](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Subscrições do Azure](event-schema-subscriptions.md)

## <a name="event-handlers"></a>Processadores de eventos

Para obter detalhes completos sobre as capacidades de cada processador, bem como artigos relacionados, veja [processadores de eventos](event-handlers.md). Atualmente, os seguintes serviços do Azure suportam o processamento de eventos no Event Grid: 

* [Automatização do Azure](handler-webhooks.md#azure-automation)
* [Funções do Azure](handler-functions.md)
* [Hubs de Eventos](handler-event-hubs.md)
* [Conexões híbridas de retransmissão](handler-relay-hybrid-connections.md)
* [Logic Apps](handler-webhooks.md#logic-apps)
* [Power Automamate (anteriormente conhecido como Microsoft Flow)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Service Bus](handler-service-bus.md)
* [Armazenamento de fila](handler-storage-queues.md)
* [WebHooks](handler-webhooks.md)

## <a name="concepts"></a>Conceitos

Existem cinco conceitos no Azure Event Grid que lhe permitem começar:

* **Eventos** - o que aconteceu.
* **Origens dos eventos** – onde o evento aconteceu.
* **Tópicos** - o ponto final no qual os publicadores enviam eventos.
* **Subscrições de eventos** - O ponto final ou mecanismo incorporado para encaminhar eventos, às vezes para mais de um manipulador. As subscrições também são utilizadas pelos processadores para filtrar inteligentemente os eventos recebidos.
* **Processadores de eventos** - a aplicação ou o serviço que reage ao evento.

Para obter mais informações sobre estes conceitos, veja [Concepts in Azure Event Grid](concepts.md) (Conceitos no Azure Event Grid).

## <a name="capabilities"></a>Capacidades

Seguem-se algumas das principais funcionalidades do Azure Event Grid:

* **Simplicidade** - aponte e clique para direcionar eventos do seu recurso do Azure para qualquer processador ou ponto final de eventos.
* **Filtragem avançada** - Filtrar no tipo de evento ou caminho de publicação de eventos para garantir que os manipuladores de eventos apenas recebem eventos relevantes.
* **Fan-out** - Subscreva vários pontos finais para o mesmo evento para enviar cópias do evento para o número de lugares necessários.
* **Fiabilidade** - 24 horas de retração com recuo exponencial para garantir que os eventos são entregues.
* **Pagar por evento** - pague apenas pela quantidade utilizada no Event Grid.
* **High Throughoff -** Construa cargas de trabalho de alto volume na Grade de Eventos.
* **Eventos incorporados** - comece a executar rapidamente com os eventos incorporados definidos por recursos.
* **Eventos Personalizados** - Use a Grade de Eventos para encaminhar, filtrar e entregar eventos personalizados na sua aplicação.

Para ver uma comparação entre o Event Grid, os Hubs de Eventos e o Service Bus, veja [Choose between Azure services that deliver messages](compare-messaging-services.md) (Escolher entre serviços do Azure que entregam mensagens).

## <a name="what-can-i-do-with-event-grid"></a>O que posso fazer com o Event Grid?

A Azure Event Grid fornece várias funcionalidades que melhoram consideravelmente o trabalho de integração e trabalhos sem servidores, automação e [integração:](https://azure.com/integration) 

### <a name="serverless-application-architectures"></a>Arquiteturas de aplicações sem servidor

![Arquitetura de aplicações sem servidor](./media/overview/serverless_web_app.png)

O Event Grid liga as origens de dados e os processadores de eventos. Por exemplo, utilize a Grade de Eventos para ativar uma função sem servidor que analise as imagens quando adicionadas a um recipiente de armazenamento de bolhas. 

### <a name="ops-automation"></a>Automatização de operações

![Automatização das operações](./media/overview/Ops_automation.png)

O Event Grid permite-lhe acelerar a automatização e simplificar a aplicação de políticas. Por exemplo, utilize a Grade de Eventos para notificar a Azure Automation quando for criada uma máquina virtual ou base de dados em Azure SQL. Utilize os eventos para verificar automaticamente se as configurações de serviço estão em conformidade, colocar metadados em ferramentas de operações, marcar máquinas virtuais ou itens de trabalho de ficheiro.

### <a name="application-integration"></a>Integração de aplicações

![Integração de aplicações com a Azure](./media/overview/app_integration.png)

O Event Grid liga a sua aplicação a outros serviços. Por exemplo, crie um tópico personalizado para enviar os dados de eventos da aplicação para o Event Grid e tire partido da entrega fiável, do encaminhamento avançado e da integração direta no Azure deste serviços. Ou, pode utilizar a Grade de Eventos com Aplicações Lógicas para processar dados em qualquer lugar, sem escrever código. 

## <a name="how-much-does-event-grid-cost"></a>Quanto custa o Event Grid?

O Azure Event Grid tem um modelo de preços “pagar por evento”, de modo que só paga pelo que utilizar. As primeiras 100 000 operações por mês são gratuitas. As operações são definidas como entrada de evento, tentativas de entrega de subscrição, chamadas de gestão e filtragem pelo sufixo do requerente. Para obter detalhes, veja a [página de preços](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Passos seguintes

* [Route Storage Blob events](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) (Encaminhar eventos do Blob de Armazenamento)  
  Utilize o Event Grid para responder a eventos de blobs de armazenamento.
* [Criar e subscrever eventos personalizados](custom-event-quickstart.md)  
  Utilize o início rápido do Azure Event Grid para começar a enviar os seus próprios eventos personalizados para qualquer ponto final imediatamente.
* [Using Logic Apps as an Event Handler](monitor-virtual-machine-changes-event-grid-logic-app.md) (Utilizar o Logic Apps como Processador de Eventos)  
  Um tutorial sobre como criar uma aplicação com o Logic Apps para reagir a eventos enviados pelo Event Grid.
* [Transmitir macrodados em fluxo para um armazém de dados](event-grid-event-hubs-integration.md)  
  Um tutorial que usa funções Azure para transmitir dados de Centros de Eventos para Azure Synapse Analytics.
* [Event Grid REST API reference](/rest/api/eventgrid) (Referência da API REST do Event Grid)  
  Fornece conteúdo de referência para a gestão de Subscrições de Eventos, encaminhamento e filtragem.