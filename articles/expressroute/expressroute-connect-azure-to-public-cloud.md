---
title: Ligando Azure às nuvens públicas Microsoft Docs
description: Descreva várias formas de ligar O Azure a outras nuvens públicas
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: duau
ms.openlocfilehash: fb4c34345ab902e243f22eae06fd5ea174fd2992
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202196"
---
# <a name="connecting-azure-with-public-clouds"></a>Ligando Azure com nuvens públicas

Muitas empresas prosseguem uma estratégia multi-nuvem devido a objetivos empresariais e técnicos. Estes incluem custo, flexibilidade, disponibilidade de recursos, redundância, soberania de dados, etc. Esta estratégia ajuda-os a aproveitar melhor de ambas as nuvens. 

Esta abordagem também coloca desafios à empresa em termos de arquitetura de rede e aplicações. Alguns destes desafios são a latência e a produção de dados. Para enfrentar estes desafios, os clientes procuram ligar-se diretamente a várias nuvens. Alguns prestadores de serviços fornecem uma solução para ligar vários fornecedores de nuvem aos clientes. Noutros casos, o cliente pode implantar o seu próprio router para ligar várias nuvens públicas.
## <a name="connectivity-via-expressroute"></a>Conectividade via ExpressRoute
O ExpressRoute permite que os clientes estendam as suas redes no local para a nuvem da Microsoft sobre uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, os clientes podem estabelecer ligações aos serviços de cloud da Microsoft.

Há três maneiras de ligar via ExpressRoute.

1. Fornecedor layer3
2. Fornecedor layer2
3. Ligação direta

### <a name="layer3-provider"></a>Fornecedor Layer3

Os fornecedores de camadas3 são vulgarmente conhecidos como fornecedores IP VPN ou MPLS VPN. Os clientes aproveitam estes fornecedores para a conectividade multiponto entre os seus centros de dados, os seus ramos e a nuvem. Os clientes ligam-se ao fornecedor L3 via BGP ou via rota por defeito estático. O prestador de serviços anuncia rotas entre os sites de clientes, datacenters e nuvem pública. 
 
Ao ligar através do fornecedor Layer3, a Microsoft anunciará as rotas VNET do cliente ao fornecedor de serviços em vez do BGP. O fornecedor pode ter duas implementações diferentes.

![Diagrama que mostra um fornecedor de Camadas3.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

O fornecedor pode estar a aterrar cada fornecedor de nuvem num VRF separado, se o tráfego de todos os fornecedores de nuvem chegar ao router do cliente. Se o cliente estiver a executar o BGP com o fornecedor de serviços, então estas rotas serão re-publicitadas a outros fornecedores de nuvem por padrão. 

Se o prestador de serviços estiver a aterrar todos os fornecedores de nuvem no mesmo VRF, então as rotas serão publicitadas diretamente a outros fornecedores de nuvem do prestador de serviços. Isto assumindo a operação padrão de BGP onde as rotas eBGP são publicitadas a outros vizinhos eBGP por padrão.

Cada nuvem pública tem um limite de prefixo diferente, pelo que, ao distribuir o prestador de serviços de rotas, deve ter cuidado na distribuição das rotas.

### <a name="layer2-provider-and-direct-connection"></a>Camada2 Provedor e conexão direta

Embora a conectividade física em ambos os modelos seja diferente, mas na camada3 BGP é estabelecida diretamente entre o MSEE e o router do cliente. Para o cliente ExpressRoute Direct conecta-se diretamente à MSEE. Enquanto no caso da Layer2, o prestador de serviços estende o VLAN das instalações do cliente para a nuvem. Os clientes executam BGP em cima da rede de camadas2 para ligar os seus DCs à nuvem.
![Diagrama que mostra um fornecedor layer2 e ligação direta.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
Em ambos os casos, o cliente terá ligações ponto a ponto a cada uma das nuvens públicas. O cliente estabelecerá uma ligação BGP separada a cada nuvem pública. As rotas recebidas por um fornecedor de nuvem serão publicitadas a outro fornecedor de nuvem por padrão. Cada fornecedor de nuvem tem um limite de prefixo diferente, por isso, enquanto publicita as rotas o cliente deve cuidar destes limites. O cliente pode usar os botões BGP habituais com a Microsoft enquanto publicita rotas de outras nuvens públicas.

## <a name="direct-connection-with-expressroute"></a>Ligação direta com o ExpressRoute

Os clientes podem optar por ligar o ExpressRoute diretamente à oferta de conectividade direta do fornecedor de nuvem. Dois fornecedores de nuvem serão ligados de volta a trás e BGP será estabelecido diretamente entre os seus routers. Este tipo de conexão está disponível com a Oracle hoje.

## <a name="site-to-site-vpn"></a>VPN site a site

Os clientes podem aproveitar a Internet para ligar as suas instâncias em Azure a outras nuvens públicas. Quase todos os fornecedores de nuvem oferecem capacidades de VPN site-to-site. No entanto, pode haver incompatibilidades por falta de certas variantes. Por exemplo, alguns fornecedores de nuvem apenas suportam iKEv1, pelo que existe um ponto final de terminação VPN necessário nessa nuvem. Para os fornecedores de nuvem que suportam o IKEv2, pode ser estabelecido um túnel direto entre gateways VPN em ambos os fornecedores de nuvem.

A VPN local não é considerada uma solução de alta produção e baixa latência. No entanto, pode ser usado como uma cópia de segurança para a conectividade física.

## <a name="next-steps"></a>Passos seguintes
Consulte [o ExpressRoute FAQ][ER-FAQ] para mais perguntas sobre a conectividade ExpressRoute e rede virtual.

Ver [Configurar a ligação direta entre Azure e Oracle Cloud][ER-OCI] para a conectividade entre Azure e Oráculo

<!--Link References-->
[ER-FAQ]: ./expressroute-faqs.md
[ER-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md