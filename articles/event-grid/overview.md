---
title: Publicar e subscrever a eventos da aplicação - Azure Event Grid
description: Envie dados de eventos de uma origem para manipuladores com o Azure Event Grid. Crie aplicações baseadas em eventos e integrar com serviços do Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 05/25/2019
ms.author: babanisa
ms.custom: seodec18
ms.openlocfilehash: b262a06bc3d80bd1f8105c6629bbc07e6ccd34eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077145"
---
# <a name="what-is-azure-event-grid"></a>O que é o Azure Event Grid?

O Azure Event Grid permite-lhe criar facilmente aplicações com arquiteturas baseadas em eventos. Primeiro, selecione o recurso do Azure que pretende subscrever e, em seguida, dê o manipulador de eventos ou o ponto final do WebHook para enviar o evento. O Event Grid tem suporte incorporado para eventos provenientes de serviços do Azure, como os blobs de armazenamento e os grupos de recursos. O Event Grid também suporta os seus próprios eventos com tópicos personalizados. 

Pode utilizar filtros para encaminhar eventos específicos para diferentes pontos finais, entregar em vários pontos finais e confirmar que os eventos são entregues com confiança.

O Azure Event Grid é implementado para maximizar a disponibilidade ao propagar nativamente em vários domínios de falha em cada região e em zonas de disponibilidade (em regiões que suportam-los). Atualmente, a Azure Event Grid está disponível em todas as regiões públicas. Ele ainda não está disponível no Azure China 21Vianet, clouds do Azure Government ou Azure Alemanha.

Este artigo mostra uma descrição geral do Azure Event Grid. Se quiser começar a utilizar o Event Grid, veja [Criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md). 

![Modelo de grelha de eventos de origens e manipuladores](./media/overview/functional-model.png)

Esta imagem mostra como o Event Grid liga-se a origens e manipuladores e não é uma lista abrangente de integrações suportadas.

## <a name="event-sources"></a>Origens de eventos

Para obter detalhes completos sobre as capacidades de cada origem, bem como artigos relacionados, veja [origens de eventos](event-sources.md). Atualmente, os seguintes serviços do Azure suportam o envio de eventos para o Event Grid:

* [Subscrições do Azure (operações de gestão)](event-sources.md#azure-subscriptions)
* [Container Registry](event-sources.md#container-registry)
* [Tópicos personalizados](event-sources.md#custom-topics)
* [Hubs de Eventos](event-sources.md#event-hubs)
* [Hub IoT](event-sources.md#iot-hub)
* [Serviços de Multimédia](event-sources.md#media-services)
* [Grupos de recursos (operações de gestão)](event-sources.md#resource-groups)
* [Service Bus](event-sources.md#service-bus)
* [Blob de armazenamento](event-sources.md#storage)
* [Azure Maps](event-sources.md#maps)

## <a name="event-handlers"></a>Processadores de eventos

Para obter detalhes completos sobre as capacidades de cada processador, bem como artigos relacionados, veja [processadores de eventos](event-handlers.md). Atualmente, os seguintes serviços do Azure suportam o processamento de eventos no Event Grid: 

* [Automatização do Azure](event-handlers.md#azure-automation)
* [Funções do Azure](event-handlers.md#azure-functions)
* [Hubs de Eventos](event-handlers.md#event-hubs)
* [Ligações Híbridas](event-handlers.md#hybrid-connections)
* [Aplicações Lógicas](event-handlers.md#logic-apps)
* [Microsoft Flow](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Armazenamento de filas](event-handlers.md#queue-storage)
* [Service Bus](event-handlers.md#service-bus-queue-preview) (pré-visualização)
* [WebHooks](event-handlers.md#webhooks)

## <a name="concepts"></a>Conceitos

Existem cinco conceitos no Azure Event Grid que lhe permitem começar:

* **Eventos** - o que aconteceu.
* **Origens dos eventos** – onde o evento aconteceu.
* **Tópicos** - o ponto final no qual os publicadores enviam eventos.
* **Subscrições de eventos** -o mecanismo de ponto final ou interna para rotear os eventos, por vezes, até mais de um manipulador. As subscrições também são utilizadas pelos processadores para filtrar inteligentemente os eventos recebidos.
* **Processadores de eventos** - a aplicação ou o serviço que reage ao evento.

Para obter mais informações sobre estes conceitos, veja [Concepts in Azure Event Grid](concepts.md) (Conceitos no Azure Event Grid).

## <a name="capabilities"></a>Capacidades

Seguem-se algumas das principais funcionalidades do Azure Event Grid:

* **Simplicidade** - aponte e clique para direcionar eventos do seu recurso do Azure para qualquer processador ou ponto final de eventos.
* **Filtragem avançada** -filtre os eventos tipo ou evento de publicar o caminho para se certificar de que os manipuladores de eventos recebem apenas eventos relevantes.
* **Fan-out** -subscreva vários pontos finais para o mesmo evento envie cópias do evento para tantas casas conforme necessário.
* **Confiabilidade** -repetição de 24 horas com um término exponencial para se certificar de que os eventos são entregues.
* **Pagar por evento** - pague apenas pela quantidade utilizada no Event Grid.
* **Débito elevado** - crie cargas de trabalho de elevado volume no Event Grid com suporte para milhões de eventos por segundo.
* **Eventos incorporados** - comece a executar rapidamente com os eventos incorporados definidos por recursos.
* **Eventos Personalizados** – utilize a rota e o filtro do Event Grid para entregar, com confiança, eventos personalizados na aplicação.

Para ver uma comparação entre o Event Grid, os Hubs de Eventos e o Service Bus, veja [Choose between Azure services that deliver messages](compare-messaging-services.md) (Escolher entre serviços do Azure que entregam mensagens).

## <a name="what-can-i-do-with-event-grid"></a>O que posso fazer com o Event Grid?

O Azure Event Grid fornece várias funcionalidades que melhorar ainda mais a sem servidor, automatização de operações, e [integração](https://azure.com/integration) trabalhar: 

### <a name="serverless-application-architectures"></a>Arquiteturas de aplicações sem servidor

![Arquitetura de aplicações sem servidor](./media/overview/serverless_web_app.png)

O Event Grid liga as origens de dados e os processadores de eventos. Por exemplo, utilize o Event Grid para acionar uma função sem servidores que analisa imagens quando adicionada a um contentor de armazenamento de Blobs. 

### <a name="ops-automation"></a>Automatização de operações

![Automatização de operações](./media/overview/Ops_automation.png)

O Event Grid permite-lhe acelerar a automatização e simplificar a aplicação de políticas. Por exemplo, utilize o Event Grid para notificar a automatização do Azure quando é criada uma máquina virtual ou base de dados SQL. Utilize os eventos para verificar automaticamente a que as configurações de serviço estão em conformidade, colocar metadados em ferramentas de operações, Etiquetar máquinas virtuais ou arquivar itens de trabalho.

### <a name="application-integration"></a>Integração de aplicações

![Integração de aplicações com o Azure](./media/overview/app_integration.png)

O Event Grid liga a sua aplicação a outros serviços. Por exemplo, crie um tópico personalizado para enviar os dados de eventos da aplicação para o Event Grid e tire partido da entrega fiável, do encaminhamento avançado e da integração direta no Azure deste serviços. Em alternativa, pode utilizar o Event Grid com o Logic Apps para processar dados em qualquer lugar, sem ter de escrever código. 

## <a name="how-much-does-event-grid-cost"></a>Quanto custa o Event Grid?

O Azure Event Grid tem um modelo de preços “pagar por evento”, de modo que só paga pelo que utilizar. As primeiras 100 000 operações por mês são gratuitas. As operações são definidas como entrada de evento, tentativas de entrega de subscrição, chamadas de gestão e filtragem pelo sufixo do requerente. Para obter detalhes, veja a [página de preços](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Passos Seguintes

* [Route Storage Blob events](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) (Encaminhar eventos do Blob de Armazenamento)  
  Utilize o Event Grid para responder a eventos de blobs de armazenamento.
* [Criar e subscrever eventos personalizados](custom-event-quickstart.md)  
  Utilize o início rápido do Azure Event Grid para começar a enviar os seus próprios eventos personalizados para qualquer ponto final imediatamente.
* [Using Logic Apps as an Event Handler](monitor-virtual-machine-changes-event-grid-logic-app.md) (Utilizar o Logic Apps como Processador de Eventos)  
  Um tutorial sobre como criar uma aplicação com o Logic Apps para reagir a eventos enviados pelo Event Grid.
* [Transmitir macrodados em fluxo para um armazém de dados](event-grid-event-hubs-integration.md)  
  Um tutorial que utiliza as Funções do Azure para transmitir dados em fluxo dos Hubs de Eventos para o SQL Data Warehouse.
* [Event Grid REST API reference](/rest/api/eventgrid) (Referência da API REST do Event Grid)  
  Fornece conteúdos de referência para a gestão de subscrições de eventos, encaminhamento e filtragem.
