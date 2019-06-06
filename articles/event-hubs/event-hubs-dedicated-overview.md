---
title: Descrição geral dos hubs de eventos dedicados - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral dos Hubs de eventos do Azure dedicada, que oferece implementações de inquilino único dos hubs de eventos.
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
ms.openlocfilehash: 52e092e6e48f004656860cb5d078e780039584ab
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730245"
---
# <a name="overview-of-event-hubs-dedicated"></a>Descrição geral dos Hubs de eventos dedicados

*Clusters de Hubs de eventos* oferecer implementações de inquilino único para os clientes com as necessidades de transmissão em fluxo mais exigentes. Esta oferta de inquilino único tem um SLA de 99,99% garantido e está disponível apenas em nossa dedicado escalão de preço. Um cluster de Hubs de eventos pode entrada milhões de eventos por segundo com capacidade garantida e a latência de frações de segundos. Os hubs de eventos e espaços de nomes criados dentro do cluster dedicado incluem todas as funcionalidades da oferta Standard e muito mais, mas sem limites de entrada. Ele também inclui o popular [captura de Hubs de eventos](event-hubs-capture-overview.md) funcionalidade sem custos adicionais, que lhe permite automaticamente batch e de registo de transmissões de dados para o armazenamento do Azure ou do Azure Data Lake. 

Clusters são aprovisionados e a faturação é ao **unidades de capacidade (CUs)** , uma quantidade previamente alocada de recursos de CPU e memória. Pode comprar CUs 1, 2, 4, 8, 12, 16 ou 20 para cada cluster. Quanto pode ingerir e transmitir em fluxo por CU depende de diversos fatores, como o número de produtores e consumidores, a forma de payload, saída de velocidade (veja os resultados do benchmark abaixo para obter mais detalhes). 

> [!NOTE]
> Todos os clusters de Hubs de eventos são Kafka ativada por predefinição e suporta pontos finais de Kafka que podem ser utilizados pelo seu existente Kafka com base em aplicações. Ter Kafka ativado no seu cluster não afeta seus casos de uso não Kafka; não existe nenhuma opção ou a necessidade de desativar o Kafka num cluster.

## <a name="why-dedicated"></a>Por isso que dedicado?

Os Hubs de eventos dedicados oferece três benefícios atraentes para os clientes que precisam de capacidade de nível empresarial:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Inquilinos de único garante a capacidade para um melhor desempenho

Um cluster dedicado garante a capacidade em escala completa e pode entrada até de gigabytes de dados de transmissão em fluxo com latência de armazenamento e de frações de segundos totalmente durável para acomodar qualquer picos no tráfego. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inclusive e exclusivo acesso às funcionalidades 
A oferta dedicada e inclui recursos como a captura em nenhum custo adicional, bem como acesso exclusivo às funcionalidades futuras, como o BYOK. O serviço também gere o balanceamento de carga, OS atualizações, patches de segurança e criação de partições para o cliente, para que possa passar menos tempo na manutenção da infraestrutura e mais tempo na criação de funcionalidades do lado do cliente.  

#### <a name="cost-savings"></a>Economia de custos
Em volumes de entrada elevado (> 100 TUs), um cluster os custos substancialmente inferior por hora comprar uma quantidade comparável de unidades de débito na oferta do padrão.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Os Hubs de eventos dedicados Quotas e limites

A oferta de Hubs de eventos dedicados é faturada por um preço mensal fixo, com um mínimo de 4 horas de utilização. O escalão dedicado oferece todas as funcionalidades do plano padrão, mas com capacidade de dimensionamento empresarial e limites para os clientes com cargas de trabalho exigentes. 

| Funcionalidade | Standard | Dedicado |
| --- |:---:|:---:|
| Largura de banda | 20 TUs (até 40 TUs) | 20 CUs |
| Espaços de nomes |  1 | 50 por CU |
| Hubs de Eventos |  10 por espaço de nomes | 1000 por espaço de nomes |
| Eventos de entrada | Pagar por milhão de eventos | Incluída |
| Tamanho da Mensagem | 1 milhão de Bytes | 1 milhão de Bytes |
| Partições | 40 por espaço de nomes | 2000 por CU |
| Grupos de consumidores | 20 por Hub de eventos | Sem limite por CU, 1000 por hub de eventos |
| Ligações mediadas | Máx. de 5.000 em 1000 incluído, | 100 mil incluídos e máx. |
| Retenção de Mensagens | 7 dias, 84 GB incluído por TU | 90 dias, 10 TB, incluído por CU |
| Captura | Pagar por hora | Incluída |

## <a name="how-to-onboard"></a>Como integrar

A experiência de gestão personalizada para [criar um cluster de Hubs de eventos](event-hubs-dedicated-cluster-create-portal.md) através da [Portal do Azure](https://aka.ms/eventhubsclusterquickstart) está agora em pré-visualização. Se tiver alguma dúvida ou precisa de ajuda inclusão para os Hubs de eventos dedicados, entre em contato com o [equipa dos Hubs de eventos](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>FAQs

#### <a name="what-can-i-achieve-with-a-cluster"></a>O que posso fazer com um cluster?

Para um cluster de Hubs de eventos, quanto pode ingerir e transmitir em fluxo depende de vários fatores, tais como os produtores, os consumidores, a taxa a que são a ingerir e processar e muito mais. 

Tabela a seguir mostra os resultados do benchmark que obtivemos durante os nossos testes:

| Forma de payload | Recetores | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/seg | 400 mil mensagens/seg | 800 MB/seg | 800 mil mensagens/seg | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/seg | 66.6 mil mensagens/seg | 1.33 GB/seg | 133 mil mensagens/seg | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/seg | 34 mil mensagens / seg | 1,05 GB/seg | 34 mil mensagens/seg | 1000 TUs | 250 TUs |

No teste, utilizou-se os seguintes critérios:

- Um cluster de Hubs de eventos de escalão dedicado com quatro unidades de capacidade (CUs) foi utilizado. 
- O hub de eventos utilizado para ingestão tinha 200 partições. 
- Os dados que foi ingeridos recebeu dois aplicativos de recetor recebimento de todas as partições.

#### <a name="can-i-scale-updown-my-cluster"></a>Posso Dimensionar para cima ou para baixo do meu cluster?

Após a criação, os clusters são cobrados por um mínimo de 4 horas de utilização. Na versão de pré-visualização da experiência de Self-Service, pode submeter um [pedido de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para a equipe de Hubs de eventos sob *técnica > Quota > pedido para aumentar verticalmente ou dimensionar para baixo dedicado Cluster* para dimensionamento o cluster ou reduzir verticalmente. Pode demorar até 7 dias para concluir o pedido para reduzir verticalmente o cluster. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Como o Geo-DR irá funcionar com meu cluster?

Pode geo-par um espaço de nomes num cluster de escalão dedicado com outro espaço de nomes num cluster de escalão dedicado. Não é recomendável emparelhamento um espaço de nomes do escalão dedicado com um espaço de nomes na nossa oferta padrão, uma vez que o limite de taxa de transferência estará incompatíveis que irá resultar em erros. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Posso migrar meu espaços de nomes Standard pertence a um cluster de escalão dedicado?
Nós não suportam atualmente um processo de migração automatizada para migrar os dados de hubs de eventos de um espaço de nomes padrão para um dedicado um. 

## <a name="next-steps"></a>Passos Seguintes

Contacte o seu representante de vendas da Microsoft ou Support da Microsoft para obter detalhes adicionais sobre Hubs de eventos dedicados. Também pode criar um cluster ou obter mais informações sobre os Hubs de eventos escalões de preço, visitando os links a seguir:

- [Criar um cluster de Hubs de eventos através do Portal do Azure](https://aka.ms/eventhubsclusterquickstart) 
- [Preços de dedicado de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/). Também pode contactar o seu representante de vendas da Microsoft ou Support da Microsoft para obter detalhes adicionais sobre a capacidade de Hubs de eventos dedicados.
- O [FAQ dos Hubs de eventos](event-hubs-faq.md) contém informações sobre preços e responde a algumas perguntas frequentes sobre os Hubs de eventos.
