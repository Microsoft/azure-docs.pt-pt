---
title: Visão geral dos centros de eventos dedicados - Azure Event Hubs Microsoft Docs
description: Este artigo fornece uma visão geral do dedicado Azure Event Hubs, que oferece implementações de inquilinos individuais de centros de eventos.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 70061b5dc4fe72c9fd2fd60dd8c67da31b1d1e6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85322437"
---
# <a name="overview-of-event-hubs-dedicated"></a>Visão geral dos Centros de Eventos Dedicados

*Os clusters de eventos Hubs* oferecem implantações de inquilinos únicos para clientes com as necessidades de streaming mais exigentes. Esta oferta de inquilino único tem um SLA garantido de 99,99% e está disponível apenas no nosso nível de preços dedicado. Um cluster Event Hubs pode entrar em milhões de eventos por segundo com capacidade garantida e latência sub-segundo. Os espaços de nome e os centros de eventos criados dentro do cluster dedicado incluem todas as funcionalidades da oferta Standard e muito mais, mas sem limites de entrada. Também inclui a popular funcionalidade [de Captura de Centros de Eventos](event-hubs-capture-overview.md) sem custos adicionais, permitindo-lhe a triagem automática de dados para O Azure Storage ou Azure Data Lake. 

Os clusters são a provisionados e faturados por **Unidades de Capacidade (CUs),** uma quantia pré-atribuída de CPU e recursos de memória. Você pode comprar 1, 2, 4, 8, 12, 16 ou 20 CUs para cada cluster. Quanto você pode ingerir e fluir por CU depende de uma variedade de fatores, tais como o número de produtores e consumidores, forma de carga útil, taxa de saída (ver resultados de referência abaixo para mais detalhes). 

> [!NOTE]
> Todos os clusters de Clusters de Eventos são ativados por Kafka por padrão e suportam pontos finais kafka que podem ser usados pelas aplicações existentes baseadas em Kafka. Ter a Kafka ativada no seu cluster não afeta os seus casos de utilização não-Kafka; não há opção ou necessidade de desativar Kafka num cluster.

## <a name="why-dedicated"></a>Por que dedicado?

O Event Hubs dedicado oferece três benefícios convincentes para os clientes que precisam de capacidade a nível empresarial:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Arrendamento único garante capacidade para um melhor desempenho

Um cluster dedicado garante capacidade em plena escala, e pode entrar até gigabytes de dados de streaming com armazenamento totalmente durável e latência sub-segundo para acomodar qualquer explosão no tráfego. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Acesso inclusivo e exclusivo às funcionalidades 
A oferta dedicada inclui funcionalidades como Capture sem custos adicionais, bem como acesso exclusivo a funcionalidades futuras como Bring Your Own Key (BYOK). O serviço também gere o equilíbrio de carga, atualizações de SO, patches de segurança e divisórias para o cliente, para que possa passar menos tempo na manutenção da infraestrutura e mais tempo na construção de funcionalidades do lado do cliente.  

#### <a name="cost-savings"></a>Poupança de Custos
Em volumes de entrada elevados (>100 TUs), um cluster custa significativamente menos por hora do que a compra de uma quantidade comparável de unidades de produção na oferta Standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Centros de Eventos Quotas e Limites Dedicados

A oferta dedicada do Event Hubs é faturada a um preço mensal fixo, com um mínimo de 4 horas de utilização. O tier dedicado oferece todas as características do plano Standard, mas com capacidade de escala empresarial e limites para clientes com cargas de trabalho exigentes. 

| Funcionalidade | Standard | Dedicada |
| --- |:---:|:---:|
| Largura de banda | 20 TUs (até 40 TUs) | 20 CUs |
| Espaços de nomes |  1 | 50 por CU |
| Hubs de Eventos |  10 por espaço de nome | 1000 por espaço de nome |
| Eventos ingressos | Pagar por milhão de eventos | Incluída |
| Tamanho da mensagem | 1 Milhão de Bytes | 1 Milhão de Bytes |
| Partições | 32 por Centro de Eventos | 1024 por Centro de Eventos |
| Grupos de consumidores | 20 por Centro de Eventos | Sem limite por CU, 1000 por centro de eventos |
| Conexões intermediadas | 1.000 incluídos, 5.000 no máximo | 100 K incluído e máx |
| Retenção de mensagens | 7 dias, 84 GB incluídos por TU | 90 dias, 10 TB incluídos por CU |
| Recolha | Pagamento por hora | Incluída |

## <a name="how-to-onboard"></a>Como embarcar

A experiência de autosserviço para [criar um cluster de Clusters de Eventos](event-hubs-dedicated-cluster-create-portal.md) através do Portal [Azure](https://aka.ms/eventhubsclusterquickstart) está agora em Pré-Visualização. Se tiver alguma dúvida ou precisar de ajuda a bordo do Event Hubs Dedicado, contacte a equipa do [Event Hubs.](mailto:askeventhubs@microsoft.com)

## <a name="faqs"></a>FAQs

#### <a name="what-can-i-achieve-with-a-cluster"></a>O que posso conseguir com um aglomerado?

Para um cluster Event Hubs, quanto você pode ingerir e stream depende de vários fatores, como os seus produtores, consumidores, a taxa a que você está ingerindo e transformando, e muito mais. 

A tabela seguinte mostra os resultados de referência que conseguimos durante os nossos testes:

| Forma de carga útil | Recetores | Largura de banda ingress| Mensagens ingress | Largura de banda da Egress | Mensagens de egress | TUs totais | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/seg | 400k mensagens/seg | 800 MB/seg | 800k mensagens/seg | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/seg | 66.6k mensagens/seg | 1,33 GB/seg | Mensagens de 133k/seg | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/seg | Mensagens de 34k /seg | 1,05 GB/seg | Mensagens de 34k/seg | 1000 TUs | 250 TUs |

No ensaio, foram utilizados os seguintes critérios:

- Foi utilizado um cluster de centros de eventos de nível dedicado com quatro unidades de capacidade (CUs). 
- O centro de eventos usado para a ingestão tinha 200 divisórias. 
- Os dados que foram ingeridos foram recebidos por duas aplicações recetoras recebidas de todas as divisórias.

#### <a name="can-i-scale-updown-my-cluster"></a>Posso escalar para cima/para baixo o meu aglomerado?

Após a criação, os clusters são faturados para um mínimo de 4 horas de uso. No lançamento de Pré-visualização da experiência de autosserviço, pode submeter um pedido de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support) à equipa de Centros de Eventos ao abrigo *do > Técnico Quota > Pedido de Escala para Cima ou Escala Para Baixo Cluster dedicado* para escalar o seu cluster para cima ou para baixo. Pode levar até 7 dias para completar o pedido para reduzir o seu cluster. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Como é que a Geo-DR vai trabalhar com o meu agrupamento?

Você pode geo-emparelhar um espaço de nome sob um cluster de nível dedicado com outro espaço de nome sob um cluster de nível dedicado. Não encorajamos a emparelhar um espaço de nome dedicado com um espaço de nome na nossa oferta Standard, uma vez que o limite de produção será incompatível, o que resultará em erros. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Posso migrar os meus espaços de nome padrão para pertencer a um cluster de nível dedicado?
Não apoiamos atualmente um processo de migração automatizado para migrar os dados dos centros de eventos de um espaço de nome Standard para um Dedicado. 

## <a name="next-steps"></a>Passos seguintes

Contacte o seu representante de vendas da Microsoft ou o Microsoft Support para obter detalhes adicionais sobre os Centros de Eventos Dedicados. Também pode criar um cluster ou saber mais sobre os níveis de preços do Event Hubs visitando os seguintes links:

- [Criar um cluster de Centros de Eventos através do Portal Azure](https://aka.ms/eventhubsclusterquickstart) 
- [Preços dedicados ao Evento Hubs.](https://azure.microsoft.com/pricing/details/event-hubs/) Também pode contactar o seu representante de vendas da Microsoft ou o Microsoft Support para obter detalhes adicionais sobre a capacidade dedicada do Event Hubs.
- O [Event Hubs FAQ](event-hubs-faq.md) contém informações sobre preços e responde a algumas perguntas frequentes sobre Os Centros de Eventos.
