---
title: Visão geral dos centros de eventos dedicados - Azure Event Hubs / Microsoft Docs
description: Este artigo fornece uma visão geral dos centros de eventos azure dedicados, que oferece implantações de centros de eventos de inquilino único.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72516742"
---
# <a name="overview-of-event-hubs-dedicated"></a>Visão geral dos Centros de Eventos Dedicados

*Os clusters do Event Hubs* oferecem implementações de inquilinos únicos para clientes com as necessidades de streaming mais exigentes. Esta oferta de inquilino único tem um SLA garantido de 99,99% e está disponível apenas no nosso nível de preços dedicado. Um cluster de Hubs de Eventos pode ingressar milhões de eventos por segundo com capacidade garantida e latência sub-segundo. Espaços de nome e centros de eventos criados dentro do cluster dedicado incluem todas as características da oferta Standard e muito mais, mas sem limites de ingresso. Também inclui a popular funcionalidade de captura de Hubs de [Eventos](event-hubs-capture-overview.md) sem custos adicionais, permitindo-lhe automaticamente emulá-lo e registar fluxos de dados para O Armazenamento Azure ou Azure Data Lake. 

Os clusters são provisionados e faturados por Unidades de **Capacidade (CUs),** uma quantidade pré-atribuída de CPU e recursos de memória. Pode comprar 1, 2, 4, 8, 12, 16 ou 20 CUs para cada cluster. Quanto é que podes ingerir e transmitir por CU depende de uma variedade de fatores, como o número de produtores e consumidores, a forma da carga útil, a taxa de saída (ver resultados de referência abaixo para mais detalhes). 

> [!NOTE]
> Todos os clusters do Event Hubs são ativados por padrão e suportam pontos finais kafka que podem ser usados pelas suas aplicações baseadas em Kafka existentes. Ter kafka ativado no seu cluster não afeta os seus casos de utilização não-Kafka; não há opção ou necessidade de desativar Kafka num aglomerado.

## <a name="why-dedicated"></a>Por que dedicado?

O Dicated Event Hubs oferece três benefícios convincentes para os clientes que precisam de capacidade ao nível da empresa:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>O arrendamento único garante capacidade para um melhor desempenho

Um cluster dedicado garante capacidade em larga escala, e pode entrar em gigabytes de dados de streaming com armazenamento totalmente durável e latência sub-segundo para acomodar qualquer explosão de tráfego. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Acesso inclusivo e exclusivo a funcionalidades 
A oferta dedicada inclui funcionalidades como Capture sem custos adicionais, bem como acesso exclusivo a funcionalidades futuras como Bring Your Own Key (BYOK). O serviço também gere o equilíbrio de carga, atualizações de SO, patches de segurança e partição para o cliente, para que possa passar menos tempo na manutenção da infraestrutura e mais tempo na construção de funcionalidades do lado do cliente.  

#### <a name="cost-savings"></a>Economia de Custos
Em volumes elevados de ingresso (>100 TUs), um cluster custa significativamente menos por hora do que a aquisição de uma quantidade comparável de unidades de entrada na oferta Standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Centros de Eventos Quotas E Limites Dedicados

A oferta dedicada do Event Hubs é faturada a um preço mensal fixo, com um mínimo de 4 horas de utilização. O nível Dedicado oferece todas as funcionalidades do plano Standard, mas com capacidade de escala empresarial e limites para clientes com cargas de trabalho exigentes. 

| Funcionalidade | Standard | Dedicada |
| --- |:---:|:---:|
| Largura de banda | 20 TUs (até 40 TUs) | 20 CUs |
| Espaços de nomes |  1 | 50 por CU |
| Hubs de Eventos |  10 por espaço de nome | 1000 por espaço de nome |
| Eventos de ingresso | Pagamento por milhão de eventos | Incluído |
| Tamanho da mensagem | 1 Milhão de Bytes | 1 Milhão de Bytes |
| Partições | 32 por Centro de Eventos | 1024 por Event Hub |
| Grupos de consumidores | 20 por Event Hub | Sem limite por CU, 1000 por centro de eventos |
| Ligações intermediadas | 1.000 incluídos, 5.000 no máximo | 100 K incluídoe máximo |
| Retenção de Mensagens | 7 dias, 84 GB incluídos por TU | 90 dias, 10 TB incluídos por CU |
| Captura | Pagamento por hora | Incluído |

## <a name="how-to-onboard"></a>Como embarcar

A experiência self-serve para [criar um cluster de Hubs](event-hubs-dedicated-cluster-create-portal.md) de Eventos através do Portal [Azure](https://aka.ms/eventhubsclusterquickstart) está agora em Pré-visualização. Se tiver alguma dúvida ou precisar de ajuda para embarcar no Event Hubs Dedicado, contacte a equipa do [Event Hubs.](mailto:askeventhubs@microsoft.com)

## <a name="faqs"></a>FAQs

#### <a name="what-can-i-achieve-with-a-cluster"></a>O que posso conseguir com um aglomerado?

Para um cluster de Hubs de Eventos, o quanto pode ingerir e transmitir depende de vários fatores como os seus produtores, consumidores, a taxa a que está a ingerir e processar, e muito mais. 

A tabela seguinte mostra os resultados de referência que alcançámos durante os nossos testes:

| Forma de carga útil | Recetores | Largura de banda de ingresso| Mensagens de ingresso | Largura de banda de Egress | Mensagens de egress | Tosta total | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/seg | 400k mensagens/seg | 800 MB/seg | 800k mensagens/seg | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/seg | 66.6k mensagens/seg | 1.33 GB/seg | 133k mensagens/seg | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/seg | 34k mensagens / seg | 1,05 GB/seg | 34k mensagens/seg | 1000 TUs | 250 TUs |

Nos ensaios, foram utilizados os seguintes critérios:

- Foi utilizado um cluster de Centros de Eventos de nível dedicado com quatro unidades de capacidade (CUs). 
- O centro de eventos usado para ingestão tinha 200 divisórias. 
- Os dados que foram ingeridos foram recebidos por duas aplicações recetoras que receberam de todas as divisórias.

#### <a name="can-i-scale-updown-my-cluster"></a>Posso escalar para cima/para baixo o meu cluster?

Após a criação, os clusters são cobrados por um mínimo de 4 horas de utilização. No lançamento da pré-visualização da experiência self-serve, pode submeter um pedido de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support) à equipa de Hubs de Eventos ao abrigo *de > Técnica > Pedido de Escala para Cima ou Scale Down Cluster dedicado* para escalar o seu cluster para cima ou para baixo. Pode levar até 7 dias para completar o pedido para reduzir o seu cluster. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Como é que a Geo-DR vai trabalhar com o meu agrupamento?

Você pode geo-emparelhar um espaço de nome sob um cluster de nível dedicado com outro espaço de nome sob um cluster de nível dedicado. Não encorajamos a emparelhar um espaço de nome de nível dedicado com um espaço de nome na nossa oferta Standard, uma vez que o limite de entrada será incompatível, o que resultará em erros. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Posso migrar os meus espaços de nome padrão para pertencer a um cluster de nível dedicado?
Atualmente não apoiamos um processo de migração automatizado para migrar os seus dados de hubs de evento de um espaço de nome Standard para um Dedicado. 

## <a name="next-steps"></a>Passos seguintes

Contacte o seu representante de vendas da Microsoft ou o Microsoft Support para obter detalhes adicionais sobre o Event Hubs Dedicado. Você também pode criar um cluster ou aprender mais sobre os níveis de preços do Event Hubs visitando os seguintes links:

- [Criar um cluster de Hubs de Eventos através do Portal Azure](https://aka.ms/eventhubsclusterquickstart) 
- Centros de [Eventos Preços Dedicados.](https://azure.microsoft.com/pricing/details/event-hubs/) Também pode contactar o seu representante de vendas da Microsoft ou o Microsoft Support para obter detalhes adicionais sobre a capacidade dedicada do Event Hubs.
- O [Event Hubs FAQ](event-hubs-faq.md) contém informações sobre preços e responde a algumas perguntas frequentes sobre Centros de Eventos.
