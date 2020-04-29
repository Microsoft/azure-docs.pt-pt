---
title: Trabalhar remotamente utilizando serviços de networking Azure
description: Esta página descreve como pode utilizar os serviços de networking Azure que estão disponíveis para permitir o trabalho remotamente e como mitigar problemas de tráfego resultantes do aumento do número de pessoas que trabalham remotamente.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: e0e5806ec59cd2d75111db213d8511488d043eec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982859"
---
# <a name="working-remotely-using-azure-networking-services"></a>Trabalhar remotamente utilizando serviços de networking Azure

>[!NOTE]
> Este artigo descreve como pode alavancar os serviços de networking Azure, a rede Microsoft e o ecossistema parceiro Azure para trabalhar remotamente e mitigar problemas de rede que poderá estar a enfrentar devido à crise COVID-19.

Este artigo descreve as opções disponíveis para as organizações para estabelecer acesso remoto aos seus utilizadores ou complementar as suas soluções existentes com capacidade adicional durante períodos de utilização máxima. Os arquitetos da rede são confrontados com os seguintes desafios:

- Abordar um aumento da utilização da rede.
- Fornecer conectividade fiável a mais colaboradores da sua empresa e clientes.
- Forneça conectividade a locais remotos em todo o mundo.

Nem todas as redes (por exemplo, redes privadas de WAN e núcleo corporativo) experimentam o congestionamento da carga máxima remota dos trabalhadores. Os estrangulamentos são geralmente reportados apenas em redes de banda larga domésticas e gateways VPN de redes de empresas no local.

Os planeadores de rede podem ajudar a aliviar os estrangulamentos e aliviar o congestionamento da rede, tendo em conta que diferentes tipos de tráfego precisam de diferentes prioridades de tratamento de rede e de alguma reorientação/distribuição de carga inteligente. Por exemplo, o tráfego tele-medecine em tempo real de interação médico-paciente é de alta importância e atraso/nervosismo sensível. Considerando que a replicação do mesmo tráfego entre armazéns não é sensível a atrasos. O tráfego anterior deve ser encaminhado através da mais ideal via de rede com maior qualidade de serviço; que é aceitável encaminhar o tráfego posterior por via sub-óptima.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Partilhar as nossas melhores práticas - A rede Azure é projetada para elasticidade e alta disponibilidade

O Azure foi concebido para resistir a mudanças repentinas na utilização dos recursos e pode ajudar muito durante períodos de utilização máxima. Além disso, a Microsoft mantém e opera uma das maiores redes mundiais. A rede da Microsoft foi concebida para uma elevada disponibilidade que pode suportar diferentes tipos de falhas: desde uma única falha de elementode rede ao fracasso de toda uma região.

A rede Microsoft foi concebida para satisfazer os requisitos e proporcionar um desempenho ideal para diferentes tipos de tráfego de rede, incluindo tráfego multimédia sensível para skype e equipas, CDN, análise de big data em tempo real, armazenamento Azure, Bing e Xbox. Para proporcionar um desempenho ideal para diferentes tipos de tráfego, a rede Microsoft atrai todo o tráfego que está destinado a passar ou a querer transitar o mais próximo possível da origem do tráfego.

>[!NOTE] 
>A utilização das funcionalidades de networking Azure descritas abaixo aproveita o comportamento de atração de tráfego da rede global da Microsoft para proporcionar uma melhor experiência de rede de clientes. O comportamento de atração de tráfego da rede Microsoft ajuda a descarregar o tráfego o mais rapidamente possível a partir das redes de primeira/última milha que podem experimentar congestionamento durante períodos de utilização máxima.
>

## <a name="enable-employees-to-work-remotely"></a>Permitir que os colaboradores trabalhem remotamente

O gateway Azure VPN suporta as ligações VPN ponto-a-local (P2S) e Site-to-Site (S2S). Utilizando o portal Azure VPN, pode escalar as ligações do seu colaborador para aceder de forma segura tanto aos seus recursos implantados azure como aos seus recursos no local. Para mais informações, consulte [Como permitir que os utilizadores trabalhem remotamente](../vpn-gateway/work-remotely-support.md). 

Se estiver a utilizar o Protocolo de Túnel de Tomadas Seguras (SSTP), o número de ligações simultâneas está limitado a 128. Para obter um maior número de ligações, sugerimos a transição para OpenVPN ou IKEv2. Para mais informações, consulte [Transição para protocolo OpenVPN ou IKEv2 da SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

Para aceder aos seus recursos implantados no Azure, os desenvolvedores remotos poderiam utilizar a solução Azure Bastion, em vez da ligação VPN para obter acesso seguro à concha (RDP ou SSH) sem exigir o acesso a IPs públicos sobre os VMs. Para mais informações, consulte [Trabalhar remotamente utilizando o Bastião Azure.](../bastion/work-remotely-support.md)

Para agregar a conexão VPN em larga escala, para apoiar quaisquer ligações entre recursos em diferentes locais globais, em diferentes hubs regionais e redes virtuais faladas, e para otimizar a utilização de várias redes de banda larga doméstica, você pode usar O WAN Virtual Azure. Para mais informações, ver [Lutando para atender ao trabalho a partir de necessidades domésticas? Aqui é onde o Azure Virtual WAN pode ajudar.](../virtual-wan/work-remotely-support.md)

Outra forma de suportar uma mão de obra remota é implementar uma Infraestrutura de Ambiente de Trabalho Virtual (VDI) hospedada na sua rede virtual Azure, protegida com uma Firewall Azure. Por exemplo, o Windows Virtual Desktop (WVD) é um serviço de virtualização de computadores e aplicações que funciona em Azure. Com o Windows Virtual Desktop, pode configurar um ambiente escalável e flexível na sua subscrição Azure sem a necessidade de executar quaisquer servidores de gateway adicionais. Você é apenas responsável pelas máquinas virtuais WVD na sua rede virtual. Para mais informações, consulte o suporte de [trabalho remoto Azure Firewall](../firewall/remote-work-support.md). 

O Azure também tem um conjunto rico de parceiros de ecossistemas. Os nossos parceiros Network Virtual Appliances no Azure também podem ajudar a escalar a conectividade VPN. Para mais informações, consulte [as considerações do Aparelho Virtual da Rede (NVA) para trabalhos remotos](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Alargar a ligação dos colaboradores ao acesso a recursos distribuídos globalmente

Os seguintes serviços Azure podem ajudar a permitir aos colaboradores o acesso aos seus recursos distribuídos globalmente. Os seus recursos podem estar em qualquer uma das regiões de Azure, redes no local, ou mesmo em outras nuvens públicas ou privadas. 

- **Peering de rede virtual Azure:** Se implementar os seus recursos em mais de uma região Azure e/ou se agregar a conectividade de colaboradores que trabalham remotamente usando várias redes virtuais, pode estabelecer conectividade entre as múltiplas redes virtuais Azure utilizando o peering de rede virtual. Para mais informações, consulte o peering da [rede Virtual.][VNet-peer]

- **Solução baseada em VPN Azure**: Para os seus funcionários remotos ligados ao Azure via P2S ou S2S VPN, pode permitir o acesso às redes no local configurando o S2S VPN entre as suas redes no local e o gateway Azure VPN. Para mais informações, consulte [Criar uma ligação Site-a-Site][S2S].

- **ExpressRoute**: Utilizando o peering privado ExpressRoute pode permitir a conectividade privada entre as suas implementações azure e a infraestrutura no local ou a sua infraestrutura numa instalação de co-localização. O ExpressRoute, através do peering da Microsoft, também permite aceder a pontos finais públicos na Microsoft a partir da sua rede no local. As ligações do ExpressRoute não passam para a Internet pública. Oferecem conectividade segura, fiabilidade, maior entrada, com latências mais baixas e consistentes do que as ligações típicas através da Internet. Para obter mais informações, veja [Descrição geral do ExpressRoute][ExR]. Aproveitar o seu fornecedor de rede existente que já faz parte do nosso ecossistema parceiro [ExpressRoute][ExR-eco] pode ajudar a reduzir o tempo para obter grandes ligações de largura de banda à Microsoft.  Utilizando o [ExpressRoute Direct][ExR-D] pode ligar diretamente a sua rede no local à espinha dorsal da Microsoft. O ExpressRoute Direct oferece duas opções diferentes de taxa de linha de 10 Gbps duplos ou 100 Gbps. 

- **Azure Virtual WAN**: O Wan Virtual Azure permite uma interoperabilidade perfeita entre as suas ligações VPN e circuitos ExpressRoute. Como mencionado anteriormente, o Azure Virtual WAN também apoia quaisquer ligações entre recursos em diferentes locais globais on-prem, em diferentes hubs regionais e redes virtuais faladas

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Escalar a conectividade do cliente para fazer frente aos recursos

Durante os momentos em que mais pessoas ficam online, muitos sites corporativos experimentam um aumento do tráfego de clientes. O Portal de Aplicações Azure pode ajudar a gerir este aumento da carga de trabalho frontal. Para mais informações, consulte o [suporte de tráfego elevado do Application Gateway.](../application-gateway/high-traffic-support.md)

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Suporte da Microsoft para tráfego multi-nuvem

Para as suas implementações noutras nuvens públicas, a Microsoft pode fornecer conectividade global. Azure Virtual WAN, VPN ou ExpressRoute podem ajudar neste aspeto. Para estender a conectividade de Azure a outras nuvens, pode configurar o S2S VPN entre as duas nuvens. Também pode estabelecer conectividade do Azure para outras nuvens públicas usando o ExpressRoute. A nuvem oracle faz parte do ecossistema parceiro ExpressRoute. Pode [estabelecer uma interligação direta entre][Az-OCI]a Azure e a Oracle Cloud Infrastructure . A maioria dos prestadores de serviços que fazem parte do ecossistema parceiro ExpressRoute também oferecem conectividade privada a outras nuvens públicas. Aproveitando estes prestadores de serviços, pode estabelecer conectividade privada entre as suas implementações em Azure e outras nuvens através do ExpressRoute.

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos discutem como diferentes funcionalidades de networking Azure podem ser usadas para escalar os utilizadores para trabalhar remotamente:

| **Artigo** | **Descrição** |
| --- | --- |
| [Como permitir que os utilizadores trabalhem remotamente](../vpn-gateway/work-remotely-support.md) | Reveja as opções disponíveis para configurar o acesso remoto aos utilizadores ou para complementar as suas soluções existentes com capacidade adicional para a sua organização.|
| [Lutando para atender ao trabalho das necessidades de casa? Aqui é onde O WAN Virtual Azure pode ajudar](../virtual-wan/work-remotely-support.md) | Utilize o Azure Virtual WAN para responder às necessidades de conectividade remota da sua organização.|
| [Suporte de tráfego elevado para o Gateway de Aplicação](../application-gateway/high-traffic-support.md) | Utilize o Gateway de Aplicação com firewall de aplicação web (WAF) para uma forma escalável e segura de gerir o tráfego para as suas aplicações web. |
| [Considerações de aparelhos virtuais de rede (NVA) para trabalhos remotos](../vpn-gateway/nva-work-remotely-support.md)|Reveja as orientações sobre a alavancagem de NVAs em Azure para fornecer soluções de acesso remoto. |
| [Transição para o protocolo OpenVPN ou IKEv2 da SSTP](https://go.microsoft.com/fwlink/?linkid=2124112) | Ultrapasse o limite de ligação 128 simultânea do SSTP através da transição para o protocolo OpenVPN ou IKEv2.|
| [Trabalhar remotamente usando o Bastião Azure](../bastion/work-remotely-support.md) | Fornecer conectividade RDP/SSH segura e sem emenda às máquinas virtuais dentro da rede virtual Azure, diretamente no portal Azure, sem a utilização de um endereço IP público. |
| [Utilização do Azure ExpressRoute para criar conectividade híbrida para suportar utilizadores remotos](../expressroute/work-remotely-support.md) | Utilize o ExpressRoute para conectividade híbrida para permitir que os utilizadores da sua organização trabalhem remotamente.|
| [Suporte de trabalho remoto Azure Firewall](../firewall/remote-work-support.md)|Proteja os recursos da rede virtual Azure utilizando o Azure Firewall. |

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
