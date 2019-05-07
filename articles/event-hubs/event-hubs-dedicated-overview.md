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
ms.openlocfilehash: e8a2d8321a42e8b3d090c1ce1fdb3fd9a7ee3714
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138665"
---
# <a name="overview-of-event-hubs-dedicated"></a>Descrição geral dos Hubs de eventos dedicados

Os clusters de Hubs de eventos do Azure oferecem implementações de inquilino único para os clientes com as necessidades de transmissão em fluxo mais exigentes. Esta oferta de inquilino único tem um SLA de 99,99% garantido. Está disponível apenas em dedicado escalão de preço.

Um cluster de Hubs de eventos pode entrada milhões de eventos por segundo com capacidade garantida e a latência de subsecond. Os hubs de eventos e espaços de nomes criados dentro do cluster dedicado incluem todas as funcionalidades da oferta Standard e muito mais, mas sem limites de entrada. Ele também inclui a [captura de Hubs de eventos](event-hubs-capture-overview.md) funcionalidade sem custos adicionais. Pode usá-lo automaticamente batch e de registo de transmissões de dados para o armazenamento do Azure ou do Azure Data Lake.

Clusters dedicados são aprovisionados e faturadas por unidades de capacidade (CUs). CUs são uma quantidade preallocated de recursos de CPU e memória. Pode comprar 1, 2, 4, 8, 12, 16 ou 20 CUs para cada cluster. Quanto pode ingerir e stream por CU depende de fatores como o número de produtores e consumidores, a forma de payload e a taxa de saída.

Para obter mais informações, consulte a tabela com os resultados do benchmark.

## <a name="why-use-event-hubs-dedicated"></a>Porquê utilizar os Hubs de eventos dedicados?

Os Hubs de eventos dedicados oferece três benefícios para os clientes que precisam de capacidade de nível empresarial.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Inquilinos de único garante a capacidade para um melhor desempenho

Um cluster dedicado garante a capacidade em escala completa. Pode receber até de gigabytes de dados de transmissão em fluxo com latência de armazenamento e subsecond totalmente durável para acomodar aumentos repentinos de tráfego.

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inclusive e exclusivo acesso às funcionalidades 
A oferta dedicada e inclui recursos como captura sem custos adicionais. Ele também oferece acesso exclusivo às funcionalidades futuras, como o BYOK. O serviço também gere o balanceamento de carga, OS atualizações, patches de segurança e criação de partições. Com estas capacidades, pode gastar menos tempo na manutenção da infraestrutura e mais tempo na criação de funcionalidades do lado do cliente.

#### <a name="cost-savings"></a>Poupança de custos
Em volumes de entrada elevado, um cluster custos significativamente menor por hora, que se compra uma quantidade comparável de unidades de débito (n) na oferta do padrão. É um grande volume > 100 TUs.


## <a name="event-hubs-standard-vs-dedicated"></a>Vs Standard dos Hubs de eventos. Dedicado

A tabela seguinte compara os escalões de serviço disponível dos Hubs de eventos. A oferta de Hubs de eventos dedicados é faturada por um preço mensal fixo, em comparação comparado a utilização de preços para a maioria das funcionalidades do Standard. O escalão dedicado oferece todas as funcionalidades do plano padrão, mas com capacidade de escala empresarial para os clientes com cargas de trabalho exigentes.

| Funcionalidade | Standard | Dedicado |
| --- |:---:|:---:|
| Eventos de entrada | Pagar por milhão de eventos | Incluída |
| Unidade de débito (entrada de 1 MB/s, saída 2 MB/seg) | Pagar por hora | Incluída |
| Tamanho da mensagem | 1 MB | 1 MB |
| Partições | 40 por espaço de nomes | 2000 por CU |
| Grupos de consumidores | 20 por hub de eventos | 1000 por hub de eventos |
| Largura de banda máxima | 20 TUs (até 40 TUs) | 20 CUs |
| Ligações mediadas | 1000 incluídos | 100.000 incluídos |
| Retenção de mensagens | Um dia incluído | Até sete dias incluídos |
| Captura | Pagar por hora | Incluída |

## <a name="what-can-i-achieve-with-a-cluster"></a>O que posso fazer com um cluster?

Para um cluster de Hubs de eventos, quanto pode ingerir e transmitir em fluxo depende sua produtores, os consumidores, a taxa a que a ingerir e processar e muito mais.

A tabela seguinte mostra os resultados do benchmark que foram obtidos durante o teste.

| Forma de payload | Recetores | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/seg | 400 000 mensagens por segundo | 800 MB/seg | 800.000 mensagens/seg | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/seg | 66,600 mensagens/seg | 1.33 GB/seg | 133,000 mensagens/seg | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/seg | 34,000 mensagens/seg | 1,05 GB/seg | 34,000 mensagens/seg | 1000 TUs | 250 TUs |

Nos testes, foram utilizados os seguintes critérios:

- Foi utilizado um cluster de Hubs de eventos do escalão dedicado com quatro unidades de capacidade.
- O hub de eventos utilizado para ingestão tinha 200 partições.
- Os dados que foi ingeridos recebeu dois aplicativos de recetor. Eles receberam dados de todas as partições.

## <a name="use-event-hubs-dedicated"></a>Utilizar Hubs de eventos dedicados

Para utilizar os Hubs de eventos dedicados, [contacte o suporte de faturação](https://ms.portal.azure.com/#create/Microsoft.Support) ou seu representante da Microsoft. Pode aumentar sua capacidade ou reduzir verticalmente ao longo do mês para atender às suas necessidades ao adicionar ou remover CUs. A equipe de produto de Hubs de eventos ajuda-o a obter a implementação flexível, que é adequada para si.

## <a name="next-steps"></a>Passos Seguintes

Contacte o seu representante de vendas da Microsoft ou Support da Microsoft para obter informações adicionais sobre a capacidade de Hubs de eventos dedicados. Para saber mais sobre os Hubs de eventos escalões de preço, utilize as seguintes ligações:

- [Preços de dedicado de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/). Também pode contactar o seu representante de vendas da Microsoft ou Support da Microsoft para obter informações adicionais sobre a capacidade de Hubs de eventos dedicados.
- O [FAQ dos Hubs de eventos](event-hubs-faq.md) contém informações sobre preços e responde a algumas perguntas frequentes sobre os Hubs de eventos.
