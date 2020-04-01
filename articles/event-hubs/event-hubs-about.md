---
title: O que são os Hubs de Eventos do Azure? - um serviço de ingestão de Big Data [ Microsoft Docs
description: Saiba mais sobre Hubs de Eventos do Azure, um serviço de fluxo de Macrodados que ingere milhões de eventos por segundo.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: overview
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 45276ab59f1a3dabea42b904ff54bd37326fdeca
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398116"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs - Uma plataforma de streaming de big data e serviço de ingestão de eventos
O Azure Event Hubs é uma plataforma de streaming de grandes dados e um serviço de ingestão de eventos. Pode receber e processar milhões de eventos por segundo. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches.

Os seguintes cenários são alguns dos cenários onde pode utilizar Os Centros de Eventos:

- Deteção de anomalias (fraude/valores atípicos)
- Registo de aplicação
- Pipelines de análise, por exemplo, clickstreams
- Dashboarding em direto
- Arquivo de dados
- Processamento de transações
- Processamento de telemetria do utilizador
- Transmissão em fluxo de telemetria do dispositivo

<iframe width="560" height="315" src="https://www.youtube.com/embed/45wgY-VSk9I" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## <a name="why-use-event-hubs"></a>Por quê utilizar o Event Hubs?

Os dados são valiosos apenas quando existe uma forma fácil de processar e obter informações atempadas de origens de dados. O Event Hubs fornece uma plataforma de processamento de fluxo distribuída com baixa latência e integração perfeita, com serviços de dados e análisedentro e fora do Azure para construir o seu pipeline completo de big data.

Os Hubs de Eventos representam a "porta de entrada" para um pipeline de eventos, denominado frequentemente *ingestor de eventos* em arquiteturas de solução. Um ingestor de eventos é um componente ou serviço que se encontra entre os publicadores de eventos e os consumidores de eventos para desacoplar a produção de uma transmissão de eventos do consumo desses eventos. O Event Hubs fornece uma plataforma de streaming unificada com tampão de retenção de tempo, dissociando os produtores de eventos dos consumidores de eventos.

As secções seguintes descrevem as principais funcionalidades do serviço Hubs de Eventos do Azure:

## <a name="fully-managed-paas"></a>PaaS totalmente gerido

O Event Hubs é uma Plataforma-as-A-Service (PaaS) totalmente gerida, com pouca configuração ou despesa de gestão, por isso foca-te nas soluções de negócio. [Os Hubs de Eventos para ecossistemas do Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) oferecem-lhe experiência de PaaS Kafka sem ter de gerir, configurar ou executar os seus clusters.

## <a name="support-for-real-time-and-batch-processing"></a>Suporte para processamento em tempo real e em lotes

Ingira, coloque em memória intermédia, armazene e processe a sua transmissão em fluxo em tempo real para obter informações acionáveis. O Event Hubs utiliza um modelo de [consumidor dividido,](event-hubs-scalability.md#partitions)permitindo que várias aplicações processem o fluxo simultaneamente e permitem controlar a velocidade de processamento.

[Capture](event-hubs-capture-overview.md) os seus dados em tempo quase real num [armazenamento de Blob Azure](https://azure.microsoft.com/services/storage/blobs/) ou no Armazenamento de [Lagos De Dados Azure](https://azure.microsoft.com/services/data-lake-store/)para retenção a longo prazo ou processamento de micro-lote. Você pode alcançar este comportamento no mesmo fluxo que você usa para derivar analítica em tempo real. A criação de dados do evento é rápida. Não existem custos administrativos para executá-lo, e escala automaticamente com [unidades](event-hubs-scalability.md#throughput-units)de produção de Hubs de Eventos . O Event Hubs permite-lhe focar-se no processamento de dados e não na captura de dados.

Os Hubs de Eventos do Azure também se integram nas [Funções do Azure](/azure/azure-functions/) para uma arquitetura sem servidor.

## <a name="scalable"></a>Escalável

Com os Hubs de Eventos, pode começar com fluxos de dados em megabytes e aumentar para gigabytes ou terabytes. A funcionalidade [Ampliação automática](event-hubs-auto-inflate.md) é uma das muitas opções disponíveis para dimensionar o número de unidades de débito para satisfazer as suas necessidades de utilização.

## <a name="rich-ecosystem"></a>Ecossistema avançado

[Os Hubs de Eventos para os ecossistemas Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) permitem que os clientes e aplicações apache Kafka [(1.0 e mais tarde)](https://kafka.apache.org/) falem com os Event Hubs. Não precisas de configurar, configurar e gerir os teus próprios aglomerados Kafka.

Com um vasto ecossistema disponível em várias línguas [.NET](https://github.com/Azure/azure-sdk-for-net/), [Java,](https://github.com/Azure/azure-sdk-for-java/) [Python,](https://github.com/Azure/azure-sdk-for-python/) [JavaScript,](https://github.com/Azure/azure-sdk-for-js/)pode facilmente começar a processar os seus fluxos a partir de Event Hubs. Todas as linguagens de cliente suportadas oferecem integração de nível baixo. O ecossistema também lhe proporciona uma integração perfeita com serviços Azure Stream Analytics e Azure E assim permite-lhe construir arquiteturas sem servidores.

## <a name="key-architecture-components"></a>Principais componentes da arquitetura
Os Hubs de Eventos contêm os seguintes [componentes principais](event-hubs-features.md):

- **Produtores de eventos**: qualquer entidade que envie dados para um hub de eventos. Os publicadores de eventos podem publicar eventos através de HTTPS ou AMQP 1.0 ou Apache Kafka (1.0 ou superior)
- **Partições**: cada consumidor só lê um subconjunto específico, ou partição do fluxo de mensagens.
- **Grupos de consumidores**: uma vista (estado, posição ou desvio) de um hub de eventos completo. Os grupos de consumidores permitem que cada um dos grupos de consumidores tenha uma visão separada do fluxo de eventos. Eles lêem o fluxo independentemente ao seu próprio ritmo e com as suas próprias compensações.
- **Unidades de débito**: unidades de capacidade previamente compradas que controlam a capacidade de débito dos Hubs de Eventos.
- **Recetores de eventos**: qualquer entidade que leia os dados de eventos de um hub de eventos. Todos os consumidores do Event Hubs ligam-se através da sessão AMQP 1.0. O serviço Event Hubs oferece eventos através de uma sessão à medida que se tornam disponíveis. Todos os consumidores de Kafka ligam através do protocolo de Kafka 1.0 e posterior.

A imagem seguinte mostra a arquitetura do processamento de fluxos dos Hubs de Eventos:

![Hubs de Eventos](./media/event-hubs-about/event_hubs_architecture.png)

## <a name="event-hubs-on-azure-stack-hub"></a>Hubs de eventos no Hub de Stack Azure
Os Hubs de Eventos no Azure Stack Hub permitem-lhe realizar cenários de nuvem híbrida. As soluções de streaming e baseadas em eventos são suportadas, tanto no local como no processamento de nuvens Azure. Quer o seu cenário seja híbrido (conectado) ou desligado, a sua solução pode suportar o processamento de eventos/streams em larga escala. O seu cenário está apenas ligado ao tamanho do cluster Event Hubs, que pode fornecer de acordo com as suas necessidades. 

As edições do Event Hubs (no Azure Stack Hub e no Azure) oferecem um alto grau de paridade de recursos. Esta paridade significa que Os DSDs, amostras, PowerShell, CLI e portais oferecem uma experiência semelhante, com poucas diferenças. 

Os Hubs de Eventos em Stack são gratuitos durante a pré-visualização pública. Para mais informações, consulte o [Event Hubs no Azure Stack Hub.](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)


## <a name="next-steps"></a>Passos seguintes

Para começar a usar Hubs de Eventos, consulte o Envio e receba tutoriais de **eventos:**

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
- [Ir](event-hubs-go-get-started-send.md)
- [C (apenas enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (apenas receber)](event-hubs-storm-getstarted-receive.md)


Para saber mais sobre os Hub de Eventos, veja os artigos seguintes:

- [Descrição geral das funcionalidades dos Hubs de Eventos](event-hubs-features.md)
- [Perguntas frequentes.](event-hubs-faq.md)


