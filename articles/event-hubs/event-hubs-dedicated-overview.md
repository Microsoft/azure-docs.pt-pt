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
ms.openlocfilehash: 52d46009464c7417d5b525154fdac09c030aabe7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708005"
---
# <a name="overview-of-event-hubs-dedicated"></a>Descrição geral dos Hubs de eventos dedicados

*Clusters de Hubs de eventos* oferecer implementações de inquilino único para os clientes com as necessidades de transmissão em fluxo mais exigentes. Esta oferta de inquilino único tem um SLA de 99,99% garantido e está disponível apenas em nossa dedicado escalão de preço. Um cluster de Hubs de eventos pode entrada milhões de eventos por segundo com capacidade garantida e a latência de frações de segundos. Os hubs de eventos e espaços de nomes criados dentro do cluster dedicado incluem todas as funcionalidades da oferta Standard e muito mais, mas sem limites de entrada. Ele também inclui o popular [captura de Hubs de eventos](event-hubs-capture-overview.md) funcionalidade sem custos adicionais, que lhe permite automaticamente batch e de registo de transmissões de dados para o armazenamento do Azure ou do Azure Data Lake. 

Clusters dedicados são aprovisionados e a faturação é ao **unidades de capacidade (CUs)**, uma quantidade previamente alocada de recursos de CPU e memória. Pode comprar CUs 1, 2, 4, 8, 12, 16 ou 20 para cada cluster. Quanto pode ingerir e transmitir em fluxo por CU depende de diversos fatores, como o número de produtores e consumidores, a forma de payload, saída de velocidade (veja os resultados do benchmark abaixo para obter mais detalhes). 

## <a name="why-dedicated"></a>Por isso que dedicado?

Os Hubs de eventos dedicados oferece três benefícios atraentes para os clientes que precisam de capacidade de nível empresarial:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Inquilinos de único garante a capacidade para um melhor desempenho

Um cluster dedicado garante a capacidade em escala completa e pode entrada até de gigabytes de dados de transmissão em fluxo com latência de armazenamento e de frações de segundos totalmente durável para acomodar qualquer picos no tráfego. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inclusive e exclusivo acesso às funcionalidades 
A oferta dedicada e inclui recursos como a captura em nenhum custo adicional, bem como acesso exclusivo às funcionalidades futuras, como o BYOK. O serviço também gere o balanceamento de carga, OS atualizações, patches de segurança e criação de partições para o cliente, para que possa passar menos tempo na manutenção da infraestrutura e mais tempo na criação de funcionalidades do lado do cliente.  

#### <a name="cost-savings"></a>Economia de custos
Em volumes de entrada elevado (> 100 TUs), um cluster os custos substancialmente inferior por hora comprar uma quantidade comparável de unidades de débito na oferta do padrão.


## <a name="event-hubs-standard-vs-dedicated"></a>Vs Standard dos Hubs de eventos. Dedicado

A tabela seguinte compara os escalões de serviço disponível dos Hubs de eventos. A oferta de Hubs de eventos dedicados é faturada por um preço mensal fixo, em comparação comparado a utilização de preços para a maioria das funcionalidades do Standard. O escalão dedicado oferece todas as funcionalidades do plano padrão, mas com capacidade de dimensionamento de enterprise para clientes com cargas de trabalho exigentes. 

| Funcionalidade | Standard | Dedicado |
| --- |:---:|:---:|
| Eventos de entrada | Pagar por milhão de eventos | Incluída |
| Unidade de débito (entrada de 1 MB/s, saída 2 MB/seg) | Pagar por hora | Incluída |
| Tamanho da Mensagem | 1 MB | 1 MB |
| Partições | 40 por espaço de nomes | 2000 por CU |
| Grupos de consumidores | 20 por Hub de eventos | 1000 por Hub de eventos |
| Um máximo de Largura de Banda | 20 TUs (até 40 TUs)    | 20 CUs |
| Ligações mediadas | 1000 incluídos | 100 mil incluídos |
| Retenção de Mensagens | 1 dia incluído | Até 7 dias incluídos |
| Captura | Pagar por hora | Incluída |

## <a name="what-can-i-achieve-with-a-cluster"></a>O que posso fazer com um cluster?

Para um cluster de Hubs de eventos, quanto pode ingerir e transmitir em fluxo depende de vários fatores, tais como os produtores, os consumidores, a taxa a que são a ingerir e processar e muito mais. 

Tabela a seguir mostra os resultados do benchmark que obtivemos durante os nossos testes:

| Forma de payload | Recetores | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/seg | 400 mil msgs/seg | 800 MB/seg | 800 k msgs/seg | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/seg | 66.6 k msgs/seg | 1.33 GB/seg | 133 k msgs/seg | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/seg | 34 k msgs / seg | 1,05 GB/seg | 34 k msgs/seg | 1000 TUs | 250 TUs |

No teste, utilizou-se os seguintes critérios:

- Um cluster de Hubs de eventos de escalão dedicado com quatro unidades de capacidade (CUs) foi utilizado. 
- O hub de eventos utilizado para ingestão tinha 200 partições. 
- Os dados que foi ingeridos recebeu dois aplicativos de recetor recebimento de todas as partições.

## <a name="how-to-onboard"></a>Como integrar

A carregar para este SKU [contacte o suporte de faturação](https://ms.portal.azure.com/#create/Microsoft.Support) ou seu representante da Microsoft. Pode aumentar sua capacidade ou reduzir verticalmente ao longo do mês para atender às suas necessidades ao adicionar ou remover CUs. O plano dedicado é o único que irá ocorrer uma integração mais prática da equipe de produto dos Hubs de eventos para obter a implementação flexível, que é adequada para si. 

## <a name="next-steps"></a>Passos Seguintes

Contacte o seu representante de vendas da Microsoft ou Support da Microsoft para obter detalhes adicionais sobre a capacidade de dedicado de Hubs de eventos. Também pode saber mais sobre os Hubs de eventos escalões de preço, visitando os links a seguir:

- [Preços de dedicado de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/). Também pode contactar o seu representante de vendas da Microsoft ou Support da Microsoft para obter detalhes adicionais sobre a capacidade de Hubs de eventos dedicados.
- O [FAQ dos Hubs de eventos](event-hubs-faq.md) contém informações sobre preços e responde a algumas perguntas frequentes sobre os Hubs de eventos.
