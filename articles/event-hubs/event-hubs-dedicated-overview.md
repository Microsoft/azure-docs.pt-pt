---
title: Visão geral dos centros de eventos dedicados - Azure Event Hubs | Microsoft Docs
description: Este artigo fornece uma visão geral do dedicado Azure Event Hubs, que oferece implementações de inquilinos individuais de centros de eventos.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 721acf354c7d14c1362b4f760982af37d59115f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715620"
---
# <a name="overview-of-event-hubs-dedicated"></a>Visão geral dos Centros de Eventos Dedicados

*Os clusters de eventos Hubs* oferecem implantações de inquilinos únicos para clientes com as necessidades de streaming mais exigentes. Esta oferta de inquilino único tem um SLA garantido de 99,99% e está disponível apenas no nosso nível de preços dedicado. Um cluster Event Hubs pode entrar em milhões de eventos por segundo com capacidade garantida e latência do subsegundo. Os espaços de nome e os centros de eventos criados dentro do cluster dedicado incluem todas as funcionalidades da oferta Standard e muito mais, mas sem limites de entrada. Também inclui a popular funcionalidade [de Captura de Centros de Eventos](event-hubs-capture-overview.md) sem custos adicionais. Esta funcionalidade permite-lhe a lotar e registar automaticamente fluxos de dados para O Azure Storage ou Azure Data Lake. 

Os clusters são a provisionados e faturados por **Unidades de Capacidade (CUs),** uma quantia pré-atribuída de CPU e recursos de memória. Você pode comprar 1, 2, 4, 8, 12, 16 ou 20 CUs para cada cluster. Quanto você pode ingerir e fluir por CU depende de uma variedade de fatores, tais como os seguintes: 

- Número de produtores e consumidores
- Forma de carga útil
- Taxa de Egress

> [!NOTE]
> Todos os clusters de Clusters de Eventos são ativados por Kafka por padrão e suportam pontos finais kafka que podem ser usados pelas aplicações existentes baseadas em Kafka. Ter a Kafka ativada no seu cluster não afeta os seus casos de utilização não-Kafka; não há opção ou necessidade de desativar Kafka num cluster.

## <a name="why-dedicated"></a>Por que dedicado?

O Event Hubs dedicado oferece três benefícios convincentes para os clientes que precisam de capacidade a nível empresarial:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Arrendamento único garante capacidade para um melhor desempenho

Um cluster dedicado garante capacidade em grande escala. Pode entrar até gigabytes de dados de streaming com armazenamento totalmente durável e latência de subsegundo para acomodar qualquer explosão no tráfego. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Acesso inclusivo e exclusivo às funcionalidades 
A oferta dedicada inclui funcionalidades como Capture sem custos adicionais e acesso exclusivo a funcionalidades futuras como Bring Your Own Key (BYOK). O serviço também gere o equilíbrio de carga, atualizações de SO, patches de segurança e divisórias. Assim, você pode passar menos tempo na manutenção da infraestrutura e mais tempo na construção de recursos do lado do cliente.  

#### <a name="cost-savings"></a>Poupança de Custos
Em volumes de entrada elevados (>100 unidades de produção), um cluster custa significativamente menos por hora do que a compra de uma quantidade comparável de unidades de produção na oferta Standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Quotas e limites dedicados do Event Hubs

A oferta dedicada do Event Hubs é faturada a um preço mensal fixo, com um mínimo de 4 horas de utilização. O tier dedicado oferece todas as características do plano padrão, mas com capacidade e limites à escala empresarial para clientes com cargas de trabalho exigentes. 

| Funcionalidade | Standard | Dedicada |
| --- |:---|:---|
| Largura de banda | 20 TUs (até 40 TUs) | 20 CUs |
| Espaços de nomes |  1 | 50 por CU |
| Hubs de Eventos |  10 por espaço de nome | 1000 por espaço de nome |
| Eventos ingressos | Pagar por milhão de eventos | Incluídos |
| Tamanho da mensagem | 1 Milhão de Bytes | 1 Milhão de Bytes |
| Partições | 32 por Centro de Eventos | 1024 por centro de eventos<br/>2000 por CU |
| Grupos de consumidores | 20 por Centro de Eventos | Sem limite por CU, 1000 por centro de eventos |
| Conexões intermediadas | 1.000 incluídos, 5.000 no máximo | 100 K incluído e máx |
| [Retenção de eventos](event-hubs-features.md#event-retention) | 7 dias, 84 GB incluídos por TU | 90 dias, 10 TB incluídos por CU |
| Recolha | Pagamento por hora | Incluídos |

Para mais quotas e limites, consulte [as quotas e limites do Event Hubs](event-hubs-quotas.md)

## <a name="how-to-onboard"></a>Como embarcar

A experiência de autosserviço para [criar um cluster de Clusters de Eventos](event-hubs-dedicated-cluster-create-portal.md) através do portal [Azure](https://aka.ms/eventhubsclusterquickstart) está agora em Pré-Visualização. Se tiver alguma dúvida ou precisar de ajuda no embarque para o Event Hubs Dedicado, contacte a equipa do [Event Hubs](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>FAQs

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Passos seguintes

Contacte o seu representante de vendas da Microsoft ou o Microsoft Support para obter detalhes adicionais sobre os Centros de Eventos Dedicados. Também pode criar um cluster ou saber mais sobre os níveis de preços do Event Hubs visitando os seguintes links:

- [Criar um cluster de Centros de Eventos através do portal Azure](https://aka.ms/eventhubsclusterquickstart) 
- [Preços dedicados ao Evento Hubs.](https://azure.microsoft.com/pricing/details/event-hubs/) Também pode contactar o seu representante de vendas da Microsoft ou o Microsoft Support para obter detalhes adicionais sobre a capacidade dedicada do Event Hubs.
- O [Event Hubs FAQ](event-hubs-faq.md) contém informações sobre preços e responde a algumas perguntas frequentes sobre Os Centros de Eventos.
