---
title: Compreender a faturação da CDN do Azure | Documentos da Microsoft
description: Nestas perguntas frequentes descreve como funciona a faturação da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: magattus
ms.openlocfilehash: af8e57f39b5b83b1d1be09c29d8b6eb5d49c7b6c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735805"
---
# <a name="understanding-azure-cdn-billing"></a>Compreender a faturação da CDN do Azure

Nestas perguntas frequentes descreve a estrutura de faturação para o conteúdo alojado pela rede de entrega de conteúdos do Azure (CDN).

## <a name="what-is-a-billing-region"></a>O que é uma região de faturação?
Uma região de faturação é uma área geográfica usada para determinar a que taxa é cobrada para a entrega de objetos da CDN do Azure. As zonas de faturas atuais e as respetivas regiões são os seguintes:

- Zona 1: América do Norte, Europa, Médio Oriente e África

- Zona 2: Ásia-Pacífico (incluindo Japão)

- Zona 3: América do Sul

- Zona 4: Austrália e Nova Zelândia

- Zona 5: Índia

Para obter informações sobre as regiões de pontos de presença (POP), consulte [localizações POP do CDN do Azure por região](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Por exemplo, um POP localizado no México está na região da América do Norte e, portanto, está incluído na zona 1. 

Para obter informações sobre os preços da CDN do Azure, consulte [preços de rede de entrega de conteúdos](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Como são calculados os custos de entrega por região?
A região de faturação da CDN do Azure baseia-se na localização do servidor de origem entregar o conteúdo para o utilizador final. O destino (localização física) do cliente não é considerado a região de faturação.

Por exemplo, se um utilizador localizado no México emite um pedido e este pedido é servido por um servidor localizado num preenchimento dos Estados Unidos devido a condições de peering ou de tráfego, a região de faturação será dos Estados Unidos.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>O que é uma transação de CDN do Azure faturável?
Qualquer pedido de HTTP (S) que termina em CDN é um evento cobrado, o que inclui todos os tipos de resposta: êxito, falha ou outro. No entanto, as respostas diferentes podem gerar quantidades de tráfego diferentes. Por exemplo, *304 não modificado* e outras respostas somente de cabeçalho geram pouco tráfego, por estarem um pequeno resposta de cabeçalho; da mesma forma, as respostas de erro (por exemplo, *404 não encontrado*) são a cobrar mas implicar um custo de pequenas devido a payload de resposta muito pequeno.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Quais outros custos do Azure estão associados a utilização da CDN do Azure?
Também é a utilização de CDN do Azure implica alguns custos de utilização sobre os serviços utilizados como origem para seus objetos. Esses custos são, normalmente, uma pequena fração do custo de utilização geral do CDN.

Se estiver a utilizar o armazenamento de Blobs do Azure como a origem para o seu conteúdo, também de ter os seguintes custos de armazenamento para preenchimentos de cache:

- GB real utilizados: O armazenamento real dos seus objetos de origem.

- Transferências em GB: A quantidade de dados transferidos para preencher os caches CDN.

- Transações: Conforme necessário para preencher a cache.

Para obter mais informações sobre a faturação do armazenamento do Azure, consulte [Noções básicas sobre faturação do Azure Storage – largura de banda, transações e capacidade](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Se estiver a utilizar *alojada prestação de serviços*, incorre em custos da seguinte forma:

- Hora de computação do Azure: As instâncias de computação que atuam como a origem.

- Transferência de computação do Azure: Os dados são transferidos das instâncias de computação para preencher os caches de CDN do Azure.

Se o cliente utiliza os pedidos de intervalo de bytes (independentemente do serviço de origem), aplicam-se as seguintes considerações:

- R *solicitação de intervalo de bytes* é uma transação cobrar na CDN. Quando um cliente emite uma solicitação de intervalo de bytes, essa solicitação é para um subconjunto (intervalo) do objeto. A CDN responde com apenas uma parte parcial do conteúdo que é solicitada. Esta resposta parcial é uma transação cobrar e a quantidade de transferência é limitada para o tamanho da resposta de intervalo (mais cabeçalhos).

- Quando chega uma solicitação para apenas uma parte de um objeto (ao especificar um cabeçalho de intervalo de bytes), a CDN pode obter todo o objeto para a sua cache. Como resultado, mesmo que a transação sujeito a faturação da CDN é uma resposta parcial, a transação cobrar da origem pode envolver o tamanho máximo do objeto.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>A atividade de transferência ocorre para suportar o cache?
Sempre que precisar de um POP da CDN para preencher a respetiva cache, ele faz um pedido para a origem para o objeto a ser colocados em cache. Como resultado, a origem incorre numa transação faturável em cada falha de acerto na cache. O número de falhas de acerto na cache depende de vários fatores:

- Como em cache o conteúdo é: Se o conteúdo tem alta TTL (time-to-live) / valores de expiração e é acedidos com frequência para que fique populares no cache, em seguida, a grande maioria da carga é manipulado pela CDN. Uma taxa de acertos na cache boa típica é bem que mais de 90%, que significa que menos de 10% de pedidos de cliente tem de voltar à origem, para uma falha de acerto na cache ou o objeto a atualizar.

- Quantos nós tem de carregar o objeto: Sempre que um nó carrega um objeto de origem, ela gera uma transação faturável. Como resultado, o conteúdo mais global (acedido a partir de mais nós) resulta em transações mais faturáveis.

- Influência TTL: Um valor de TTL superior, para um objeto significa que ele precisa ser obtidos a partir da origem com menos frequência. Isso também significa que os clientes, tais como navegadores, podem colocar em cache o objeto mais tempo, o que pode reduzir as transações para a CDN.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Como posso gerir os meus custos com mais eficiência?
Defina o valor de TTL mais longo possível no seu conteúdo. 
