---
title: Rede global da Microsoft - Azure
description: Saiba como a Microsoft constrói e opera uma das maiores redes de espinha dorsal do mundo, e por que é fundamental para oferecer uma grande experiência em nuvem.
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2019
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: 05e966d3f4631e3c344bd97c0faec99cc3c6dd66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819902"
---
# <a name="microsoft-global-network"></a>Rede global da Microsoft

A Microsoft detém e opera uma das maiores redes de espinha dorsal do mundo. Esta arquitetura global e sofisticada, com mais de 165.000 milhas, liga os nossos datacenters e clientes. 
 
Todos os dias, os clientes de todo o mundo conectam e passam triliões de pedidos para a Microsoft Azure, Bing, Dynamics 365, Microsoft 365, XBox, entre muitos outros. Independentemente do tipo, os clientes esperam fiabilidade instantânea e capacidade de resposta dos nossos serviços. 
 
A [rede global da Microsoft](https://azure.microsoft.com/global-infrastructure/global-network/) (WAN) é uma parte central de proporcionar uma grande experiência em nuvem. Conectando os [nossos centros de dados](https://azure.microsoft.com/global-infrastructure/) da Microsoft em 61 regiões de Azure e uma grande malha de nós de borda estrategicamente posicionados em todo o mundo, a nossa rede global oferece tanto a disponibilidade, a capacidade como a flexibilidade para satisfazer qualquer procura.

![Rede global da Microsoft](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Obtenha a rede de nuvem premium
 
Optar pela [melhor experiência possível](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) é fácil quando se utiliza a nuvem microsoft. A partir do momento em que o tráfego de clientes entra na nossa rede global através dos nossos nós de borda estrategicamente posicionados, os seus dados viajam através de rotas otimizadas perto da velocidade da luz. Isto garante uma latência ótima para o melhor desempenho. Estes nós de borda, todos interligados a mais de 4000 parceiros únicos da Internet (pares) através de milhares de conexões em mais de 175 locais, fornecem a base da nossa estratégia de interconexão. 
 
Seja ligando de Londres a Tóquio, ou de Washington DC a Los Angeles, o desempenho da rede é quantificado e impactado por coisas como latência, nervosismo, perda de pacotes e produção.  Na Microsoft, preferimos e usamos interligações diretas em oposição às ligações de trânsito, isto mantém a resposta simétrica do tráfego e ajuda a manter o lúpulo, as partes e os caminhos o mais curtos e simples possível. 

Por exemplo, se um utilizador em Londres tentar aceder a um serviço em Tóquio, então o tráfego da Internet entra numa das nossas bordas em Londres, passa por cima da Microsoft WAN através da França, os nossos caminhos Trans-Arabia entre a Europa e a Índia, e depois para o Japão onde o serviço está hospedado. O tráfego de resposta é simétrico. Isto é por vezes referido como [encaminhamento de batatas frias,](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) o que significa que o tráfego permanece na rede da Microsoft o máximo possível antes de o entregarmos.  
  
Isso significa todo e qualquer tráfego quando se utiliza os serviços da Microsoft? Sim, qualquer tráfego entre centros de dados, dentro do Microsoft Azure ou entre serviços da Microsoft como Máquinas Virtuais, Microsoft 365, XBox, SQL DBs, Armazenamento e redes virtuais são encaminhados dentro da nossa rede global e nunca através da Internet pública, para garantir o melhor desempenho e integridade.  
 
Os investimentos maciços na capacidade de fibra e diversidade em todo o metro, terrestre e submarino são cruciais para mantermos consistentes e altos níveis de serviço, alimentando o crescimento extremo da nossa nuvem e serviços online. As adições recentes à nossa rede global são o nosso cabo submarino [MAREA,](https://www.submarinecablemap.com/#/submarine-cable/marea) o primeiro Sistema de Linha Aberta (OLS) da indústria sobre o mar, entre Bilbau, Espanha e Virginia Beach, Virginia, EUA, bem como o [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) entre Nova Iorque, EUA e Dublin, Irlanda e [New Cross Pacific (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) entre Tóquio, Japão e Portland, Oregon, EUA. 
 

## <a name="our-network-is-your-network"></a>A nossa rede é a sua rede

Colocamos duas décadas de experiência, juntamente com investimentos maciços na rede, para garantir o melhor desempenho em todos os momentos. As empresas podem tirar o máximo partido dos nossos ativos de rede e construir arquiteturas avançadas sobrepostas no topo. 
 
O Microsoft Azure oferece o portfólio mais rico de serviços e capacidades, permitindo que os clientes construam, expandam e cumpram os requisitos de networking em qualquer lugar. A nossa família de serviços de conectividade abrange redes virtuais que espreitam entre regiões, híbridas e arquiteturas de ponto a local e local-a-local, bem como cenários globais de trânsito IP.  Para as empresas que procuram ligar o seu próprio datacenter ou rede ao Azure, ou clientes com necessidades massivas de ingestão de dados ou de trânsito, [o ExpressRoute](../expressroute/expressroute-introduction.md)e o [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) oferecem opções até 100 Gbps de largura de banda, diretamente na rede global da Microsoft em locais de observação em todo o mundo.  
 
[O ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) foi concebido para complementar a implementação wan do seu fornecedor de serviços e ligar os seus sites no local em todo o mundo. Se executar uma operação global, pode utilizar o ExpressRoute Global Reach em conjunto com os seus fornecedores de serviços preferidos e locais para ligar todos os seus sites globais utilizando a rede global da Microsoft. Expandir a sua nova rede na nuvem (WAN) para abranger um grande número de sites de sucursais pode ser realizado através do Azure Virtual WAN, o que traz a capacidade de ligar perfeitamente as suas agências à rede global da Microsoft com dispositivos VPN SDWAN & (isto é, Equipamento de Instalações do Cliente ou CPE) com facilidade de utilização incorporada e gestão automatizada de conectividade e configuração. 
 
[O persimento global da VNet](../virtual-network/virtual-network-peering-overview.md) permite que os clientes conectem duas ou mais redes virtuais Azure em todas as regiões de forma perfeita. Uma vez espreitadas, as redes virtuais aparecem como uma só. O tráfego entre máquinas virtuais nas redes virtuais é encaminhado através da infraestrutura da Espinha Dorsal da Microsoft, tal como o tráfego é encaminhado entre máquinas virtuais na mesma rede virtual - apenas através de endereços IP privados. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Bem gerido usando inovação definida por software

Com uma das principais nuvens do mundo, a Microsoft ganhou muita visão e experiência na construção e gestão de infraestruturas globais de alto desempenho.  
 
Aderimos a um conjunto robusto de princípios operacionais: 
 
- Utilize hardware de comutação de melhor raça através dos vários níveis da rede.  
- Implemente novas funcionalidades com impacto zero para os utilizadores finais.  
- Desenrolar as atualizações de forma segura e fiável através da frota, o mais rápido possível. Horas em vez de semanas.  
- Utilize telemetria profunda em escala de nuvem e atenuação de falhas totalmente automatizada.  
- Utilize tecnologia de rede unificada e definida por software para controlar todos os elementos de hardware da rede.  Eliminando a duplicação e reduzindo falhas. 
 
Estes princípios aplicam-se a todas as camadas da rede: desde a Interface de Rede hospedeira, plataforma de comutação, funções de rede no centro de dados como os Load Balancers, até ao WAN com a nossa plataforma de engenharia de tráfego e as nossas redes óticas.  
 
O crescimento exponencial do Azure e da sua rede chegou a um ponto em que eventualmente percebemos que a intuição humana já não podia ser confiada para gerir as operações globais da rede. Para satisfazer a necessidade de validar alterações de longo, médio e curto prazo na rede, desenvolvemos uma plataforma para espelhar e imitar a nossa rede de produção sinteticamente. A capacidade de criar ambientes espelhados e executar milhões de simulações, permite-nos testar alterações de software e hardware e o seu impacto, antes de os comprometer com a nossa plataforma de produção e rede. 

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais sobre os serviços de networking prestados em Azure](https://azure.microsoft.com/product-categories/networking/)
