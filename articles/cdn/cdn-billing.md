---
title: Compreendendo a cobrança da CDN do Azure | Microsoft Docs
description: Estas perguntas frequentes descrevem como funciona a cobrança da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: magattus
ms.openlocfilehash: 8704d715a20b94dc170f232b07a0acd54bb1e6f1
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996814"
---
# <a name="understanding-azure-cdn-billing"></a>Compreender a faturação da CDN do Azure

Estas perguntas frequentes descrevem a estrutura de cobrança para o conteúdo hospedado pela CDN (rede de distribuição de conteúdo) do Azure.

## <a name="what-is-a-billing-region"></a>O que é uma região de cobrança?
Uma região de cobrança é uma área geográfica usada para determinar qual taxa é cobrada para a entrega de objetos da CDN do Azure. As zonas de cobrança atuais e suas regiões são as seguintes:

- Zona 1: América do Norte, Europa, Oriente Médio e África

- Zona 2: Ásia-Pacífico (incluindo o Japão)

- Zona 3: América do Sul

- Zona 4: Austrália e Nova Zelândia

- Zona 5: Índia

Para obter informações sobre regiões de ponto de presença (POP), consulte [locais pop de CDN do Azure por região](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Por exemplo, um POP localizado no México está na região de América do Norte e, portanto, é incluído na zona 1. 

Para obter informações sobre os preços da CDN do Azure, consulte [preços de rede de distribuição de conteúdo](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Como as cobranças de entrega são calculadas por região?
A região de cobrança da CDN do Azure baseia-se no local do servidor de origem que fornece o conteúdo para o usuário final. O destino (local físico) do cliente não é considerado a região de cobrança.

Por exemplo, se um usuário localizado no México emitir uma solicitação e essa solicitação for atendida por um servidor localizado em um Estados Unidos POP devido a condições de tráfego ou de emparelhamento, a região de cobrança será a Estados Unidos.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>O que é uma transação da CDN do Azure Faturável?
Qualquer solicitação HTTP (S) que termina na CDN é um evento Faturável, que inclui todos os tipos de resposta: êxito, falha ou outro. No entanto, respostas diferentes podem gerar diferentes quantidades de tráfego. Por exemplo, *304 não modificado* e outras respostas somente de cabeçalho geram pouco tráfego porque elas são uma pequena resposta de cabeçalho; da mesma forma, as respostas de erro (por exemplo, *404 não encontradas*) são faturáveis, mas incorrem em um pequeno custo devido à pequena carga de resposta.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Quais outros custos do Azure estão associados ao uso da CDN do Azure?
Usar a CDN do Azure também incorre em alguns encargos de uso sobre os serviços usados como a origem de seus objetos. Normalmente, esses custos são uma pequena fração do custo geral do uso da CDN.

Se você estiver usando o armazenamento de BLOBs do Azure como a origem do seu conteúdo, você também incorrerá nos seguintes encargos de armazenamento para os preenchimentos de cache:

- GB reais usados: O armazenamento real de seus objetos de origem.

- Transações Conforme necessário para preencher o cache.

- Transferências em GB: A quantidade de dados transferidos para preencher os caches da CDN.

> [!NOTE]
> A partir de outubro de 2019, se você estiver usando a CDN do Azure da Microsoft, o custo da transferência de dados de origens hospedadas no Azure para PoPs da CDN será gratuito. A CDN do Azure da Verizon e a CDN do Azure da Akamai estão sujeitas às tarifas descritas abaixo.

Para obter mais informações sobre a cobrança de armazenamento do Azure, consulte [noções básicas sobre cobrança de armazenamento do Azure – largura de banda, transações e capacidade](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Se estiver usando a *entrega de serviço hospedado*, você incorrerá em encargos da seguinte maneira:

- Tempo de computação do Azure: As instâncias de computação que atuam como a origem.

- Transferência de computação do Azure: Os dados são transferidos das instâncias de computação para preencher os caches da CDN do Azure.

Se o cliente usar solicitações de intervalo de bytes (independentemente do serviço de origem), as seguintes considerações se aplicarão:

- Uma *solicitação de intervalo de bytes* é uma transação FATURÁVEL na CDN. Quando um cliente emite uma solicitação de intervalo de bytes, essa solicitação é para um subconjunto (intervalo) do objeto. A CDN responde com apenas uma parte parcial do conteúdo solicitado. Essa resposta parcial é uma transação Faturável e o valor da transferência é limitado ao tamanho da resposta do intervalo (mais cabeçalhos).

- Quando uma solicitação chega apenas a uma parte de um objeto (especificando um cabeçalho de intervalo de bytes), a CDN pode buscar todo o objeto em seu cache. Como resultado, embora a transação Faturável da CDN seja para uma resposta parcial, a transação Faturável da origem pode envolver o tamanho total do objeto.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Quanta atividade de transferência ocorre para dar suporte ao cache?
Cada vez que um POP CDN precisa preencher seu cache, ele faz uma solicitação para a origem do objeto que está sendo armazenado em cache. Como resultado, a origem incorre em uma transação Faturável em cada erro de cache. O número de erros de cache depende de vários fatores:

- Como armazenar em cache o conteúdo é: Se o conteúdo tiver altos valores de/Expiration TTL (vida útil) e for acessado com frequência para que ele permaneça popular no cache, a grande maioria da carga será tratada pela CDN. Um bom índice de acertos de cache é muito mais de 90%, o que significa que menos de 10% das solicitações de cliente precisam retornar à origem, seja para um erro de cache ou atualização de objeto.

- Quantos nós precisam carregar o objeto: Cada vez que um nó carrega um objeto da origem, ele incorre em uma transação faturável. Como resultado, mais conteúdo global (acessado de mais nós) resulta em mais transações faturáveis.

- Influência de TTL: Um TTL mais alto para um objeto significa que ele precisa ser buscado a partir da origem com menos frequência. Isso também significa que os clientes, como navegadores, podem armazenar em cache o objeto mais longo, o que pode reduzir as transações para a CDN.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Quais serviços de origem estão qualificados para a transferência de dados gratuita com a CDN do Azure da Microsoft? 
Se você usar um dos seguintes serviços do Azure como sua origem de CDN, não será cobrado da transferência de dados da origem para os PoPs da CDN. 

- Storage do Azure
- Serviços de Multimédia do Azure
- Máquinas Virtuais do Azure
- Rede virtual
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
- Serviço de Azure App
- Funções do Azure
- Azure Data Factory
- Gestão de API do Azure
- Azure Batch 
- Azure Data Explorer
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Serviço Azure Machine Learning 
- Base de dados SQL do Azure
- Cache do Azure para Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Como fazer gerenciar meus custos com mais eficiência?
Defina o TTL mais longo possível em seu conteúdo. 
