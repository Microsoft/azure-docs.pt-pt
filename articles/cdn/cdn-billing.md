---
title: Compreensão da faturação do Azure CDN ! Microsoft Docs
description: Conheça a estrutura de faturação dos conteúdos hospedados pela Azure Content Delivery Network, incluindo regiões de faturação, taxas de entrega e gestão de custos.
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
ms.openlocfilehash: aa2f00a732a3978524fc017481285859c9535387
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018686"
---
# <a name="understanding-azure-cdn-billing"></a>Compreender a faturação da CDN do Azure

Esta FAQ descreve a estrutura de faturação para os conteúdos hospedados pela Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>O que é uma região de faturação?
Uma região de faturação é uma área geográfica usada para determinar qual a taxa cobrada para a entrega de objetos da Azure CDN. As atuais zonas de faturação e as suas regiões são as seguintes:

- Zona 1: América do Norte, Europa, Médio Oriente e África

- Zona 2: Ásia-Pacífico (incluindo o Japão)

- Zona 3: América do Sul

- Zona 4: Austrália e Nova Zelândia

- Zona 5: Índia

Para obter informações sobre as regiões do ponto de presença (POP), consulte [as localizações pop do Azure CDN por região.](./cdn-pop-locations.md) Por exemplo, um POP localizado no México está na região da América do Norte e, portanto, está incluído na zona 1. 

Para obter informações sobre os preços da Azure CDN, consulte [os preços da Rede de Entrega de Conteúdos](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Como são calculadas as taxas de entrega por região?
A região de faturação Azure CDN baseia-se na localização do servidor de origem que entrega o conteúdo ao utilizador final. O destino (localização física) do cliente não é considerado a região de faturação.

Por exemplo, se um utilizador localizado no México emitir um pedido e este pedido for reparado por um servidor localizado num POP dos Estados Unidos devido a condições de observação ou tráfego, a região de faturação será os Estados Unidos.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>O que é uma transação Azure CDN faturada?
Qualquer pedido HTTP(S) que encerre no CDN é um evento faturante, que inclui todos os tipos de resposta: sucesso, falha ou outro. No entanto, diferentes respostas podem gerar diferentes quantidades de tráfego. Por exemplo, *304 respostas não modificadas* e outras respostas apenas para cabeçalho geram pouco tráfego porque são uma resposta de cabeçalho pequeno; da mesma forma, as respostas de erro (por exemplo, *404 Não Encontradas*) são faturantes, mas incorrem num pequeno custo devido à carga útil de resposta minúscula.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Que outros custos da Azure estão associados à utilização do Azure CDN?
A utilização do Azure CDN também incorre em algumas taxas de utilização nos serviços utilizados como origem para os seus objetos. Estes custos são tipicamente uma pequena fração do custo total de utilização do CDN.

Se estiver a utilizar o armazenamento Azure Blob como origem para o seu conteúdo, também incorre nos seguintes custos de armazenamento para enchimentos de cache:

- GB real utilizado: O armazenamento real dos seus objetos de origem.

- Transações: Conforme necessário para preencher a cache.

- Transferências em GB: A quantidade de dados transferidos para preencher as caches CDN.

> [!NOTE]
> A partir de outubro de 2019, se estiver a utilizar o Azure CDN da Microsoft, o custo da transferência de dados do Origins alojado em Azure para o CDN PoPs é gratuito. Azure CDN de Verizon e Azure CDN da Akamai estão sujeitos às taxas descritas abaixo.

Para obter mais informações sobre a faturação do Armazenamento Azure, consulte [a Understanding Azure Storage Billing – Bandwidth, Transactions e Capacity](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Se estiver a utilizar *a prestação de serviços hospedado,* incorrerá nas despesas da seguinte forma:

- Tempo de computação Azure: As instâncias de computação que atuam como a origem.

- Transferência de cálculo Azure: Os dados transferem-se das instâncias de computação para preencher as caches Azure CDN.

Se o seu cliente utilizar pedidos de alcance byte (independentemente do serviço de origem), aplicam-se as seguintes considerações:

- Um *pedido de byte-range* é uma transação faturada na CDN. Quando um cliente emite um pedido de byte-range, este pedido é para um subconjunto (intervalo) do objeto. O CDN responde apenas com uma parte parcial do conteúdo solicitado. Esta resposta parcial é uma transação faturada e o valor de transferência é limitado ao tamanho da resposta da gama (mais cabeçalhos).

- Quando um pedido chega apenas para uma parte de um objeto (especificando um cabeçalho de alcance byte), o CDN pode levar todo o objeto para a sua cache. Como resultado, mesmo que a transação faturada do CDN seja para uma resposta parcial, a transação faturada a partir da origem pode envolver o tamanho total do objeto.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Quanta atividade de transferência ocorre para suportar a cache?
Cada vez que um CDN POP precisa de preencher a sua cache, faz um pedido à origem do objeto que está a ser cache. Como resultado, a origem incorre numa transação faturada em cada falha de cache. O número de falhas de cache depende de uma série de fatores:

- Quão cacheable é o conteúdo: Se o conteúdo tiver valores TTL elevados (tempo de vida)/expiração e for acedido frequentemente para que se mantenha popular em cache, então a grande maioria da carga é manuseada pelo CDN. Um rácio típico de bom cache-hit é bem superior a 90%, o que significa que menos de 10% dos pedidos do cliente têm que voltar à origem, seja para uma falha de cache ou atualização de objeto.

- Quantos nós precisam para carregar o objeto: Cada vez que um nó carrega um objeto da origem, incorre numa transação faturada. Como resultado, mais conteúdo global (acedido a partir de mais nós) resulta em transações mais faturadas.

- Influência TTL: Um TTL mais alto para um objeto significa que precisa de ser recolhido da origem com menos frequência. Também significa que os clientes, como os navegadores, podem cache o objeto por mais tempo, o que pode reduzir as transações para o CDN.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Que serviços de origem são elegíveis para transferência gratuita de dados com a Azure CDN da Microsoft? 
Se utilizar um dos seguintes serviços Azure como origem CDN, não será cobrado da transferência de Dados da Origem para os PoPs do CDN. 

- Storage do Azure
- Serviços de Multimédia do Azure
- Máquinas Virtuais do Microsoft Azure
- Rede Virtual
- Balanceador de Carga
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
- Gestão de API do Azure
- Azure Batch 
- Azure Data Explorer
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Base de Dados SQL do Azure
- Instância Gerida do Azure SQL
- Cache do Azure para Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Como posso gerir os meus custos de forma mais eficaz?
Desaver o TTL mais longo possível no seu conteúdo.