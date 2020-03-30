---
title: Rede global da Microsoft - Azure
description: Descreve como a Microsoft constrói a sua rede global rápida e fiável
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
ms.openlocfilehash: 10a061163447a60f1c25b386ef28028436284650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453061"
---
# <a name="microsoft-global-network"></a>Rede global da Microsoft

A Microsoft detém e opera uma das maiores redes de espinha dorsal do mundo. Esta arquitetura global e sofisticada, com mais de 160.000 km, liga os nossos datacenters e clientes. 
 
Todos os dias, os clientes de todo o mundo conectam e passam triliões de pedidos para o Microsoft Azure, Bing, Dynamics 365, Office 365, XBox, entre muitos outros. Independentemente do tipo, os clientes esperam fiabilidade instantânea e capacidade de resposta dos nossos serviços. 
 
A [rede global](https://azure.microsoft.com/global-infrastructure/global-network/) da Microsoft (WAN) é uma parte central da entrega de uma grande experiência na nuvem. Conectando os nossos centros de [dados](https://azure.microsoft.com/global-infrastructure/) da Microsoft em 54 regiões Azure e uma grande malha de pontas de borda estrategicamente colocadas em todo o mundo, a nossa rede global oferece tanto a disponibilidade, capacidade e flexibilidade para satisfazer qualquer procura.

![Rede global da Microsoft](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Obtenha a rede de nuvem premium
 
Optar pela [melhor experiência possível](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) é fácil quando se utiliza a nuvem da Microsoft. A partir do momento em que o tráfego de clientes entra na nossa rede global através dos nossos nódeos de borda estrategicamente posicionados, os seus dados viajam através de rotas otimizadas a uma velocidade de luz. Isto garante uma latência ótima para um melhor desempenho. Estes nódeos de borda, todos interligados a mais de 3500 parceiros exclusivos da Internet (pares) através de milhares de conexões em mais de 145 locais, fornecem a base da nossa estratégia de interconexão. 
 
Quer se ligue de Londres a Tóquio, ou de Washington DC a Los Angeles, o desempenho da rede é quantificado e impactado por coisas como latência, nervosismo, perda de pacotes e entrada.  Na Microsoft, preferimos e utilizamos interligações diretas em oposição às ligações de trânsito, isto mantém a resposta simétrica do tráfego e ajuda a manter o lúpulo, as festas e os caminhos o mais curtos e simples possível. 

Por exemplo, se um utilizador em Londres tenta aceder a um serviço em Tóquio, então o tráfego da Internet entra numa das nossas bordas em Londres, passa pela Microsoft WAN através da França, dos nossos caminhos trans-arabia entre a Europa e a Índia, e depois para o Japão onde o serviço está hospedado. O tráfego de resposta é simétrico. Isto é por vezes referido como [encaminhamento de batata fria,](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) o que significa que o tráfego permanece na rede da Microsoft o máximo possível antes de o entregarmos.  
  
Então, isso significa algum tráfego quando se usa os serviços da Microsoft? Sim, qualquer tráfego entre centros de dados, dentro do Microsoft Azure ou entre serviços da Microsoft como Máquinas Virtuais, Office 365, XBox, DBs SQL, Armazenamento e redes virtuais são encaminhados dentro da nossa rede global e nunca através da Internet pública, para garantir o ideal desempenho e integridade.  
 
Os investimentos maciços em fibra e diversidade em caminhos de metro, terrestres e submarinos são cruciais para mantermos consistentes e de alto nível de serviço, ao mesmo tempo que alimentamos o crescimento extremo da nossa nuvem e serviços online. As recentes adições à nossa rede global são o nosso cabo submarino [MAREA,](https://www.submarinecablemap.com/#/submarine-cable/marea) o primeiro Open Line System (OLS) da indústria sobre o submar, entre Bilbau, Espanha e Virginia Beach, Virginia, EUA, bem como a [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) entre Nova Iorque, EUA e Dublin, Irlanda e [New Cross Pacific (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) entre Tóquio, Japão e Portland, Oregon, EUA. 
 

## <a name="our-network-is-your-network"></a>A nossa rede é a sua rede

Colocamos duas décadas de experiência, juntamente com investimentos maciços na rede, para garantir um desempenho ideal em todos os momentos. As empresas podem tirar o máximo partido dos nossos ativos de rede e construir arquiteturas de sobreposição avançadas no topo. 
 
O Microsoft Azure oferece o portfólio mais rico de serviços e capacidades, permitindo aos clientes construir, expandir e satisfazer os requisitos de networking em qualquer lugar. A nossa família de serviços de conectividade abrange a rede virtual de observação entre regiões, arquiteturas híbridas e em nuvem ponto-a-local e local-a-site, bem como cenários globais de trânsito IP.  Para as empresas que procuram ligar o seu próprio datacenter ou rede ao Azure, ou clientes com necessidades massivas de ingestão de dados ou de trânsito, o [ExpressRoute](../expressroute/expressroute-introduction.md)e o [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) fornecem opções até 100 Gbps de largura de banda, diretamente na rede global da Microsoft em locais de observação em todo o mundo.  
 
O [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) foi concebido para complementar a implementação wan do seu fornecedor de serviços e ligar os seus sites no local em todo o mundo. Se executar uma operação global, pode utilizar o ExpressRoute Global Reach em conjunto com os seus fornecedores de serviços preferidos e locais para ligar todos os seus sites globais usando a rede global da Microsoft. Expandir a sua nova rede na nuvem (WAN) para abranger um grande número de sites de ramificação pode ser realizado através do Azure Virtual WAN, o que traz a capacidade de ligar perfeitamente as suas agências à rede global da Microsoft com dispositivos VPN & SDWAN (isto é, Equipamento de Instalações de Clientes ou CPE) com facilidade de utilização incorporada e gestão automatizada de conectividade e configuração. 
 
[O peering Global VNet](../virtual-network/virtual-network-peering-overview.md) permite que os clientes conectem duas ou mais redes virtuais Azure em regiões sem problemas. Uma vez espreitadas, as redes virtuais aparecem como uma só. O tráfego entre máquinas virtuais nas redes virtuais espreitadas é encaminhado através da infraestrutura de espinha dorsal da Microsoft, tal como o tráfego é encaminhado entre máquinas virtuais na mesma rede virtual - apenas através de endereços IP privados. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Bem gerido usando inovação definida por software

Com uma das principais nuvens do mundo, a Microsoft ganhou muita sonção e experiência na construção e gestão de infraestruturas globais de alto desempenho.  
 
Aderimos a um conjunto robusto de princípios operacionais: 
 
- Utilize hardware de comutação de melhor raça nos vários níveis da rede.  
- Implementar novas funcionalidades sem impacto zero para os utilizadores finais.  
- Lançar atualizações de forma segura e fiável em toda a frota, o mais rápido possível. Horas em vez de semanas.  
- Utilize a telemetria profunda em escala em nuvem e a mitigação de falhas totalmente automatizada.  
- Utilize tecnologia de networking unificada e definida por software para controlar todos os elementos de hardware da rede.  Eliminar a duplicação e reduzir falhas. 
 
Estes princípios aplicam-se a todas as camadas da rede: desde a Interface de Rede de Recolhimento, plataforma de comutação, funções de rede no centro de dados como os Balancers de Carga, até ao WAN com a nossa plataforma de engenharia de tráfego e as nossas redes óticas.  
 
O crescimento exponencial do Azure e da sua rede chegou a um ponto em que eventualmente percebemos que a intuição humana já não podia contar com a gestão das operações globais da rede. Para satisfazer a necessidade de validar alterações de longo, médio e curto prazo na rede, desenvolvemos uma plataforma para espelhar e imitar a nossa rede de produção sinteticamente. A capacidade de criar ambientes espelhados e executar milhões de simulações, permite-nos testar alterações de software e hardware e o seu impacto, antes de os comprometer na nossa plataforma de produção e rede. 

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais sobre os serviços de networking prestados no Azure](https://azure.microsoft.com/product-categories/networking/)
