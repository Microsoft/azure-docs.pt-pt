---
title: Compreensão da faturação do CDN azure [ Microsoft Docs
description: Este FAQ descreve como funciona a faturação do Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: allensu
ms.openlocfilehash: d3a2dfba98f83d34c3e83ec865e3b692f7dbacd2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254228"
---
# <a name="understanding-azure-cdn-billing"></a>Compreender a faturação da CDN do Azure

Este FAQ descreve a estrutura de faturação para conteúdos hospedados pela Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>O que é uma região de faturação?
Uma região de faturação é uma área geográfica usada para determinar que taxa é cobrada para a entrega de objetos do Azure CDN. As atuais zonas de faturação e as suas regiões são as seguintes:

- Zona 1: América do Norte, Europa, Médio Oriente e África

- Zona 2: Ásia-Pacífico (incluindo japão)

- Zona 3: América do Sul

- Zona 4: Austrália e Nova Zelândia

- Zona 5: Índia

Para obter informações sobre as regiões do ponto de presença (POP), consulte as [localizações pop do Azure CDN POP por região](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Por exemplo, um POP localizado no México está na região da América do Norte e está, portanto, incluído na zona 1. 

Para obter informações sobre os preços do CDN Azure, consulte [os preços da Rede](https://azure.microsoft.com/pricing/details/cdn/)de Entrega de Conteúdos .

## <a name="how-are-delivery-charges-calculated-by-region"></a>Como são calculados os encargos de entrega por região?
A região de faturação do CDN Azure baseia-se na localização do servidor de origem que entrega o conteúdo ao utilizador final. O destino (localização física) do cliente não é considerado a região de faturação.

Por exemplo, se um utilizador localizado no México emitir um pedido e este pedido for servido por um servidor localizado num POP dos Estados Unidos devido a condições de observação ou de tráfego, a região de faturação será os Estados Unidos.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>O que é uma transação de CDN Azure faturada?
Qualquer pedido http(S) que termina no CDN é um evento faturado, que inclui todos os tipos de resposta: sucesso, falha ou outros. No entanto, respostas diferentes podem gerar diferentes quantidades de tráfego. Por exemplo, *304 respostas não modificadas* e outras respostas apenas para cabeçalho geram pouco tráfego porque são uma pequena resposta cabeçalho; da mesma forma, as respostas de erro (por exemplo, *404 Não Encontradas)* são faturadas, mas incorrem num pequeno custo devido à carga útil da resposta minúscula.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Que outros custos do Azure estão associados à utilização do Azure CDN?
A utilização do Azure CDN também incorre em algumas taxas de utilização nos serviços utilizados como origem para os seus objetos. Estes custos são tipicamente uma pequena fração do custo total de utilização do CDN.

Se estiver a utilizar o armazenamento Azure Blob como origem para o seu conteúdo, também incorre nas seguintes despesas de armazenamento para enchimentos de cache:

- Gb real utilizado: O armazenamento real dos seus objetos de origem.

- Transações: Conforme necessário para preencher a cache.

- Transferências em GB: A quantidade de dados transferidos para preencher os caches da CDN.

> [!NOTE]
> A partir de outubro de 2019, se estiver a utilizar o Azure CDN da Microsoft, o custo da transferência de dados das Origens hospedadas em Azure para CDN PoPs é gratuito. O Azure CDN da Verizon e o Azure CDN da Akamai estão sujeitos às taxas descritas abaixo.

Para obter mais informações sobre a faturação de armazenamento azure, consulte [Understanding Azure Storage Billing – Width, Transactions e Capacity](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Se estiver a utilizar a entrega de *serviço hospedada,* incorrerá em encargos da seguinte forma:

- Tempo de computação azure: Os casos computacionais que funcionam como a origem.

- Transferência de cálculo Azure: Os dados transferem-se das instâncias computadas para preencher os caches Azure CDN.

Se o seu cliente utilizar pedidos de gama byte (independentemente do serviço de origem), aplicam-se as seguintes considerações:

- Um *pedido de byte-range* é uma transação faturada na CDN. Quando um cliente emite um pedido de byte-range, este pedido é para um subconjunto (intervalo) do objeto. O CDN responde apenas com uma parte parcial do conteúdo solicitado. Esta resposta parcial é uma transação faturada e o valor da transferência está limitado ao tamanho da resposta de intervalo (mais cabeçalhos).

- Quando um pedido chega apenas para uma parte de um objeto (especificando um cabeçalho de alcance byte), o CDN pode levar todo o objeto para a sua cache. Como resultado, embora a transação faturada do CDN seja para uma resposta parcial, a transação faturada da origem pode envolver a dimensão total do objeto.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Quanta atividade de transferência ocorre para suportar a cache?
Cada vez que um CDN POP precisa preencher a sua cache, faz um pedido à origem para o objeto ser cache. Como resultado, a origem incorre numa transação faturada em cada cache miss. O número de caches falha depende de uma série de fatores:

- Quão cacheable o conteúdo é: Se o conteúdo tiver valores elevados de TTL (tempo de vida)/expiração e for acedido frequentemente para que permaneça popular em cache, então a grande maioria da carga é manuseada pelo CDN. Uma relação típica de bom cache-hit é bem superior a 90%, o que significa que menos de 10% dos pedidos de clientes têm de voltar à origem, seja para uma falha de cache ou para a atualização de objetos.

- Quantos nós precisam carregar o objeto: Cada vez que um nó carrega um objeto da origem, incorre numa transação faturada. Como resultado, um conteúdo mais global (acedido a partir de mais nós) resulta em transações mais faturadas.

- Influência TTL: Um TTL mais alto para um objeto significa que precisa de ser recolhido da origem com menos frequência. Também significa que os clientes, como os navegadores, podem cache o objeto por mais tempo, o que pode reduzir as transações para o CDN.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Quais os serviços de origem elegíveis para transferência gratuita de dados com o Azure CDN da Microsoft? 
Se utilizar um dos seguintes serviços Azure como origem CDN, não será cobrado a partir da transferência de Dados da Origem para os PoPs cDN. 

- Storage do Azure
- Serviços Azure Media
- Máquinas Virtuais do Azure
- Rede Virtual
- Load balancer
- Gateway de Aplicação
- DNS do Azure
- ExpressRoute
- Gateway de VPN
- Gestor de Tráfego
- Observador de Rede
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Serviço de Aplicações Azure
- Funções do Azure
- Azure Data Factory
- API Management do Azure
- Azure Batch 
- Azure Data Explorer
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Base de dados SQL do Azure
- Cache do Azure para Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Como posso gerir os meus custos de forma mais eficaz?
Detete o TTL mais longo possível no seu conteúdo. 
