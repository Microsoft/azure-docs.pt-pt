---
title: Ligando Azure às nuvens públicas [ Microsoft Docs
description: Descreva várias formas de ligar Azure a outras nuvens públicas
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889632"
---
# <a name="connecting-azure-with-public-clouds"></a>Conectando Azure com nuvens públicas

Muitas empresas estão a seguir uma estratégia multi-nuvem devido a objetivos empresariais e técnicos. Estes incluem custos, flexibilidade, disponibilidade de recursos, redundância, soberania de dados, etc. Esta estratégia ajuda-os a alavancar o melhor de ambas as nuvens. 

Esta abordagem também coloca desafios para a empresa em termos de arquitetura de rede e aplicação. Alguns destes desafios são a latência e a entrada de dados. Para responder a estes desafios, os clientes procuram ligar-se diretamente a várias nuvens. Alguns prestadores de serviços fornecem uma solução para ligar vários fornecedores de nuvem para os clientes. Noutros casos, o cliente pode implementar o seu próprio router para ligar várias nuvens públicas.
## <a name="connectivity-via-expressroute"></a>Conectividade via ExpressRoute
O ExpressRoute permite que os clientes alarguem as suas redes no local para a nuvem da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, os clientes podem estabelecer ligações aos serviços de cloud da Microsoft.

Existem três formas de ligar via ExpressRoute.

1. Fornecedor de camada3
2. Fornecedor de camada2
3. Ligação direta

### <a name="layer3-provider"></a>Fornecedor de Camada3

Os fornecedores de camada3 são vulgarmente conhecidos como fornecedores IP VPN ou MPLS VPN. Os clientes aproveitam estes fornecedores para uma conectividade multiponto entre os seus centros de dados, sucursais e a nuvem. Os clientes ligam-se ao fornecedor L3 via BGP ou através de uma rota estática por defeito. O prestador de serviços anuncia rotas entre os sites de clientes, datacenters e nuvem pública. 
 
Ao ligar-se através do fornecedor Layer3, a Microsoft irá anunciar as rotas VNET do cliente para o fornecedor de serviços através do BGP. O fornecedor pode ter duas implementações diferentes.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

O fornecedor pode estar a aterrar cada fornecedor de nuvem num VRF separado, se o tráfego de todos os fornecedores de nuvem chegar ao router do cliente. Se o cliente estiver a executar o BGP com o prestador de serviços, então estas rotas serão republicitadas a outros fornecedores de nuvem por padrão. 

Se o prestador de serviços estiver a desembarcar todos os fornecedores de nuvem no mesmo VRF, então as rotas serão publicitadas diretamente a outros fornecedores de nuvem do prestador de serviços. Isto está assumindo a operação padrão de BGP onde as rotas do EBGP são publicitadas a outros vizinhos do EBGP por padrão.

Cada nuvem pública tem um limite de prefixo diferente, pelo que, ao mesmo tempo que distribui as rotas, o prestador de serviços deve ter cuidado na distribuição das rotas.

### <a name="layer2-provider-and-direct-connection"></a>Fornecedor de Camada2 e conexão direta

Embora a conectividade física em ambos os modelos seja diferente, mas em camada3 BGP é estabelecido diretamente entre o MSEE e o router do cliente. Para o ExpressRoute Direct o cliente liga-se diretamente ao MSEE. Enquanto no caso da Layer2, o prestador de serviços estende a VLAN das instalações do cliente para a nuvem. Os clientes executam o BGP em cima da rede de camada2 para ligar os seus DCs à nuvem.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
Em ambos os casos, o cliente terá ligações ponto-a-ponto a cada uma das nuvens públicas. O cliente estabelecerá uma ligação BGP separada a cada nuvem pública. As rotas recebidas por um fornecedor de nuvem serão publicitadas a outro fornecedor de nuvem por padrão. Cada fornecedor de nuvem tem um limite de prefixo diferente, por isso, ao publicitar as rotas, o cliente deve cuidar destes limites. O cliente pode usar os botões BGP habituais com a Microsoft enquanto publicita rotas de outras nuvens públicas.

## <a name="direct-connection-with-expressroute"></a>Ligação direta com expressRoute

Os clientes podem optar por ligar o ExpressRoute diretamente à oferta direta de conectividade do fornecedor de nuvem. Dois fornecedores de nuvem serão ligados de volta para trás e o BGP será estabelecido diretamente entre os seus routers. Este tipo de ligação está disponível com a Oracle hoje.

## <a name="site-to-site-vpn"></a>VPN site a site

Os clientes podem aproveitar a Internet para ligar os seus casos em Azure a outras nuvens públicas. Quase todos os fornecedores de nuvem oferecem capacidades VPN site-to-site. No entanto, pode haver incompatibilidades por falta de certas variantes. Por exemplo, alguns fornecedores de nuvem apenas suportam o IKEv1, pelo que existe um ponto final de rescisão VPN necessário nessa nuvem. Para os fornecedores de nuvem que suportam o IKEv2, pode ser estabelecido um túnel direto entre gateways VPN em ambos os fornecedores de nuvem.

A VPN do site-a-site não é considerada uma solução de alta entrada e baixa latência. No entanto, pode ser usado como um backup para a conectividade física.

## <a name="next-steps"></a>Passos seguintes
Consulte o [ExpressRoute FAQ][ER-FAQ] para mais questões sobre expressRoute e conectividade de rede virtual.

Consulte [A ligação direta entre Azure e Oracle Cloud][ER-OCI] para conectividade entre Azure e Oráculo

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



