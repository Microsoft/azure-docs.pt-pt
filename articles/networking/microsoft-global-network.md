---
title: Rede global Microsoft - Azure
description: Descreve como Microsoft baseia-se a rede global de rápida e fiável
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: networking
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2019
ms.author: ypitsch,kumud
ms.openlocfilehash: 99650577b17874e61d1cede979d7ef2f4bfd491b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247293"
---
# <a name="microsoft-global-network"></a>Rede global da Microsoft

A Microsoft é o proprietário e opera uma das maiores redes backbone do mundo. Esta arquitetura global e sofisticada, que abrangem mais de 100 000 milhas, liga-se nossos centros de dados e clientes. 
 
Todos os dias, os clientes em todo o mundo se ligar e passam triliões de pedidos para o Microsoft Azure, Bing, do Dynamics 365, Office 365, XBox e muitos outros. Independentemente do tipo, os clientes esperar instantânea confiabilidade e capacidade de resposta de nossos serviços. 
 
O [rede global Microsoft](https://azure.microsoft.com/global-infrastructure/global-network/) (WAN) é uma parte central de proporcionar uma experiência de cloud excelente. Ligar o nosso Microsoft [centros de dados](https://azure.microsoft.com/global-infrastructure/) em 54 regiões do Azure e grande malha de nós de extremidade estrategicamente colocados em todo o mundo, a nossa rede global oferece tanto a disponibilidade, capacidade e a flexibilidade para satisfazer qualquer tipo de procura.

![Rede global da Microsoft](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Obtenha a rede de nuvem premium
 
Aceitar para o [melhor experiência possível](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) é fácil quando utilizam o Microsoft cloud. Desde o momento quando o tráfego de cliente entra nossa rede global por meio de nosso estrategicamente colocadas de nós de extremidade, os seus dados viaja através de rotas otimizadas em quase a velocidade da luz. Isto garante a latência ideal para um melhor desempenho. Estes edge-nós, todos interligado à mais de 3500 exclusivos Internet parceiros (elementos) por meio de milhares de conexões em mais do que 145 locais, fornecem a base da nossa estratégia de interligação. 
 
Se ligar de Londres para Tóquio ou de Washington, D.C. de Los Angeles, o desempenho da rede é quantificado e afetado por coisas como, tremulação, perda de pacotes, débito e latência.  Na Microsoft, preferimos e uso direto interconexões em vez de ligações de trânsito, isso mantém o tráfego de resposta simétrica e ajuda a manter os saltos, peering partes e caminhos mais curto e simples possível. Esta abordagem de premium, normalmente conhecida como [frio potato encaminhamento](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing), garante que os clientes de rede tráfego permanece na rede da Microsoft quanto possível antes vamos passá-lo.  
 
Então, que significa todo e qualquer tráfego ao utilizar os serviços da Microsoft? Sim, qualquer tráfego entre dados centra-se, no Microsoft Azure ou entre serviços da Microsoft, como máquinas virtuais, do Office 365, XBox, bds SQL, armazenamento, e redes virtuais são encaminhadas dentro de nossa rede global e nunca pela Internet pública, para garantir ideal desempenho e a integridade.  
 
Grandes investimentos em capacidade de fibra e diversidade nos caminhos de terrestrial e submarino metro, são cruciais para nós manter consistente e de alto nível de serviço ao mesmo tempo por detrás do crescimento extremo da nossa cloud e serviços online. São acréscimos recentes para nossa rede global nosso [MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea) submarino cabo, primeiro abrir linha de sistema (OLS do setor) ao longo do subsea, entre o museu, Espanha e Virgínia praia, Virginia, EUA, bem como o [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1)entre Nova Iorque, EUA e Dublin, Irlanda e [novos cruzada do Pacífico (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) entre Tóquio, Japão e Portland, Oregon, EUA. 
 

## <a name="our-network-is-your-network"></a>Nossa rede é a sua rede

Nós as pusemos duas décadas de experiência, juntamente com grandes investimentos para a rede garantir um desempenho ideal em todos os tempos. As empresas podem tirar partido dos nossos ativos de rede e crie arquiteturas de sobreposição avançadas na parte superior. 
 
O Microsoft Azure oferece o mais diversificado portfólio de serviços e capacidades, possibilitando aos clientes criar, rápida e facilmente, expandam e cumprem os requisitos de rede em qualquer lugar. Nossa família de serviços de conectividade abranger o peering de rede virtual entre regiões, híbridos e na nuvem ponto a site e cenários de trânsito de arquiteturas de site a site, bem como global IP.  Para as empresas que pretendem para ligar o seu próprio datacenter ou a rede no Azure ou os clientes com a ingestão de dados em massa ou necessidades de trânsito, [ExpressRoute](../expressroute/expressroute-introduction.md), e [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) fornecem opções até 100 Gbps de largura de banda, diretamente para a rede global da Microsoft em localizações de peering em todo o mundo.  
 
[Alcance Global do ExpressRoute](../expressroute/expressroute-global-reach.md) foi concebido para complementar a implementação de WAN do seu fornecedor de serviços e ligar os seus sites no local em todo o mundo. Se executar uma operação global, pode utilizar alcance Global do ExpressRoute em conjunto com seus provedores de serviço preferido e local para ligar todos os seus sites global utilizando a rede global da Microsoft. Expandir a sua nova rede na cloud (WAN) para abranger um grande número de locais de ramal pode ser conseguida através do Azure WAN Virtual, que concede a capacidade se conectem seus ramos a rede global da Microsoft com dispositivos SDWAN & VPN (ou seja, Equipamento do cliente no local ou CPE) com incorporada facilidade de uso e gerenciamento automatizado de conectividade e a configuração. 
 
[Global VNet peering](../virtual-network/virtual-network-peering-overview.md) permite aos clientes ligar duas ou mais redes virtuais do Azure entre regiões de forma totalmente integrada. Uma vez executado o peering, as redes virtuais aparecem como uma única. O tráfego entre máquinas virtuais nas redes virtuais em modo de peering será encaminhado através da infraestrutura principal da Microsoft, tal como o tráfego é encaminhado entre máquinas virtuais na mesma rede virtual - através de endereços IP privados apenas. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Bem geridos através de inovação definida pelo software

Executar uma das nuvens líderes do setor no mundo, Microsoft ganhou muita informação e experiência no [criar e gerir](https://myignite.techcommunity.microsoft.com/sessions/66668) infraestrutura global de alto desempenho.  
 
Respeitamos a um conjunto robusto de princípios operacionais: 
 
- Utilize o hardware de comutação de melhor em várias camadas da rede.  
- Implemente novos recursos com impacto zero aos utilizadores finais.  
- Distribuir atualizações de forma segura e fiável toda a frota, mais rápido possível. Horas em vez de semanas.  
- Utilizar uma telemetria profunda de escala da cloud e totalmente automatizado atenuação de falhas.  
- Utilize tecnologia de sistema de rede unificada e definida pelo software para controlar todos os elementos de hardware na rede.  A eliminação de duplicação e reduzir falhas. 
 
Esses princípios se aplicam a todas as camadas da rede: do anfitrião de Interface de rede, mudar de plataforma, as funções no Centro de dados, como balanceadores de carga, até a WAN com a nossa plataforma de engenharia de tráfego e o nosso ópticas redes de rede.  
 
O crescimento exponencial do Azure e a sua rede tenha atingido um ponto em que, eventualmente, percebemos que intuição humana poderia já não ser confiável para gerir as operações de rede global. Para satisfazer a necessidade de validar há muito tempo, médio e alterações a curto prazo na rede, desenvolvemos uma plataforma para espelhar e emular synthetically a nossa rede de produção. A capacidade de criar ambientes espelhados e executar milhões de simulações, permite-nos testar o software e as alterações de hardware e seu impacto, antes de consolidá-los para a nossa plataforma de produção e de rede. 

## <a name="next-steps"></a>Passos Seguintes
- [Saiba mais sobre os serviços de rede fornecidos no Azure](https://azure.microsoft.com/product-categories/networking/)
