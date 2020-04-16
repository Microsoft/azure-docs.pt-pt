---
title: O que é o Azure Event Grid?
description: Envie dados do evento de uma fonte para manipuladores com a Rede de Eventos Azure. Construir aplicações baseadas em eventos e integrar-se com os serviços Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 02/20/2020
ms.author: babanisa
ms.custom: seodec18
ms.openlocfilehash: b6f987eb283edea7f2d8e6b207b5b4e5b14f9120
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393190"
---
# <a name="what-is-azure-event-grid"></a>O que é o Azure Event Grid?

O Azure Event Grid permite-lhe criar facilmente aplicações com arquiteturas baseadas em eventos. Primeiro, selecione o recurso Azure que gostaria de subscrever e, em seguida, dê ao manipulador de eventos ou ao ponto final do WebHook para enviar o evento. O Event Grid tem suporte incorporado para eventos provenientes de serviços do Azure, como os blobs de armazenamento e os grupos de recursos. O Event Grid também suporta os seus próprios eventos com tópicos personalizados. 

Pode utilizar filtros para encaminhar eventos específicos para diferentes pontos finais, entregar em vários pontos finais e confirmar que os eventos são entregues com confiança.

A Azure Event Grid é implementada para maximizar a disponibilidade, espalhando-se de forma nativa por vários domínios de falhas em todas as regiões, e através de zonas de disponibilidade (em regiões que os suportam). Para uma lista de regiões que são apoiadas pela Event Grid, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)

Este artigo mostra uma descrição geral do Azure Event Grid. Se quiser começar a utilizar o Event Grid, veja [Criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md). 

![Modelo de rede de eventos de fontes e manipuladores](./media/overview/functional-model.png)

Esta imagem mostra como a Rede de Eventos conecta fontes e manipuladores, e não é uma lista completa de integrações apoiadas.

## <a name="event-sources"></a>Origens de eventos

Atualmente, os seguintes serviços do Azure suportam o envio de eventos para o Event Grid:

- [Configuração da Aplicação Azure](event-schema-app-configuration.md)
- [Armazenamento Azure Blob](event-schema-blob-storage.md)
- [Registo de Contentores Azure](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Serviços Azure Media](event-schema-media-services.md)
- [Grupos de recursos azure](event-schema-resource-groups.md)
- [Service Bus do Azure](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Subscrições do Azure](event-schema-subscriptions.md)

## <a name="event-handlers"></a>Processadores de eventos

Para obter detalhes completos sobre as capacidades de cada processador, bem como artigos relacionados, veja [processadores de eventos](event-handlers.md). Atualmente, os seguintes serviços do Azure suportam o processamento de eventos no Event Grid: 

* [Automatização do Azure](event-handlers.md#azure-automation)
* [Funções do Azure](event-handlers.md#azure-functions)
* [Hubs de Eventos](event-handlers.md#event-hubs)
* [Conexões Híbridas](event-handlers.md#hybrid-connections)
* [Aplicações Lógicas](event-handlers.md#logic-apps)
* [Power Automate (anteriormente conhecido como Microsoft Flow)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Service Bus](event-handlers.md#service-bus)
* [Armazenamento de filas](event-handlers.md#queue-storage)
* [WebHooks](event-handlers.md#webhooks)

## <a name="concepts"></a>Conceitos

Existem cinco conceitos no Azure Event Grid que lhe permitem começar:

* **Eventos** - o que aconteceu.
* **Origens dos eventos** – onde o evento aconteceu.
* **Tópicos** - o ponto final no qual os publicadores enviam eventos.
* **Assinaturas** de eventos - O ponto final ou mecanismo incorporado para encaminhar eventos, às vezes para mais do que um manipulador. As subscrições também são utilizadas pelos processadores para filtrar inteligentemente os eventos recebidos.
* **Processadores de eventos** - a aplicação ou o serviço que reage ao evento.

Para obter mais informações sobre estes conceitos, veja [Concepts in Azure Event Grid](concepts.md) (Conceitos no Azure Event Grid).

## <a name="capabilities"></a>Capacidades

Seguem-se algumas das principais funcionalidades do Azure Event Grid:

* **Simplicidade** - aponte e clique para direcionar eventos do seu recurso do Azure para qualquer processador ou ponto final de eventos.
* **Filtragem avançada** - Filtrar no tipo de evento ou no caminho de publicação de eventos para garantir que os manipuladores de eventos apenas recebem eventos relevantes.
* **Fan-out** - Subscreva vários pontos finais do mesmo evento para enviar cópias do evento para o maior número de lugares que for necessário.
* **Fiabilidade** - 24 horas de retry com backoff exponencial para garantir que os eventos são entregues.
* **Pagar por evento** - pague apenas pela quantidade utilizada no Event Grid.
* **Débito elevado** - crie cargas de trabalho de elevado volume no Event Grid com suporte para milhões de eventos por segundo.
* **Eventos incorporados** - comece a executar rapidamente com os eventos incorporados definidos por recursos.
* **Eventos Personalizados** - Utilize a Grelha de Eventos para encaminhar, filtrar e oferecer eventos personalizados de forma fiável na sua aplicação.

Para ver uma comparação entre o Event Grid, os Hubs de Eventos e o Service Bus, veja [Choose between Azure services that deliver messages](compare-messaging-services.md) (Escolher entre serviços do Azure que entregam mensagens).

## <a name="what-can-i-do-with-event-grid"></a>O que posso fazer com o Event Grid?

A Azure Event Grid fornece várias funcionalidades que melhoram muito o trabalho de automação de operações, ops e [integração:](https://azure.com/integration) 

### <a name="serverless-application-architectures"></a>Arquiteturas de aplicações sem servidor

![Arquitetura de aplicação sem servidor](./media/overview/serverless_web_app.png)

O Event Grid liga as origens de dados e os processadores de eventos. Por exemplo, utilize a Rede de Eventos para acionar uma função sem servidorque analise as imagens quando adicionada a um recipiente de armazenamento blob. 

### <a name="ops-automation"></a>Automatização de operações

![Automatização das operações](./media/overview/Ops_automation.png)

O Event Grid permite-lhe acelerar a automatização e simplificar a aplicação de políticas. Por exemplo, utilize a Rede de Eventos para notificar a Automação Azure quando for criada uma máquina virtual ou uma base de dados SQL. Utilize os eventos para verificar automaticamente se as configurações do serviço são conformes, coloque metadados em ferramentas de operações, marque máquinas virtuais ou itens de trabalho de ficheiros.

### <a name="application-integration"></a>Integração de aplicações

![Integração de candidaturas com o Azure](./media/overview/app_integration.png)

O Event Grid liga a sua aplicação a outros serviços. Por exemplo, crie um tópico personalizado para enviar os dados de eventos da aplicação para o Event Grid e tire partido da entrega fiável, do encaminhamento avançado e da integração direta no Azure deste serviços. Ou, pode usar a Grelha de Eventos com Aplicações Lógicas para processar dados em qualquer lugar, sem escrever código. 

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
  Um tutorial que utiliza as Funções do Azure para transmitir dados em fluxo dos Hubs de Eventos para o SQL Data Warehouse.
* [Event Grid REST API reference](/rest/api/eventgrid) (Referência da API REST do Event Grid)  
  Fornece conteúdo de referência para gestão de Assinaturas de Eventos, encaminhamento e filtragem.
