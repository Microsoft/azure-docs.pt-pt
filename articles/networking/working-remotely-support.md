---
title: Trabalhando remotamente utilizando serviços de networking Azure
description: Esta página descreve como pode utilizar os serviços de networking Azure que estão disponíveis para permitir o funcionamento remoto e como mitigar problemas de tráfego resultantes do aumento do número de pessoas que trabalham remotamente.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: e0e5806ec59cd2d75111db213d8511488d043eec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80982859"
---
# <a name="working-remotely-using-azure-networking-services"></a>Trabalhando remotamente utilizando serviços de networking Azure

>[!NOTE]
> Este artigo descreve como pode aproveitar os serviços de networking da Azure, a rede Microsoft e o ecossistema parceiro Azure para trabalhar remotamente e mitigar problemas de rede que poderá estar a enfrentar devido à crise covid-19.

Este artigo descreve as opções que estão disponíveis para as organizações para configurar o acesso remoto aos seus utilizadores ou para complementar as suas soluções existentes com capacidade adicional durante períodos de utilização máxima. Os arquitetos da rede são confrontados com os seguintes desafios:

- Abordar um aumento na utilização da rede.
- Fornecer conectividade fiável a mais colaboradores da sua empresa e clientes.
- Fornecer conectividade a locais remotos em todo o mundo.

Nem todas as redes (por exemplo, WAN privadas e redes core corporativas) experimentam o congestionamento da carga de trabalhadores remotos de pico. Os estrangulamentos são geralmente reportados apenas em redes de banda larga doméstica e gateways VPN de redes de empresas no local.

Os planeadores de rede podem ajudar a aliviar os estrangulamentos e aliviar o congestionamento da rede, tendo em conta que diferentes tipos de tráfego precisam de diferentes prioridades de tratamento de rede e de algumas reorientações/distribuição de carga inteligente. Por exemplo, o tráfego tele-medecine em tempo real da interação médico-paciente é de alta importância e o atraso/nervosismo sensível. Considerando que a replicação do mesmo tráfego entre armazenagem não é sensível a atrasos. O tráfego anterior deve ser encaminhado através da via de rede mais ideal com maior qualidade de serviço; que é aceitável encaminhar o tráfego posterior através de uma rota sub-óptima.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Partilhar as nossas melhores práticas - A rede Azure é projetada para elasticidade e alta disponibilidade

O Azure foi concebido para resistir a mudanças repentinas na utilização dos recursos e pode ajudar muito durante períodos de utilização máxima. Além disso, a Microsoft mantém e opera uma das maiores redes mundiais. A rede da Microsoft foi concebida para uma alta disponibilidade que pode resistir a diferentes tipos de falhas: desde uma falha de elementos de rede a falhas de toda uma região.

A rede Microsoft foi concebida para satisfazer os requisitos e fornecer um desempenho ótimo para diferentes tipos de tráfego de rede, incluindo tráfego multimédia sensível para Skype e Equipas, CDN, análise de big data em tempo real, armazenamento Azure, Bing e Xbox. Para proporcionar um desempenho ótimo para diferentes tipos de tráfego, a rede Microsoft atrai todo o tráfego que se destina ou pretende atravessar os seus recursos o mais próximo possível da origem do tráfego.

>[!NOTE] 
>A utilização das funcionalidades de networking Azure descritas abaixo aproveita o comportamento de atração de tráfego da rede global da Microsoft para proporcionar uma melhor experiência de networking de clientes. O comportamento de atração de tráfego da rede Microsoft ajuda a descarregar o tráfego o mais rapidamente possível a partir das redes de primeira/última milha que podem experimentar congestionamento durante períodos de utilização máxima.
>

## <a name="enable-employees-to-work-remotely"></a>Permitir que os colaboradores trabalhem remotamente

O gateway Azure VPN suporta ligações VPN ponto-a-local (P2S) e local-a-local (S2S). Utilizando o gateway Azure VPN, pode escalar as ligações do seu colaborador para aceder de forma segura tanto aos recursos implantados do seu Azure como aos seus recursos no local. Para obter mais informações, consulte [Como permitir que os utilizadores funcionem remotamente.](../vpn-gateway/work-remotely-support.md) 

Se estiver a utilizar o Protocolo de Túnel de Tomadas Seguras (SSTP), o número de ligações simultâneas é limitado a 128. Para obter um maior número de ligações, sugerimos a transição para OpenVPN ou IKEv2. Para obter mais informações, consulte [o protocolo Transição para OpenVPN ou IKEv2 da SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

Para aceder aos seus recursos implantados em Azure, os desenvolvedores remotos poderiam utilizar a solução Azure Bastion, em vez de ligação VPN para obter acesso seguro à concha (RDP ou SSH) sem exigir o acesso de IPs públicos nos VMs. Para obter mais informações, consulte [Trabalhar remotamente utilizando o Bastião Azure.](../bastion/work-remotely-support.md)

Para agregar a ligação VPN em larga escala, para suportar qualquer ligação entre recursos em diferentes localizações globais no local, em diferentes centros regionais e redes virtuais falada, e para otimizar a utilização de várias redes de banda larga doméstica, pode utilizar a Azure Virtual WAN. Para mais informações, consulte [a Luta para atender ao trabalho a partir das necessidades domésticas? Aqui é onde Azure Virtual WAN pode ajudar.](../virtual-wan/work-remotely-support.md)

Outra forma de suportar uma força de trabalho remota é implementar uma Infraestrutura virtual de Desktop (VDI) hospedada na sua rede virtual Azure, protegida com uma Firewall Azure. Por exemplo, o Windows Virtual Desktop (WVD) é um serviço de virtualização de desktop e aplicações que funciona em Azure. Com o Windows Virtual Desktop, pode configurar um ambiente escalável e flexível na sua subscrição Azure sem a necessidade de executar quaisquer servidores de gateway adicionais. É responsável apenas pelas máquinas virtuais WVD na sua rede virtual. Para obter mais informações, consulte [o suporte ao trabalho remoto do Azure Firewall](../firewall/remote-work-support.md). 

A Azure também tem um rico conjunto de parceiros do ecossistema. Os nossos parceiros Network Virtual Appliances em Azure também podem ajudar a escalar a conectividade VPN. Para obter mais informações, consulte considerações de [Aparelho Virtual de Rede (NVA) para trabalhos remotos](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Alargar a ligação dos colaboradores ao acesso a recursos distribuídos globalmente

Os seguintes serviços Azure podem ajudar a permitir que os colaboradores acedam aos seus recursos distribuídos globalmente. Os seus recursos podem estar em qualquer uma das regiões do Azure, redes no local, ou mesmo em outras nuvens públicas ou privadas. 

- **Azure rede virtual a espreitar**: Se implementar os seus recursos em mais de uma região do Azure e/ou se agregar a conectividade de funcionários que trabalham remotamente usando várias redes virtuais, pode estabelecer conectividade entre as redes virtuais Azure múltiplas usando o espreitamento de rede virtual. Para obter mais informações, consulte [a rede Virtual a espreitar.][VNet-peer]

- **Solução baseada em Azure VPN**: Para os seus colaboradores remotos ligados ao Azure via P2S ou S2S VPN, pode permitir o acesso a redes no local, configurando a VPN S2S entre as suas redes no local e o gateway Azure VPN. Para obter mais informações, consulte [Criar uma ligação Site-to-Site.][S2S]

- **ExpressRoute**: Utilizando o espreitamento privado ExpressRoute, pode permitir a conectividade privada entre as suas infraestruturas Azure e as infraestruturas no local ou a sua infraestrutura numa instalação de co-localização. O ExpressRoute, via Microsoft, também permite aceder a pontos finais públicos na Microsoft a partir da sua rede no local. As ligações do ExpressRoute não passam para a Internet pública. Oferecem conectividade segura, fiabilidade, maior produção, com latências mais baixas e consistentes do que as ligações típicas através da Internet. Para obter mais informações, veja [Descrição geral do ExpressRoute][ExR]. Aproveitar o seu fornecedor de rede existente que já faz parte do nosso [ecossistema parceiro ExpressRoute][ExR-eco] pode ajudar a reduzir o tempo para obter grandes ligações de largura de banda à Microsoft.  Utilizando [o ExpressRoute Direct,][ExR-D] pode ligar diretamente a sua rede no local à espinha dorsal da Microsoft. O ExpressRoute Direct oferece duas opções diferentes de linha de 10 Gbps ou 100 Gbps. 

- **Azure Virtual WAN**: Azure Virtual WAN permite uma interoperabilidade perfeita entre as ligações VPN e os circuitos ExpressRoute. Como mencionado anteriormente, a Azure Virtual WAN também suporta qualquer ligação entre recursos em diferentes localizações globais on-prem, em diferentes centros regionais e redes virtuais falando

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Escala a conectividade do cliente para os recursos frontend

Durante os momentos em que mais pessoas ficam online, muitos sites corporativos experimentam um aumento do tráfego de clientes. O Azure Application Gateway pode ajudar a gerir esta carga de trabalho aumentada. Para obter mais informações, consulte [o suporte de tráfego elevado application Gateway](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Suporte da Microsoft para tráfego multi-nuvem

Para as suas implementações em outras nuvens públicas, a Microsoft pode fornecer conectividade global. Azure Virtual WAN, VPN ou ExpressRoute podem ajudar neste aspeto. Para estender a conectividade de Azure a outras nuvens, pode configurar a VPN S2S entre as duas nuvens. Também pode estabelecer conectividade de Azure a outras nuvens públicas usando ExpressRoute. A nuvem oráculo faz parte do ecossistema parceiro ExpressRoute. Pode [configurar uma interligação direta entre a Infraestrutura Azure e a Oracle Cloud Infrastructure][Az-OCI]. A maioria dos prestadores de serviços que fazem parte do ecossistema parceiro ExpressRoute também oferecem conectividade privada a outras nuvens públicas. Aproveitando estes prestadores de serviços, pode estabelecer conectividade privada entre as suas implementações em Azure e outras nuvens via ExpressRoute.

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos discutem como diferentes funcionalidades de rede Azure podem ser usadas para escalar os utilizadores para trabalhar remotamente:

| **Artigo** | **Descrição** |
| --- | --- |
| [Como permitir que os utilizadores funcionem remotamente](../vpn-gateway/work-remotely-support.md) | Reveja as opções disponíveis para configurar o acesso remoto aos utilizadores ou complementar as suas soluções existentes com capacidade adicional para a sua organização.|
| [Lutando para cuidar do trabalho a partir das necessidades domésticas? Aqui é onde Azure Virtual WAN pode ajudar](../virtual-wan/work-remotely-support.md) | Utilize a Azure Virtual WAN para responder às necessidades de conectividade remota da sua organização.|
| [Suporte de tráfego elevado para o Gateway de Aplicação](../application-gateway/high-traffic-support.md) | Utilize o Gateway de Aplicações com firewall de aplicação web (WAF) para uma forma escalável e segura de gerir o tráfego das suas aplicações web. |
| [Considerações de Aparelho Virtual de Rede (NVA) para trabalhos remotos](../vpn-gateway/nva-work-remotely-support.md)|Reveja as orientações sobre a utilização de NVAs em Azure para fornecer soluções de acesso remoto. |
| [Transição para protocolo OpenVPN ou IKEv2 da SSTP](https://go.microsoft.com/fwlink/?linkid=2124112) | Supere o limite de ligação simultânea de 128 SSTP através da transição para o protocolo OpenVPN ou IKEv2.|
| [Trabalhando remotamente usando Azure Bastion](../bastion/work-remotely-support.md) | Fornecer conectividade SECURE/SSH a máquinas virtuais dentro da rede virtual Azure, diretamente no portal Azure, sem a utilização de um endereço IP público. |
| [Utilizar o Azure ExpressRoute para criar conectividade híbrida para suportar utilizadores remotos](../expressroute/work-remotely-support.md) | Utilize o ExpressRoute para conectividade híbrida para permitir que os utilizadores da sua organização funcionem remotamente.|
| [Suporte remoto de trabalho Azure Firewall](../firewall/remote-work-support.md)|Proteja os recursos de rede virtual Azure utilizando o Azure Firewall. |

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
