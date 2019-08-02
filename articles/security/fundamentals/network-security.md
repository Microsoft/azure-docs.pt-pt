---
title: Segurança de rede do Azure | Microsoft Docs
description: Saiba mais sobre os serviços de computação baseados em nuvem que incluem uma ampla seleção de instâncias de computação & serviços que podem ser escalados verticalmente e reduzidos automaticamente para atender às necessidades de seu aplicativo ou empresa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: db3f5aca0240c19f67d5d0775148d5eec76daa03
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726993"
---
# <a name="azure-network-security"></a>Segurança de rede do Azure

Sabemos que a segurança é o trabalho um na nuvem e como é importante que você encontre informações precisas e oportunas sobre a segurança do Azure. Um dos melhores motivos para usar o Azure para seus aplicativos e serviços é aproveitar a ampla variedade de ferramentas e recursos de segurança do Azure. Essas ferramentas e recursos ajudam a tornar possível criar soluções seguras na plataforma do Azure.

Microsoft Azure fornece confidencialidade, integridade e disponibilidade dos dados do cliente, ao mesmo tempo em que também permite a responsabilidade transparente. Para ajudá-lo a entender melhor a coleção de controles de segurança de rede implementada em Microsoft Azure da perspectiva do cliente, este artigo, "segurança de rede do Azure", foi escrito para fornecer uma visão abrangente dos controles de segurança de rede disponível com Microsoft Azure.

Este documento destina-se a informá-lo sobre a ampla variedade de controles de rede que você pode configurar para aprimorar a segurança das soluções implantadas no Azure. Se você estiver interessado no que a Microsoft faz para proteger a malha de rede da própria plataforma Azure, consulte a seção segurança do Azure na [central de confiabilidade da Microsoft](https://microsoft.com/en-us/trustcenter/cloudservices/azure).

## <a name="azure-platform"></a>Plataforma Azure

O Azure é uma plataforma de serviço de nuvem pública que dá suporte a uma ampla seleção de sistemas operacionais, linguagens de programação, estruturas, ferramentas, bancos de dados e dispositivos.  Ele pode executar contêineres do Linux com a integração do Docker; Crie aplicativos com JavaScript, Python, .NET, PHP, Java e node. js; Crie back-ends para dispositivos iOS, Android e Windows. Os serviços de nuvem do Azure dão suporte às mesmas tecnologias que milhões de desenvolvedores e profissionais de ti já contam e confiam.

Ao se basear ou migrar ativos de ti para um provedor de serviços de nuvem pública, você estará confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que eles fornecem para gerenciar a segurança de seus ativos baseados em nuvem.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para hospedar milhões de clientes simultaneamente, além de fornecer uma base confiável sobre a qual as empresas podem atender aos seus requisitos de segurança. Além disso, o Azure fornece uma ampla coleção de opções de segurança configuráveis e a capacidade de controlá-las para que você possa personalizar a segurança para atender aos requisitos exclusivos das implantações de sua organização.

## <a name="abstract"></a>Abstrato

Os serviços de nuvem pública da Microsoft fornecem serviços e infraestrutura de hiperescala, recursos de nível empresarial e muitas opções de conectividade híbrida. Você pode optar por acessar esses serviços pela Internet ou com o Azure ExpressRoute, que fornece conectividade de rede privada. A plataforma Microsoft Azure permite estender diretamente sua infraestrutura para a nuvem e criar arquiteturas de várias camadas. Além disso, terceiros podem habilitar recursos aprimorados oferecendo serviços de segurança e dispositivos virtuais.

Os serviços de rede do Azure maximizam a flexibilidade, a disponibilidade, a resiliência, a segurança e a integridade por design. Este white paper fornece detalhes sobre as funções de rede do Azure e informações sobre como os clientes podem usar os recursos de segurança nativos do Azure para ajudar a proteger seus ativos de informações.

Os públicos-alvo para este White Paper incluem:

- Gerentes técnicos, administradores de rede e desenvolvedores que estão procurando soluções de segurança disponíveis e com suporte no Azure.

-   Os SMEs ou executivos de processos empresariais que desejam obter uma visão geral de alto nível das tecnologias e dos serviços de rede do Azure que são relevantes em discussões sobre a segurança de rede na nuvem pública do Azure.

## <a name="azure-networking-big-picture"></a>Visão geral de rede do Azure
O Microsoft Azure inclui uma infraestrutura de rede robusta para dar suporte aos seus requisitos de conectividade de serviço e aplicativo. A conectividade de rede é possível entre os recursos localizados no Azure, entre os recursos hospedados no local e o Azure e de e para a Internet e o Azure.

![Visão geral de rede do Azure](./media/network-security/azure-network-security-fig-1.png)

A [infraestrutura de rede do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) permite que você conecte com segurança os recursos do Azure entre si com redes virtuais (VNets). Uma VNet é uma representação de sua própria rede na nuvem. Uma VNet é um isolamento lógico da rede de nuvem do Azure dedicada à sua assinatura. Você pode conectar o VNets às suas redes locais.

O Azure dá suporte à conectividade de link WAN dedicada à sua rede local e a uma rede virtual do Azure com o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). O link entre o Azure e seu site usa uma conexão dedicada que não passa pela Internet pública. Se o aplicativo do Azure estiver sendo executado em vários datacenters, você poderá usar o [Gerenciador de tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para rotear solicitações de usuários de forma inteligente entre instâncias do aplicativo. Você também pode rotear o tráfego para os serviços que não estão em execução no Azure se eles estiverem acessíveis pela Internet.

## <a name="enterprise-view-of-azure-networking-components"></a>Exibição empresarial de componentes de rede do Azure
O Azure tem muitos componentes de rede que são relevantes para as discussões de segurança de rede. descrevemos esses componentes de rede e nos concentramos nos problemas de segurança relacionados a eles.

> [!Note]
> Nem todos os aspectos da rede do Azure são descritos – discutimos apenas aqueles considerados como consistentes no planejamento e na criação de uma infraestrutura de rede segura em relação aos seus serviços e aplicativos que você implanta no Azure.

Neste artigo, abordaremos os seguintes recursos corporativos de rede do Azure:

-   Conectividade de rede básica

-   Conectividade híbrida

-   Controlos de Segurança

-   Validação de rede

### <a name="basic-network-connectivity"></a>Conectividade de rede básica

O serviço de [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) permite que você conecte com segurança recursos do Azure entre si com redes virtuais (VNet). Uma VNet é uma representação de sua própria rede na nuvem. Uma VNet é um isolamento lógico da infraestrutura de rede do Azure dedicada à sua assinatura. Você também pode conectar o VNets entre si e às suas redes locais usando VPNs site a site e [links WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)dedicados.

![Conectividade de rede básica](./media/network-security/azure-network-security-fig-2.png)

Com a compreensão de que você usa VMs para hospedar servidores no Azure, a pergunta é como essas VMs se conectam a uma rede. A resposta é que as VMs se conectam a uma [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

As redes virtuais do Azure são como as redes virtuais que você usa localmente com suas próprias soluções de plataforma de virtualização, como o Microsoft Hyper-V ou o VMware.

#### <a name="intra-vnet-connectivity"></a>Conectividade interna da VNet

Você pode conectar o VNets entre si, permitindo que os recursos conectados à VNet se comuniquem entre si em VNets. Você pode usar uma ou ambas as opções a seguir para conectar o VNets entre si:

- **Emparelhamento** Permite que os recursos conectados a diferentes VNets do Azure no mesmo local do Azure se comuniquem entre si. A largura de banda e a latência em toda a VNet são as mesmas que se os recursos estivessem conectados à mesma VNet. Para saber mais sobre emparelhamento, leia [emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

  ![Peering](./media/network-security/azure-network-security-fig-3.png)

- **Conexão VNet a VNet:** Habilita recursos conectados a uma VNet diferente do Azure dentro dos mesmos locais do Azure ou diferentes. Ao contrário do emparelhamento, a largura de banda é limitada entre VNets porque o tráfego deve fluir por meio de um gateway de VPN do Azure.

![Conexão VNet a VNet](./media/network-security/azure-network-security-fig-4.png)


Para saber mais sobre como conectar o VNets com uma conexão VNet a VNet, leia o [artigo configurar uma conexão de vnet para vnet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Recursos de rede virtual do Azure:

Como você pode ver, uma rede virtual do Azure fornece máquinas virtuais para se conectar à rede para que elas possam se conectar a outros recursos de rede de maneira segura. No entanto, a conectividade básica é apenas o começo. Os seguintes recursos do serviço de rede virtual do Azure expõem as características de segurança da rede virtual do Azure:

-   Isolamento

-   Conectividade Internet

-   Conectividade de recursos do Azure

-   Conectividade VNet

-   Conectividade no local

-   Filtragem do tráfego

-   Encaminhamento

**Isolado**

VNets são [isoladas](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) uns dos outros. Você pode criar VNets separadas para desenvolvimento, teste e produção que usam os mesmos blocos de endereço [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Por outro lado, você pode criar vários VNets que usam blocos de endereço CIDR diferentes e conectam redes. Você pode segmentar uma VNet em várias sub-redes.

O Azure fornece resolução de nomes interna para instâncias de função de VMs e [serviços de nuvem](https://azure.microsoft.com/services/cloud-services/) conectadas a uma VNet. Opcionalmente, você pode configurar uma VNet para usar seus próprios servidores DNS, em vez de usar a resolução de nomes internos do Azure.

Você pode implementar vários VNets em cada [assinatura](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure e [região](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json)do Azure. Cada VNet é isolada de outras VNets. Para cada VNet, você pode:

-   Especificar um espaço de endereços IP privado personalizado, utilizando endereços públicos e privados (RFC 1918). O Azure atribui recursos conectados à VNet de um endereço IP privado do espaço de endereço, que você atribui.

-   Segmente a VNet em uma ou mais sub-redes e aloque uma parte do espaço de endereço da VNet para cada sub-rede.

-   Use a resolução de nomes fornecida pelo Azure ou especifique seu próprio servidor DNS para uso por recursos conectados a uma VNet. Para saber mais sobre a resolução de nomes em VNets, leia a [resolução de nomes para VMs e serviços de nuvem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Ligação à Internet**

Todas as instâncias de função de [VM (máquinas virtuais) do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/) e serviços de nuvem conectadas a uma VNet têm acesso à Internet, por padrão. Você também pode habilitar o acesso de entrada a recursos específicos, conforme necessário. As instâncias de função (VM) e serviços de nuvem conectadas a uma VNet têm acesso à Internet, por padrão. Você também pode habilitar o acesso de entrada a recursos específicos, conforme necessário.

Por padrão, todos os recursos conectados a uma VNet têm conectividade de saída com a Internet. O endereço IP privado do recurso é um endereço de rede de origem traduzido (SNAT) para um endereço IP público pela infraestrutura do Azure. Você pode alterar a conectividade padrão implementando o roteamento personalizado e a filtragem de tráfego. Para saber mais sobre a conectividade de Internet de saída, leia [noções básicas sobre conexões de saída no Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para comunicar a entrada com os recursos do Azure da Internet ou para comunicar a saída à Internet sem SNAT, um recurso deve ser atribuído a um endereço IP público. Para saber mais sobre endereços IP públicos, leia os [endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Conectividade de recursos do Azure**

[Recursos do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) , como serviços de nuvem e VMS, podem ser conectados à mesma VNet. Os recursos podem se conectar entre si usando endereços IP privados, mesmo se estiverem em sub-redes diferentes. O Azure fornece roteamento padrão entre sub-redes, VNets e redes locais, para que você não precise configurar e gerenciar rotas.

Você pode conectar vários recursos do Azure a uma VNet, como máquinas virtuais (VM), serviços de nuvem, ambientes de serviço de aplicativo e conjuntos de dimensionamento de máquinas virtuais. As VMs se conectam a uma sub-rede em uma VNet por meio de uma NIC (interface de rede). Para saber mais sobre NICs, leia as [interfaces de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Conectividade VNet**

O [VNets](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) pode ser conectado entre si, permitindo que os recursos conectados a qualquer vnet se comuniquem com qualquer recurso em qualquer outra vnet.

Você pode conectar o VNets entre si, permitindo que os recursos conectados à VNet se comuniquem entre si em VNets. Você pode usar uma ou ambas as opções a seguir para conectar o VNets entre si:

- **Emparelhamento** Permite que os recursos conectados a diferentes VNets do Azure no mesmo local do Azure se comuniquem entre si. A largura de banda e a latência em VNets são as mesmas que se os recursos estivessem conectados ao mesmo VNet.To saiba mais sobre o emparelhamento, leia o [emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **Conexão VNet a VNet:** Habilita recursos conectados a uma VNet diferente do Azure dentro dos mesmos locais do Azure ou diferentes. Ao contrário do emparelhamento, a largura de banda é limitada entre VNets porque o tráfego deve fluir por meio de um gateway de VPN do Azure. Para saber mais sobre como conectar o VNets com uma conexão VNet a VNet. Para saber mais, leia [Configurar uma conexão de vnet para vnet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Conectividade no local**

O VNets pode ser conectado a redes [locais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) por meio de conexões de rede privada entre sua rede e o Azure, ou por meio de uma conexão VPN site a site pela Internet.

Você pode conectar sua rede local a uma VNet usando qualquer combinação das seguintes opções:

- **VPN (rede virtual privada) ponto a site:** Estabelecida entre um único PC conectado à sua rede e a VNet. Este tipo de ligação é excelente se estiver a começar a utilizar o Azure ou para os programadores, uma vez que necessita de pouca ou nenhumas alterações à sua rede existente. A conexão usa o protocolo SSTP para fornecer comunicação criptografada pela Internet entre o PC e a VNet. A latência de uma VPN ponto a site é imprevisível, pois o tráfego atravessa a Internet.

- **VPN site a site:** Estabelecido entre o dispositivo VPN e um gateway de VPN do Azure. Esse tipo de conexão habilita qualquer recurso local que você autorizar a acessar uma VNet. A conexão é uma VPN IPsec/IKE que fornece comunicação criptografada pela Internet entre o dispositivo local e o gateway de VPN do Azure. A latência de uma conexão site a site é imprevisível, pois o tráfego atravessa a Internet.

- **Azure ExpressRoute:** Estabelecida entre sua rede e o Azure, por meio de um parceiro de ExpressRoute. Esta ligação é privada. O tráfego não atravessa a Internet. A latência de uma conexão do ExpressRoute é previsível, pois o tráfego não atravessa a Internet. Para saber mais sobre todas as opções de conexão anteriores, leia os diagramas de [topologia de conexão](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtragem do tráfego**

Instâncias de função de serviços de nuvem e de VM o [tráfego de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) pode ser filtrado de entrada e saída por endereço IP de origem e porta, endereço IP de destino e porta e protocolo.

Pode filtrar o tráfego de rede entre as sub-redes com uma ou ambas das seguintes opções:

- **NSG (grupos de segurança de rede):** Cada NSG pode conter várias regras de segurança de entrada e saída que permitem filtrar o tráfego por endereço IP de origem e de destino, porta e protocolo. Você pode aplicar um NSG a cada NIC em uma VM. Você também pode aplicar um NSG à sub-rede uma NIC ou a outro recurso do Azure, está conectado ao. Para saber mais sobre o NSGs, leia os [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Dispositivos de rede virtual:** Um dispositivo de rede virtual é uma VM que executa um software que executa uma função de rede, como um firewall. Exiba uma lista de NVAs disponíveis no Azure Marketplace. NVAs também estão disponíveis para fornecer otimização de WAN e outras funções de tráfego de rede. NVAs normalmente são usados com rotas BGP ou definidas pelo usuário. Você também pode usar um NVA para filtrar o tráfego entre VNets.

**Encaminhamento**

Opcionalmente, você pode substituir o roteamento padrão do Azure Configurando suas próprias rotas ou usando rotas BGP por meio de um gateway de rede.

O Azure cria tabelas de rotas que permitem que os recursos conectados a qualquer sub-rede em qualquer VNet se comuniquem entre si, por padrão. Pode implementar uma ou ambas das seguintes opções para substituir as rotas predefinidas que o Azure cria:

- **Rotas definidas pelo usuário:** Você pode criar tabelas de rotas personalizadas com rotas que controlam para onde o tráfego é roteado para cada sub-rede. Para saber mais sobre rotas definidas pelo usuário, leia as [rotas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Rotas BGP:** Se você conectar sua VNet à sua rede local usando um gateway de VPN do Azure ou uma conexão de ExpressRoute, você poderá propagar rotas BGP para seu VNets.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Conectividade de Internet híbrida: Conectar-se a uma rede local
Você pode conectar sua rede local a uma VNet usando qualquer combinação das seguintes opções:

-   Conectividade Internet

-   VPN ponto a site (VPN P2S)

-   VPN site a site (VPN S2S)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Conectividade com a Internet

Como seu nome sugere, a conectividade com a Internet torna suas cargas de trabalho acessíveis da Internet, fazendo com que você exponha pontos de extremidade públicos diferentes para cargas de trabalho que residem dentro da rede virtual. Essas cargas de trabalho podem ser expostas usando [Load Balancer voltadas](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) para a Internet ou simplesmente atribuindo um endereço IP público à VM. Dessa forma, é possível que qualquer coisa na Internet seja capaz de alcançar essa máquina virtual, desde que um firewall de host, [NSG (grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)e [rotas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) permitam que isso aconteça.

Nesse cenário, você pode expor um aplicativo que precisa ser público para a Internet e ser capaz de se conectar a ele de qualquer lugar ou de locais específicos, dependendo da configuração de suas cargas de trabalho.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>VPN ponto a site ou VPN site a site
Esses dois se enquadram na mesma categoria. Ambos precisam que sua VNet tenha um gateway de VPN e você pode se conectar a ele usando um cliente VPN para sua estação de trabalho como parte da [configuração ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) ou você pode configurar seu [dispositivo VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) local para poder encerrar um site a site VPNs. Dessa forma, os dispositivos locais podem se conectar aos recursos na VNet.

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma P2S é uma ligação VPN através de SSTP (Secure Socket Tunneling Protocol).

![VPN ponto a Site](./media/network-security/azure-network-security-fig-5.png)

As conexões ponto a site são úteis quando você deseja se conectar à sua VNet de um local remoto, como de casa ou de um centro de conferências, ou quando há apenas alguns clientes que precisam se conectar a uma rede virtual.

As ligações P2S não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. O utilizador estabelece a ligação VPN a partir do computador cliente. Portanto, o P2S não é uma maneira recomendada de se conectar ao Azure caso você precise de uma conexão persistente de muitos computadores e dispositivos locais para sua rede do Azure.

![VPN site a Site](./media/network-security/azure-network-security-fig-6.png)

> [!Note]
> Para obter mais informações sobre conexões ponto a site, consulte o [FA v ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Uma ligação de gateway de VPN de Site para Site é utilizada para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2).

Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Essa conexão ocorre pela Internet e permite que você "encapsular" informações dentro de um link criptografado entre sua rede e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. A criptografia de túnel é executada usando o [modo de encapsulamento IPSec](https://technet.microsoft.com/library/cc786385.aspx).

Embora a VPN site a site seja uma tecnologia confiável, confiável e estabelecida, o tráfego dentro do túnel atravessa a Internet. Além disso, a largura de banda é relativamente restrita a um máximo de cerca de 200 Mbps.

Se você precisar de um nível excepcional de segurança ou desempenho para suas conexões entre locais, recomendamos que você use o Azure ExpressRoute para a conectividade entre locais. O ExpressRoute é um link WAN dedicado entre sua localização local ou um provedor de hospedagem do Exchange. Como essa é uma conexão Telco, seus dados não viajam pela Internet e, portanto, não são expostos aos riscos potenciais inerentes às comunicações da Internet.

> [!Note]
> Para obter mais informações sobre gateways de VPN, consulte sobre o [Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Link WAN dedicado
Microsoft Azure ExpressRoute permite que você estenda suas redes locais para o Azure por meio de uma conexão privada dedicada, facilitada por um provedor de conectividade.

As ligações do ExpressRoute não passam para a Internet pública. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.

![ Link WAN dedicado](./media/network-security/azure-network-security-fig-7.png)

> [!Note]
> Para obter informações sobre como conectar sua rede à Microsoft usando o ExpressRoute, consulte [modelos de conectividade do expressroute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) e [visão geral técnica do expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Assim como nas opções de VPN site a site, o ExpressRoute também permite que você se conecte a recursos que não estão necessariamente em apenas uma VNet. Na verdade, dependendo do SKU, você pode se conectar a 10 VNets. Se você tiver o [complemento Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), as conexões com até 100 VNets são possíveis, dependendo da largura de banda. Para saber mais sobre como esses tipos de conexões são semelhantes, leia [diagramas de topologia de conexão](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Controles de segurança
Uma rede virtual do Azure fornece uma rede lógica segura que é isolada de outras redes virtuais e dá suporte a muitos controles de segurança que você usa em suas redes locais. Os clientes criam sua própria estrutura usando: sub-redes — eles usam seu próprio intervalo de endereços IP privado, configuram tabelas de rotas, grupos de segurança de rede, ACLs (listas de controle de acesso), gateways e dispositivos virtuais para executar suas cargas de trabalho na nuvem.

Veja a seguir os controles de segurança que você pode usar em suas redes virtuais do Azure:

-   Controles de acesso à rede

-   Rotas definidas pelo usuário

-   Dispositivo de segurança de rede

-   Gateway de Aplicação

-   Firewall do aplicativo Web do Azure

-   Controle de disponibilidade de rede

#### <a name="network-access-controls"></a>Controles de acesso à rede
Embora a VNet (rede virtual) do Azure seja a base do modelo de rede do Azure e fornece isolamento e proteção, os [NSG (grupos de segurança de rede)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) são a principal ferramenta usada para impor e controlar as regras de tráfego de rede no nível da rede.

![ Controles de acesso à rede](./media/network-security/azure-network-security-fig-8.png)


Você pode controlar o acesso permitindo ou negando a comunicação entre as cargas de trabalho em uma rede virtual, de sistemas em redes do cliente por meio de conectividade entre locais ou comunicação direta com a Internet.

No diagrama, VNets e NSGs residem em uma camada específica na pilha de segurança geral do Azure, em que NSGs, UDR e dispositivos virtuais de rede podem ser usados para criar limites de segurança para proteger as implantações de aplicativo na rede protegida.

NSGs use uma 5 tupla para avaliar o tráfego (e são usados nas regras que você configura para o NSG):

-   [Endereço IP de origem e de destino](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Porta de origem e de destino](https://technet.microsoft.com/library/dd197515)

-   Protocolo: [TCP (protocolo de controle de transmissão)](https://technet.microsoft.com/library/cc940037.aspx) ou [UDP (protocolo de datagrama de usuário)](https://technet.microsoft.com/library/cc940034.aspx)

Isso significa que você pode controlar o acesso entre uma única VM e um grupo de VMs, ou uma única VM para outra VM única ou entre sub-redes inteiras. Novamente, lembre-se de que isso é uma simples filtragem de pacotes com estado, e não inspeção de pacote completa. Não há nenhuma validação de protocolo ou identificador de nível de rede ou capacidade de IPS em um grupo de segurança de rede.

Um NSG vem com algumas regras internas das quais você deve estar atento. Nomeadamente:

-   **Permitir todo o tráfego em uma rede virtual específica:** Todas as VMs na mesma rede virtual do Azure podem se comunicar entre si.

-   **Permitir o balanceamento de carga do Azure para entrada:**  essa regra permite o tráfego de qualquer endereço de origem para qualquer endereço de destino para o balanceador de carga do Azure.

-   **Negar tudo de entrada:**  essa regra bloqueia todo o tráfego de origem da Internet que você permitiu explicitamente.

-   **Permitir todo o tráfego de saída para a Internet:** Essa regra permite que as VMs iniciem conexões com a Internet. Se não quiser que essas conexões sejam iniciadas, você precisará criar uma regra para bloquear essas conexões ou impor o túnel forçado.

#### <a name="system-routes-and-user-defined-routes"></a>Rotas do sistema e rotas definidas pelo usuário

Ao adicionar máquinas virtuais (VMs) a uma rede virtual (VNet) no Azure, você percebe que as VMs podem se comunicar entre si pela rede, automaticamente. Não é necessário especificar um gateway, apesar das VM estarem em sub-redes diferentes.

O mesmo se verifica para a comunicação entre as VM e a Internet pública, e até mesmo na sua rede no local quando está presente uma ligação híbrida a partir do Azure para o seu centro de dados.

![Rotas de sistema](./media/network-security/azure-network-security-fig-9.png)

Este fluxo de comunicação é possível porque o Azure utiliza uma série de rotas de sistema para definir como flui o tráfego IP. As rotas de sistema controlam o fluxo de comunicação nos seguintes cenários:

-   A partir da mesma sub-rede.

-   A partir de uma sub-rede para outra numa VNet.

-   A partir das VM para a Internet.

-   A partir de uma VNet para outra VNet através de um gateway VPN.

-   De uma VNet para outra VNet por meio de emparelhamento VNet ([encadeamento de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   A partir de um VNet para a sua rede no local através de um gateway VPN.

Muitas empresas têm requisitos estritos de segurança e conformidade que exigem a inspeção local de todos os pacotes de rede para impor políticas específicas. O Azure fornece um mecanismo chamado [túnel forçado](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) que roteia o tráfego das VMs para o local criando uma rota personalizada ou por anúncios de [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) por meio do ExpressRoute ou da VPN.

O túnel forçado no Azure está configurado por meio de rotas definidas pelo utilizador da rede virtual (UDR). Redirecionar o tráfego para um site no local é expressa como uma rota predefinida para o gateway de VPN do Azure.

A secção seguinte mostra a limitação atual da tabela de encaminhamento e rotas de uma rede Virtual do Azure:

- Cada sub-rede da rede virtual tem uma tabela de roteamento interno, do sistema. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:

  -  **Rotas de VNet locais:** Diretamente para as VMs de destino na mesma rede virtual

  - **Rotas locais:** Para o gateway de VPN do Azure

  -  **Rota padrão:** Diretamente à Internet. Os pacotes destinados aos endereços IP privados não cobertos pelas duas rotas anteriores são removidos.

- Com o lançamento de rotas definidas pelo usuário, você pode criar uma tabela de roteamento para adicionar uma rota padrão e, em seguida, associar a tabela de roteamento à sub-rede da VNet para habilitar o túnel forçado nessas sub-redes.

- Tem de definir um "site predefinido" entre os sites locais em vários locais ligado à rede virtual.

- O túnel forçado tem de ser associado a uma VNet com um gateway de VPN encaminhamento dinâmico (não um gateway estático).

- Túnel forçado do ExpressRoute não está configurado por intermédio deste mecanismo, mas em vez disso, está ativado por uma rota predefinida por meio de sessões de peering de BGP de ExpressRoute de publicidade.

> [!Note]
> Para obter mais informações, consulte a [documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para obter mais informações.

#### <a name="network-security-appliances"></a>Dispositivos de segurança de rede
Embora os grupos de segurança de rede e as rotas definidas pelo usuário possam fornecer uma determinada medida de segurança de rede nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), haverá situações em que você deseja ou precisa habilitar a segurança em níveis mais altos do pilha de rede. Em tais situações, recomendamos que você implante dispositivos de segurança de rede virtual fornecidos pelos parceiros do Azure.

![Dispositivos de segurança de rede](./media/network-security/azure-network-security-fig-10.png)

Os dispositivos de segurança de rede do Azure melhoram as funções de rede e segurança de VNet e estão disponíveis de vários fornecedores por meio do [Azure Marketplace](https://azuremarketplace.microsoft.com). Esses dispositivos de segurança virtual podem ser implantados para fornecer:

-   Firewalls altamente disponíveis

-   Prevenção de intrusão

-   Detecção de intrusão

-   WAFs (firewalls do aplicativo Web)

-   Otimização de WAN

-   Encaminhamento

-   Balanceamento de carga

-   VPN

-   Gestão de certificados

-   Active Directory

-   Autenticação multifator

#### <a name="application-gateway"></a>Gateway de aplicação

[Microsoft Azure gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) é uma solução de virtualização dedicada que fornece um ADC (controlador de entrega de aplicativo) como um serviço.

 ![Gateway de Aplicação](./media/network-security/azure-network-security-fig-11.png)

O gateway de aplicativo permite otimizar o desempenho e a disponibilidade do web farm descarregando a terminação SSL com uso intensivo de CPU para o gateway de aplicativo (descarregamento de SSL). Ele também fornece outros recursos de roteamento de camada 7, incluindo:

-   Distribuição Round Robin do tráfego de entrada

-   Afinidade de sessão baseada em cookie

-   Roteamento baseado em caminho de URL

-   Capacidade de hospedar vários sites por trás de um único gateway de aplicativo


Um [WAF (firewall do aplicativo Web)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) também é fornecido como parte do gateway de aplicativo. Isso fornece proteção aos aplicativos Web contra explorações e vulnerabilidades comuns da Web. O gateway de aplicativo pode ser configurado como um gateway voltado para a Internet, um gateway somente interno ou uma combinação de ambos.

O WAF do gateway de aplicativo pode ser executado no modo de detecção ou prevenção. Um caso de uso comum é para os administradores executarem no modo de detecção para observar o tráfego de padrões mal-intencionados. Após a detecção de possíveis explorações, a ativação do modo de prevenção bloqueia o tráfego de entrada suspeito.

 ![Gateway de Aplicação](./media/network-security/azure-network-security-fig-12.png)

Além disso, o WAF do gateway de aplicativo ajuda a monitorar aplicativos Web contra ataques usando um log WAF em tempo real que é integrado com o [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) e a [central de segurança do Azure](https://azure.microsoft.com/services/security-center/) para controlar alertas de WAF e monitorar facilmente as tendências.

O log formatado em JSON vai diretamente para a conta de armazenamento do cliente. Você tem controle total sobre esses logs e pode aplicar suas próprias políticas de retenção.

Você também pode ingerir esses logs em seu próprio sistema de análise usando a [integração de log do Azure](https://aka.ms/AzLog). Os logs do WAF também são integrados a [logs de Azure monitor](/azure/log-analytics/log-analytics-overview) para que você possa usar os logs de Azure monitor para executar consultas refinadas sofisticadas.

#### <a name="azure-web-application-firewall-waf"></a>Firewall do aplicativo Web do Azure (WAF)

Os aplicativos Web são cada vez mais alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns, como injeção de SQL, ataques de script entre sites e outros ataques que aparecem nos [10 principais OWASP](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). A prevenção de tais explorações no aplicativo requer manutenção rigorosa, aplicação de patches e monitoramento em várias camadas da topologia do aplicativo.

 ![Firewall do aplicativo Web do Azure (WAF)](./media/network-security/azure-network-security-fig-13.png)

Um [WAF (firewall de aplicativo Web)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) centralizado pode proteger contra ataques da Web e simplifica o gerenciamento de segurança sem a necessidade de qualquer alteração no aplicativo.

Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

#### <a name="network-availability-controls"></a>Controles de disponibilidade de rede

Existem diferentes opções para distribuir o tráfego de rede com o Microsoft Azure. Estas opções funcionam de forma diferente entre si, tendo um conjunto de funcionalidades diferente e suportando diferentes cenários. Podem todos ser utilizados em isolamento ou em conjunto.

A seguir estão os controles de disponibilidade de rede:

-   Balanceador de Carga do Azure

-   Gateway de Aplicação

-   Gestor de Tráfego

**Azure Load Balancer**

Fornece alta disponibilidade e desempenho de rede para seus aplicativos. É um balanceador de carga de camada 4 (TCP, UDP) que distribui o tráfego de entrada entre instâncias íntegras de serviços definidos em um conjunto de balanceamento de carga.

 ![Balanceador de Carga do Azure](./media/network-security/azure-network-security-fig-14.png)


Azure Load Balancer pode ser configurado para:

-   Balancear a carga do tráfego de entrada da Internet para máquinas virtuais. Essa configuração é conhecida como [balanceamento de carga voltado para a Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Balancear a carga do tráfego entre máquinas virtuais em uma rede virtual, entre máquinas virtuais em serviços de nuvem ou entre computadores locais e máquinas virtuais em uma rede virtual entre locais. Essa configuração é conhecida como [balanceamento de carga interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Encaminhe o tráfego externo para uma máquina virtual específica.

Todos os recursos na nuvem precisam de um endereço IP público para serem acessíveis pela Internet. A infraestrutura de nuvem no Azure usa endereços IP não roteáveis para seus recursos. O Azure usa NAT (conversão de endereços de rede) com endereços IP públicos para se comunicar com a Internet.

 **Gateway de aplicativo**

 O gateway de aplicativo funciona na camada de aplicativo (camada 7 na pilha de referência de rede OSI). Atua como um serviço de proxy de reversão, terminando a ligação de cliente e reencaminhando pedidos de pontos finais de back-end.

 **Gerenciador de tráfego**

Gerenciador de Tráfego do Microsoft Azure permite que você controle a distribuição do tráfego do usuário para pontos de extremidade de serviço em diferentes data centers. Os pontos de extremidade de serviço com suporte do Gerenciador de tráfego incluem VMs do Azure, aplicativos Web e serviços de nuvem. Também pode utilizar o Gestor de Tráfego com pontos finais externos, não pertencentes ao Azure.

O Gerenciador de tráfego usa o DNS (sistema de nomes de domínio) para direcionar solicitações de cliente para o ponto de extremidade mais apropriado com base em um [método de roteamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) e a integridade dos pontos. O Gerenciador de tráfego fornece uma variedade de métodos de roteamento de tráfego para atender a necessidades de aplicativos diferentes, [monitoramento](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)de integridade do ponto de extremidade e failover automático. O Gestor de Tráfego é resiliente a falhas, incluindo a falhas numa região do Azure inteira.

O Gerenciador de tráfego do Azure permite controlar a distribuição de tráfego entre os pontos de extremidade do aplicativo. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure.

O Gerenciador de tráfego fornece dois benefícios principais:

-   Distribuição de tráfego de acordo com um dos vários [métodos de roteamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Monitoramento contínuo da integridade do ponto de extremidade](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) e failover automático quando os pontos de extremidade falham.

Quando um cliente tenta se conectar a um serviço, ele deve primeiro resolver o nome DNS do serviço para um endereço IP. Em seguida, o cliente se conecta a esse endereço IP para acessar o serviço. O Gerenciador de tráfego usa o DNS para direcionar clientes para pontos de extremidade de serviço específicos com base nas regras do método de roteamento de tráfego. Os clientes se conectam diretamente ao ponto de extremidade selecionado. O Gerenciador de tráfego não é um proxy ou um gateway. O Gerenciador de tráfego não vê o tráfego que passa entre o cliente e o serviço.

### <a name="azure-network-validation"></a>Validação de rede do Azure

A validação de rede do Azure é para garantir que a rede do Azure esteja funcionando conforme estiver configurada e a validação possa ser feita usando os serviços e recursos disponíveis para monitorar a rede. Com o observador de rede do Azure, você pode acessar uma grande quantidade de recursos de log e diagnóstico que permitem que você entenda o desempenho e a integridade da rede. Esses recursos podem ser acessados por meio do portal, do Power Shell, da CLI, da API REST e do SDK.

A segurança operacional do Azure refere-se aos serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros ativos no Microsoft Azure. A segurança operacional do Azure baseia-se em uma estrutura que incorpora o conhecimento obtido por meio de vários recursos que são exclusivos à Microsoft, incluindo o SDL (ciclo de vida do desenvolvimento de segurança da Microsoft), o programa Microsoft Security Response Centre e conscientização profunda do panorama de ameaças à segurança cibernético.

-   [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Gerenciador de recursos do Azure

As pessoas e os processos que operam Microsoft Azure talvez sejam o recurso de segurança mais importante da plataforma. Esta seção descreve os recursos da infraestrutura global de datacenter da Microsoft que ajudam a aprimorar e manter a segurança, a continuidade e a privacidade.

Normalmente, a infraestrutura da sua aplicação é composta por vários componentes, como uma máquina virtual, uma conta de armazenamento e uma rede virtual, ou uma aplicação Web, uma base de dados, um servidor de base de dados e serviços de terceiros. Não vê estes componentes como entidades separadas. Em vez disso, vê-os como partes relacionadas e interdependentes de uma única entidade. Deve implementá-los, geri-los e monitorizá-los como um grupo. O Azure Resource Manager permite trabalhar com os recursos na sua solução como um grupo.

Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Utiliza um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, como de teste e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

**Os benefícios do uso do Resource Manager**

O Resource Manager oferece várias vantagens:

-   Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

-   Pode implementar repetidamente a solução durante todo o ciclo de vida de desenvolvimento e ter a confiança de que os recursos são implementados num estado consistente.

-   Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

-   Você pode definir as dependências entre os recursos, para que eles sejam implantados na ordem correta.

-   Pode aplicar o controlo de acesso a todos os serviços no seu grupo de recursos porque o Controlo de Acesso Baseado em Funções (RBAC) está integrado de forma nativa na plataforma de gestão.

-   Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.

-   Você pode esclarecer a cobrança da sua organização exibindo os custos de um grupo de recursos de compartilhamento de marca.

> [!Note]
> O Resource Manager proporciona uma nova forma de implementar e gerir as suas soluções. Se utilizou o modelo de implementação anterior e pretende obter informações sobre as alterações, consulte [Compreender a implementação do Resource Manager e a implementação clássica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Monitoramento e registro em log de rede do Azure

O Azure oferece muitas ferramentas para monitorar, prevenir, detectar e responder a eventos de segurança de rede. Algumas das ferramentas mais poderosas disponíveis nessa área incluem:

-   Observador de Rede

-   Monitoramento no nível de recurso de rede

-   Registos do Azure Monitor

### <a name="network-watcher"></a>Observador de rede

[Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – o monitoramento baseado em cenário é fornecido com os recursos no observador de rede. Esse serviço inclui captura de pacote, próximo salto, verificação de fluxo de IP, exibição de grupo de segurança, logs de fluxo de NSG. O monitoramento em nível de cenário fornece uma exibição de ponta a ponta dos recursos de rede em contraste com o monitoramento de recursos de rede individual.

 ![Observador de Rede](./media/network-security/azure-network-security-fig-15.png)

O observador de rede é um serviço regional que permite monitorar e diagnosticar condições em um nível de cenário de rede em, para e do Azure. As ferramentas de diagnóstico e visualização de rede disponíveis com o observador de rede ajudam você a entender, diagnosticar e obter informações sobre sua rede no Azure.

O observador de rede atualmente tem os seguintes recursos:

#### <a name="topology"></a>Topologia

A [topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) retorna um grafo de recursos de rede em uma rede virtual. O grafo ilustra a interconexão entre os recursos para representar a conectividade de rede de ponta a ponta. No portal, a topologia retorna os objetos de recurso de acordo com cada rede virtual. As relações são representadas por linhas entre os recursos fora da região do observador de rede, mesmo se o grupo de recursos não for exibido. Os recursos retornados na exibição do portal são um subconjunto dos componentes de rede que estão grafos. Para ver a lista completa de recursos de rede, você pode usar o [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) ou [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

À medida que os recursos são retornados, a conexão entre eles é modelada sob duas relações.

- **Contenção** -a rede virtual contém uma sub-rede, que contém uma NIC.

- **Associado** -uma NIC está associada a uma VM.

#### <a name="variable-packet-capture"></a>Captura de pacote variável

A captura de [pacotes da variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) do observador de rede permite que você crie sessões de captura de pacote para controlar o tráfego de e para uma máquina virtual. A captura de pacotes ajuda a diagnosticar anomalias de rede reativas e de proatividade. Outros usos incluem a coleta de estatísticas de rede, a obtenção de informações sobre invasões de rede, a depuração de comunicações cliente-servidor e muito mais.

A captura de pacote é uma extensão de máquina virtual que é iniciada remotamente por meio do observador de rede. Esse recurso facilita a carga de executar uma captura de pacote manualmente na máquina virtual desejada, o que economiza tempo valioso. A captura de pacotes pode ser disparada por meio do portal, do PowerShell, da CLI ou da API REST. Um exemplo de como a captura de pacotes pode ser disparado é com alertas de máquina virtual.

#### <a name="ip-flow-verify"></a>Verificação do fluxo de IP

Os [fluxos de IP verificam](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) se um pacote é permitido ou negado para ou de uma máquina virtual com base em informações de 5 tuplas. Essas informações consistem em direção, protocolo, IP local, IP remoto, porta local e porta remota. Se o pacote for negado por um grupo de segurança, o nome da regra que negou o pacote será retornado. Embora qualquer IP de origem ou de destino possa ser escolhido, esse recurso ajuda os administradores a diagnosticar rapidamente problemas de conectividade de ou para a Internet e de ou para o ambiente local.

A verificação de fluxos de IP visa uma interface de rede de uma máquina virtual. O fluxo de tráfego é verificado com base nas configurações definidas de ou para essa interface de rede. Esse recurso é útil para confirmar se uma regra em um grupo de segurança de rede está bloqueando o tráfego de entrada ou saída de ou para uma máquina virtual.

#### <a name="next-hop"></a>Próximo salto

Determina o [próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) para os pacotes que estão sendo roteados na malha de rede do Azure, permitindo que você diagnostique quaisquer rotas definidas pelo usuário mal configuradas. O tráfego de uma VM é enviado para um destino com base nas rotas efetivas associadas a uma NIC. O próximo salto Obtém o tipo do próximo salto e o endereço IP de um pacote de uma máquina virtual específica e NIC. Isso ajuda a determinar se o pacote está sendo direcionado para o destino ou se o tráfego está em buraco negro.

O próximo salto também retorna a tabela de rotas associada ao próximo salto. Ao consultar um próximo salto se a rota for definida como uma rota definida pelo usuário, essa rota será retornada. Caso contrário, o próximo salto retornará "rota do sistema".

#### <a name="security-group-view"></a>Vista do grupo de segurança

Obtém as regras de segurança efetivas e aplicadas que são aplicadas em uma VM. Os grupos de segurança de rede são associados no nível de sub-rede ou em um nível de NIC. Quando associado a um nível de sub-rede, ele se aplica a todas as instâncias de VM na sub-rede. A [exibição do grupo de segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) de rede retorna todos os NSGs e regras configurados que estão associados em um nível de NIC e de sub-rede para uma máquina virtual que fornece informações sobre a configuração. Além disso, as regras de segurança em vigor são retornadas para cada uma das NICs em uma VM. Usando a exibição grupo de segurança de rede, você pode avaliar uma VM para vulnerabilidades de rede, como portas abertas. Você também pode validar se o grupo de segurança de rede está funcionando conforme o esperado com base em uma [comparação entre as regras de segurança configuradas e efetivas](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Log de fluxo do NSG

 Os logs de fluxo para grupos de segurança de rede permitem capturar logs relacionados ao tráfego permitido ou negado pelas regras de segurança no grupo. O fluxo é definido por informações de 5 tuplas – IP de origem, IP de destino, porta de origem, porta de destino e protocolo.

[Os logs de fluxo do grupo de segurança de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) são um recurso do observador de rede que permite exibir informações sobre o tráfego IP de entrada e saída por meio de um grupo de segurança de rede.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Solução de problemas de gateway de rede virtual e conexão

O observador de rede fornece muitos recursos que se relacionam com a compreensão de seus recursos de rede no Azure. Um desses recursos é a solução de problemas de recursos. A [solução de problemas de recursos](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) pode ser chamada pelo PowerShell, pela CLI ou pela API REST. Quando chamado, o observador de rede inspeciona a integridade de um gateway de rede virtual ou uma conexão e retorna suas descobertas.

Esta seção o conduz pelas diferentes tarefas de gerenciamento que estão disponíveis atualmente para solução de problemas de recursos.

-   [Solucionar problemas de um gateway de rede virtual](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Solucionar problemas de conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Limites de assinatura de rede

[Os limites de assinatura de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornecem detalhes do uso de cada recurso de rede em uma assinatura em uma região em relação ao número máximo de recursos disponíveis.

#### <a name="configuring-diagnostics-log"></a>Configurando o log de diagnóstico

O observador de rede fornece uma exibição de [logs de diagnóstico](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) . Esta exibição contém todos os recursos de rede que dão suporte ao log de diagnóstico. Nessa exibição, você pode habilitar e desabilitar os recursos de rede de forma conveniente e rápida.

### <a name="network-resource-level-monitoring"></a>Monitoramento no nível de recurso de rede

Os seguintes recursos estão disponíveis para o monitoramento no nível do recurso:

#### <a name="audit-log"></a>Registo de auditoria

As operações executadas como parte da configuração de redes são registradas em log. Esses logs de auditoria são essenciais para estabelecer várias conformidades. Esses logs podem ser exibidos no portal do Azure ou recuperados usando ferramentas da Microsoft, como Power BI ou ferramentas de terceiros. Os logs de auditoria estão disponíveis por meio do portal, do PowerShell, da CLI e da API REST.

> [!Note]
> Para obter mais informações sobre logs de auditoria, consulte [operações de auditoria com o Gerenciador de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Os logs de auditoria estão disponíveis para operações realizadas em todos os recursos de rede.


#### <a name="metrics"></a>Métricas

Métricas são medidas de desempenho e contadores coletados em um período. As métricas estão disponíveis no momento para o gateway de aplicativo. Métricas podem ser usadas para disparar alertas com base no limite. O gateway de Aplicativo Azure, por padrão, monitora a integridade de todos os recursos em seu pool de back-ends e remove automaticamente qualquer recurso considerado não íntegro do pool. O gateway de aplicativo continua monitorando as instâncias não íntegras e as adiciona de volta ao pool de back-end íntegro quando elas ficam disponíveis e respondem a investigações de integridade. O gateway de aplicativo envia as investigações de integridade com a mesma porta que é definida nas configurações de HTTP de back-end. Essa configuração garante que a investigação esteja testando a mesma porta que os clientes usam para se conectar ao back-end.

> [!Note]
> Consulte [diagnóstico do gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) para exibir como as métricas podem ser usadas para criar alertas.

#### <a name="diagnostic-logs"></a>Registos de diagnósticos

Eventos periódicos e ESPONTANEOS são criados por recursos de rede e registrados em contas de armazenamento, enviados a um hub de eventos ou logs de Azure Monitor. Esses logs fornecem informações sobre a integridade de um recurso. Esses logs podem ser exibidos em ferramentas como Power BI e Azure Monitor logs. Para saber como exibir os logs de diagnóstico, visite [Azure monitor logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Os logs de diagnóstico estão disponíveis para [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), rotas e gateway de [aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

O observador de rede fornece uma exibição de logs de diagnóstico. Esta exibição contém todos os recursos de rede que dão suporte ao log de diagnóstico. Nessa exibição, você pode habilitar e desabilitar os recursos de rede de forma conveniente e rápida.

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) são um serviço no Azure que monitora seus ambientes locais e de nuvem para manter a disponibilidade e o desempenho. Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens.

Os logs de Azure Monitor oferecem as seguintes soluções para monitorar suas redes:

-   Monitor de Desempenho de Rede (NPM)

-   Análise de gateway de Aplicativo Azure

-   Análise do grupo de segurança de rede do Azure

#### <a name="network-performance-monitor-npm"></a>Monitor de desempenho de rede (NPM)
A solução de gerenciamento de [Monitor de desempenho de rede](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) é uma solução de monitoramento de rede que monitora a integridade, a disponibilidade e a acessibilidade das redes.

Ele é usado para monitorar a conectividade entre:

-   nuvem pública e local

-   data centers e locais de usuários (filiais)

-   sub-redes que hospedam várias camadas de um aplicativo de várias camadas.


#### <a name="azure-application-gateway-analytics-in-azure-monitor-logs"></a>Análise de gateway de aplicativo do Azure em logs de Azure Monitor

Os seguintes logs têm suporte para gateways de aplicativo:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

As métricas a seguir têm suporte para gateways de aplicativo:

-   taxa de transferência de 5 minutos

#### <a name="azure-network-security-group-analytics-in-azure-monitor-logs"></a>Análise de grupo de segurança de rede do Azure em logs de Azure Monitor

Os seguintes logs têm suporte para [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** Contém entradas para as quais as regras de NSG são aplicadas às VMs e funções de instância com base no endereço MAC. O status dessas regras é coletado a cada 60 segundos.

- **NetworkSecurityGroupRuleCounter:** Contém entradas para quantas vezes cada regra NSG é aplicada para negar ou permitir o tráfego.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre segurança lendo alguns dos nossos tópicos de segurança aprofundados:

-   [Logs de Azure Monitor para NSGs (grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Inovações de rede que conduzem a interrupção da nuvem](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: O software de comutador de rede que alimenta a nuvem global da Microsoft](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Como a Microsoft cria sua rede global rápida e confiável](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Iluminação de inovação de rede](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
