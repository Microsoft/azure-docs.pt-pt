---
title: Segurança de rede do Azure | Documentos da Microsoft
description: Saiba mais sobre serviços informáticos com base na cloud que incluem uma ampla seleção de instâncias de computação e serviços que podem aumenta e reduz automaticamente para satisfazer as necessidades do seu aplicativo ou a empresa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f684a9d7bca77a8aa3aa60f5079dda0ce3b58a1c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121592"
---
# <a name="azure-network-security"></a>Segurança de rede do Azure

Sabemos que a segurança é terreno na cloud e como é importante que encontrará informações precisas e atualizadas sobre a segurança do Azure. Uma das razões para utilizar o Azure para as suas aplicações e serviços melhores é tirar partido infinidade do Azure de ferramentas de segurança e os recursos. Essas ferramentas e recursos ajudam a que seja possível criar soluções seguras na plataforma do Azure.

Microsoft Azure fornece confidencialidade, integridade e disponibilidade dos dados dos clientes, ao ativar também a responsabilidade transparente. Para ajudar a compreender melhor a coleção de controles de segurança de rede implementado no Microsoft Azure, da perspectiva do cliente, neste artigo, "Segurança de rede do Azure", é escrito para fornecer uma visão abrangente da rede controlos de segurança disponível com o Microsoft Azure.

Este documento destina-se para o informar sobre a vasta gama de controlos de rede que pode configurar para melhorar a segurança das soluções que implementa no Azure. Se estiver interessado em que a Microsoft faz para proteger os recursos de infraestrutura de rede da própria plataforma do Azure, consulte a secção de segurança do Azure no [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure).

## <a name="azure-platform"></a>Plataforma Azure

O Azure é uma plataforma de serviço de nuvem pública que suporta uma seleção ampla de sistemas operativos, linguagens de programação, arquiteturas, ferramentas, bases de dados e dispositivos.  Ele pode executar contentores do Linux com a integração de Docker; Crie aplicações com JavaScript, Python, .NET, PHP, Java e node. js; Crie back-ends para dispositivos iOS, Android e Windows dispositivos. Serviços cloud do Azure suportam as tecnologias em que milhões de desenvolvedores e profissionais de TI confiam e confiam.

Quando criar ou migrar ativos de TI para, um fornecedor de serviços de cloud pública, está a depender das capacidades dessa organização para proteger as suas aplicações e dados com os serviços e os controles fornecem para gerir a segurança dos seus ativos baseados na nuvem.

Infraestrutura do Azure foi concebida a partir do recurso para aplicações para alojar milhões de clientes em simultâneo, e fornece uma Fundação fidedigna na qual as empresas podem satisfazer os seus requisitos de segurança. Além disso, Azure fornece uma coleção abrangente de opções de segurança configuráveis e a capacidade para controlá-las para que possa personalizar a segurança para satisfazer os requisitos únicos das implementações da sua organização.

## <a name="abstract"></a>Abstrato

Serviços cloud pública da Microsoft oferecem serviços de hiper escala e infraestrutura, capacidades de nível empresarial e muitas opções de conectividade híbrida. Pode optar por aceder a estes serviços através da Internet ou com o Azure ExpressRoute, que fornece conectividade de rede privada. A plataforma Microsoft Azure permite-lhe expandir a sua infraestrutura para a cloud e crie arquiteturas de várias camadas de forma totalmente integrada. Além disso, terceiros pode ativar recursos aprimorados, oferecendo serviços de segurança e de aplicações virtuais.

Os serviços de rede do Azure maximizar a flexibilidade, disponibilidade, resiliência, segurança e integridade por predefinição. Este white paper fornece detalhes sobre as funções de sistema de rede do Azure e obter informações sobre como os clientes podem utilizar funcionalidades de segurança nativa do Azure para ajudar a proteger seus ativos de informações.

Os públicos-alvo previstos deste White Paper incluem:

- Gestores de técnicos, os administradores de rede e os desenvolvedores que estiver à procura de soluções de segurança disponíveis e suportadas no Azure.

-   Os SMEs ou os executivos de processo de negócios que desejam obter uma visão geral, as tecnologias de rede do Azure e os serviços que são relevantes em discussões sobre segurança de rede na cloud pública do Azure.

## <a name="azure-networking-big-picture"></a>Visão geral de redes do Azure
Microsoft Azure inclui uma infraestrutura de rede robusta para suportar a sua aplicação e os requisitos de conectividade do serviço. Conectividade de rede é possível entre recursos localizados no Azure, entre no local e recursos, alojados no Azure e de e para a Internet e o Azure.

![Visão geral de redes do Azure](media/azure-network-security/azure-network-security-fig-1.png)

O [infraestrutura de rede do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) permite-lhe ligar em segurança a recursos do Azure entre si com as redes virtuais (VNets). Uma VNet é uma representação da sua própria rede na cloud. Uma VNet é um isolamento lógico da rede na cloud do Azure dedicado à sua subscrição. Pode ligar VNets para as suas redes no local.

O Azure suporta dedicado a conectividade da ligação WAN para sua rede no local e uma rede Virtual do Azure com [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). A ligação entre o Azure e o seu site utiliza uma ligação dedicada que não passa pela Internet pública. Se a aplicação do Azure está em execução em vários datacenters, pode utilizar [Gestor de tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para encaminhar os pedidos dos utilizadores de forma inteligente entre instâncias da aplicação. Também pode encaminhar o tráfego para serviços não está em execução no Azure se podem ser acedidos a partir da Internet.

## <a name="enterprise-view-of-azure-networking-components"></a>Vista de Enterprise dos componentes de rede do Azure
O Azure tem muitos componentes de rede que são relevantes para discussões sobre segurança de rede. Podemos descrever esses componentes de rede e concentrar-se sobre os problemas de segurança relacionados aos mesmos.

> [!Note]
> Nem todos os aspetos do funcionamento em rede do Azure são descritos – vamos abordar apenas os considera pivotal planear e estruturar uma infraestrutura de rede segura em torno de seus serviços e aplicações que implementar no Azure.

Neste artigo, será abrangem o seguinte Azure enterprise capacidades de rede:

-   Conectividade de rede básica

-   Conectividade híbrida

-   Controlos de Segurança

-   Validação de rede

### <a name="basic-network-connectivity"></a>Conectividade de rede básica

O [rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) serviço permite-lhe ligar em segurança a recursos do Azure entre si com as redes virtuais (VNet). Uma VNet é uma representação da sua própria rede na cloud. Uma VNet é um isolamento lógico da infra-estrutura de rede do Azure dedicado à sua subscrição. Também pode ligar VNets entre si e para as suas redes no local com VPNs de site a site e dedicado [WAN links](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Conectividade de rede básica](media/azure-network-security/azure-network-security-fig-2.png)

Com a compreensão que use VMs para servidores de anfitrião no Azure, a pergunta é como essas VMs ligam a uma rede. A resposta é que as VMs se ligam para um [rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Redes virtuais do Azure são como virtual redes utilizam no local com suas próprias soluções de plataforma de virtualização, como o Microsoft Hyper-V ou VMware.

#### <a name="intra-vnet-connectivity"></a>Conectividade de intra-VNet

Pode ligar VNets entre si, permitindo que os recursos ligados a das Vnets para comunicar entre si em VNets. Pode utilizar uma ou ambas das seguintes opções para ligar VNets entre si:

- **Peering:** Permite que os recursos ligados a VNets do Azure diferente na mesma localização do Azure para comunicar entre si. A largura de banda e a latência entre a VNet é o mesmo como se os recursos foram ligados à mesma VNet. Para saber mais sobre o peering, leia [peering de rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

  ![Peering](media/azure-network-security/azure-network-security-fig-3.png)

- **Ligação de VNet a VNet:** Permite que os recursos ligados à VNet do Azure diferente dentro das mesmo ou em diferentes localizações do Azure. Ao contrário de peering, largura de banda é limitada entre VNets, porque o tráfego tem de fluir através de um Gateway de VPN do Azure.

![Ligação de VNet a VNet](media/azure-network-security/azure-network-security-fig-4.png)


Para saber mais sobre como ligar VNets com uma ligação VNet a VNet, veja a [configurar um artigo de ligação de VNet a VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Capacidades de rede virtual do Azure:

Como pode ver, uma rede Virtual do Azure fornece máquinas virtuais para ligar à rede para que se podem ligar a outros recursos de rede de forma segura. No entanto, a conectividade básica é apenas o começo. As seguintes capacidades do serviço de rede Virtual do Azure expõem as características de segurança de rede Virtual do Azure:

-   Isolamento

-   Conectividade Internet

-   Conectividade de recursos do Azure

-   Conectividade VNet

-   Conectividade no local

-   Filtragem do tráfego

-   Encaminhamento

**Isolamento**

As VNets estiverem [isolado](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) uns dos outros. Pode criar VNets separadas para desenvolvimento, teste e produção que utilizam o mesmo [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) blocos de endereços. Por outro lado, pode criar várias VNets que usam diferentes blocos de endereços CIDR e ligar a redes em conjunto. Pode segmentar uma VNet em várias sub-redes.

O Azure oferece resolução de nomes internos para VMs e [serviços Cloud](https://azure.microsoft.com/services/cloud-services/) instâncias de função ligada a uma VNet. Opcionalmente, pode configurar uma VNet para utilizar os seus próprios servidores DNS, em vez de utilizar a resolução do nome interno do Azure.

Pode implementar várias VNets dentro de cada [subscrição](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) e do Azure [região](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json). Cada VNet é isolada de outras VNets. Para cada VNet, pode:

-   Especificar um espaço de endereços IP privado personalizado, utilizando endereços públicos e privados (RFC 1918). O Azure atribui recursos ligados à VNet um endereço IP privado do espaço de endereço, atribuir.

-   Segmentar a VNet num ou mais sub-redes e atribuir uma parte do espaço de endereços da VNet, a cada sub-rede.

-   Utilizar a resolução de nomes fornecida pelo Azure ou especificar o seu próprio servidor DNS para utilização por recursos ligados a uma VNet. Para saber mais sobre a resolução de nomes em VNets, leia os [resolução de nomes para VMs e serviços Cloud](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Ligação à Internet**

Todos os [máquinas virtuais (VM) do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/) e instâncias de função dos serviços Cloud ligadas a uma VNet têm acesso à Internet, por predefinição. Também pode ativar o acesso de entrada para recursos específicos, conforme necessário. (VM) e instâncias de função dos serviços Cloud ligadas a uma VNet têm acesso à Internet, por predefinição. Também pode ativar o acesso de entrada para recursos específicos, conforme necessário.

Por predefinição, todos os recursos ligados a uma VNet tem conectividade de saída à Internet. O endereço IP privado do recurso é a rede endereço de origem traduzido (SNAT) a um endereço IP público pela infraestrutura do Azure. Pode alterar a conectividade de predefinição através da implementação de filtragem de tráfego e encaminhamento personalizado. Para saber mais sobre a conectividade de Internet de saída, veja a [compreender as ligações de saída no Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para comunicar internamente com recursos do Azure da Internet ou comunicações de saída à Internet sem o SNAT, um recurso tem de atribuir um endereço IP público. Para saber mais sobre endereços IP públicos, leia os [endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Conectividade de recursos do Azure**

[Recursos do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) como VMs e serviços Cloud podem ser ligadas à mesma VNet. Os recursos podem ligar-se entre si utilizando endereços IP privados, mesmo que estejam em sub-redes diferentes. O Azure fornece encaminhamento predefinido entre sub-redes, VNets e redes no local, para que não tenha de configurar e gerir rotas.

Pode ligar a vários recursos do Azure a uma VNet, como máquinas virtuais (VM), serviços Cloud, ambientes de serviço de aplicações e os conjuntos de dimensionamento de máquinas virtuais. As VMs se ligam a uma sub-rede numa VNet através de uma interface de rede (NIC). Para saber mais sobre NICs, leia os [interfaces de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Conectividade VNet**

[VNets](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) podem ser ligadas entre si, permitindo que os recursos ligados a qualquer VNet para comunicar com qualquer recurso em qualquer outra VNet.

Pode ligar VNets entre si, permitindo que os recursos ligados a das Vnets para comunicar entre si em VNets. Pode utilizar uma ou ambas das seguintes opções para ligar VNets entre si:

- **Peering:** Permite que os recursos ligados a VNets do Azure diferente na mesma localização do Azure para comunicar entre si. A largura de banda e a latência entre as VNets é o mesmo como se os recursos foram ligados para o mesmo VNet.To Saiba mais sobre o peering, leia os [peering de rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **Ligação de VNet a VNet:** Permite que os recursos ligados à VNet do Azure diferente dentro das mesmo ou em diferentes localizações do Azure. Ao contrário de peering, largura de banda é limitada entre VNets, porque o tráfego tem de fluir através de um Gateway de VPN do Azure. Para obter mais informações sobre como ligar VNets com uma ligação VNet a VNet. Para saber mais, leia os [configurar uma ligação VNet a VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Conectividade no local**

As VNets podem ser ligadas para [locais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) redes por meio de conexões de rede privada entre a rede e do Azure ou através de uma ligação de VPN de site a site através da Internet.

Pode ligar a sua rede no local a uma VNet com qualquer combinação das seguintes opções:

- **Ponto a site rede privada virtual (VPN):** Estabelecido entre um único PC ligado à sua rede e a VNet. Este tipo de ligação é excelente se estiver a começar a utilizar o Azure ou para os programadores, uma vez que necessita de pouca ou nenhumas alterações à sua rede existente. A ligação utiliza o protocolo SSTP para fornecer comunicações encriptadas através da Internet entre o PC e a VNet. A latência de uma VPN ponto a site é imprevisível, uma vez que o tráfego atravessa a Internet.

- **VPN de site a site:** Estabelecida entre o dispositivo VPN e um Gateway de VPN do Azure. Este tipo de ligação permite que qualquer recurso no local que está a autorizar para aceder a uma VNet. A ligação é uma VPN IPsec/IKE que fornece comunicações encriptadas através da Internet entre o dispositivo no local e o gateway de VPN do Azure. A latência de uma ligação site a site é imprevisível, uma vez que o tráfego atravessa a Internet.

- **O Azure ExpressRoute:** Estabelecida entre a rede e o Azure, através de um parceiro do ExpressRoute. Esta ligação é privada. Tráfego atravessa a Internet. A latência de uma ligação do ExpressRoute é previsível, uma vez que o tráfego não atravessa a Internet. Para saber mais sobre todas as opções de ligação anterior, leia os [diagramas de topologia de ligação](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtragem do tráfego**

Instâncias de função VM e serviços em nuvem [tráfego de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) possam ser filtrados entrada e saída por endereço IP de origem e porta, endereço IP de destino e porta e protocolo.

Pode filtrar o tráfego de rede entre as sub-redes com uma ou ambas das seguintes opções:

- **Grupos de segurança de rede (NSG):** Cada NSG pode conter várias regras de segurança de entrada e saída que permitem filtrar o tráfego por origem e endereço IP de destino, porta e protocolo. Pode aplicar um NSG a cada NIC numa VM. Também pode aplicar um NSG à sub-rede de uma NIC ou outros recursos do Azure, está ligado. Para saber mais sobre NSGs, leia os [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Aplicações de rede virtual:** Uma aplicação de rede virtual é uma VM a executar o software que executa uma função de rede, como uma firewall. Ver uma lista das NVAs disponíveis no Azure Marketplace. NVAs estão também disponíveis, que fornecem a otimização de WAN e a outra rede funções de tráfego. NVAs são normalmente utilizadas com definidas pelo utilizador ou rotas BGP. Também pode utilizar uma NVA para filtrar o tráfego entre VNets.

**Encaminhamento**

Opcionalmente, pode substituir predefinição do Azure encaminhamento através da configuração de suas próprias rotas ou rotas BGP através de um gateway de rede a utilizar.

O Azure cria as tabelas de rotas que permitem recursos ligados a qualquer sub-rede na VNet em qualquer para comunicar entre si, por predefinição. Pode implementar uma ou ambas das seguintes opções para substituir as rotas predefinidas que o Azure cria:

- **Rotas definidas pelo utilizador:** Pode criar tabelas de rotas personalizadas com rotas que controlam onde o tráfego é encaminhado para cada sub-rede. Para saber mais sobre as rotas definidas pelo utilizador, leia os [rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Rotas BGP:** Se ligar a VNet à sua rede no local através de um Gateway de VPN do Azure ou a ligação do ExpressRoute, pode propagar rotas BGP para as suas VNets.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Conectividade híbrida com a internet: Ligar a uma rede no local
Pode ligar a sua rede no local a uma VNet com qualquer combinação das seguintes opções:

-   Conectividade Internet

-   VPN Point-to-site (P2S VPN)

-   VPN site a Site (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Conectividade com a Internet

Como o nome sugere, ligação à Internet faz com que as cargas de trabalho acessível a partir da Internet, fazendo com que expor pontos finais públicos diferentes para cargas de trabalho que residem dentro da rede virtual. Estas cargas de trabalho podem estar expostas usando [Balanceador de carga com acesso à Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) ou simplesmente a atribuir um endereço IP público à VM. Dessa forma, se tornará possível para nada na Internet para ter acesso à máquina virtual, fornecida uma firewall de host [grupos de segurança (NSG) de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg), e [rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) permitir que isso acontece.

Neste cenário, poderia expor um aplicativo que precisa ser público à Internet e conseguir estabelecer ligação à mesma em qualquer lugar ou a partir de localizações específicas, dependendo da configuração das cargas de trabalho.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>VPN Point-to-Site ou VPN de Site a Site
Estes dois enquadra-se da mesma categoria. Precisam de sua VNet para ter um Gateway de VPN e pode ligá-la através de um um cliente VPN para a estação de trabalho como parte dos [configuraçãopontoaSiteda](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) ou pode configurar o ambiente [dispositivo VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)para poder encerrar uma VPN de site a site. Dessa forma, dispositivos no local podem ligar a recursos dentro da VNet.

Uma configuração Ponto a Site (P2S) permite-lhe criar uma ligação segura a partir de um computador cliente individual para a sua rede virtual. Uma P2S é uma ligação VPN através de SSTP (Secure Socket Tunneling Protocol).

![VPN ponto a Site](media/azure-network-security/azure-network-security-fig-5.png)

As ligações ponto a Site são úteis quando pretende ligar a VNet a partir de uma localização remota, como em casa ou de um centro de conferência ou quando tem apenas alguns clientes que precisam de ligar a uma rede virtual.

As ligações P2S não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. O utilizador estabelece a ligação VPN a partir do computador cliente. Por conseguinte, P2S não é recomendada a forma de ligar ao Azure, caso precise de uma ligação persistente de vários dispositivos no local e computadores à sua rede do Azure.

![VPN site a Site](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Para obter mais informações sobre ligações ponto a Site, consulte a [Point-to-Site FA v p](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Uma ligação de gateway de VPN de Site para Site é utilizada para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2).

Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Essa conexão ocorre através da Internet e permite-lhe informações de "encapsulamento" dentro de uma ligação encriptada entre a rede e do Azure. VPN site a site é uma tecnologia madura e segura que tenha sido implementada por empresas de todos os tamanhos há décadas. Encriptação de túnel é executada usando [modo de encapsulamento IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Embora VPN site a site é uma tecnologia estabelecida, fiável e fidedigna, o tráfego dentro do túnel atravessa a Internet. Além disso, a largura de banda relativamente é restrito a um máximo de cerca de 200 Mbps.

Se necessitar de um nível excepcional de segurança ou de desempenho para as suas ligações entre locais, recomendamos que utilize o Azure ExpressRoute para a conectividade em vários locais. O ExpressRoute é uma WAN dedicada ligação entre a sua localização no local ou um fornecedor de alojamento do Exchange. Como se trata de uma ligação de telecomunicações, seus dados não viajam através da Internet e, portanto, não são expostos a potenciais riscos inerentes em comunicações da Internet.

> [!Note]
> Para obter mais informações sobre gateways de VPN, consulte [gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Ligação WAN dedicada
O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para o Azure através de uma ligação privada dedicada facilitada por um fornecedor de conectividade.

As ligações do ExpressRoute não passam para a Internet pública. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.

![ Ligação WAN dedicada](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Para obter informações sobre como ligar a sua rede para a Microsoft através do ExpressRoute, veja [modelos de conectividade do ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) e [descrição geral técnica do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Como com as opções de VPN de site a site, ExpressRoute também permite-lhe ligar a recursos que não são necessariamente na VNet em modo apenas um. Na verdade, dependendo do SKU, pode ligar às 10 VNets. Se tiver o [suplemento premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), ligações para VNets até 100 são possíveis, dependendo da largura de banda. Para saber mais sobre o que esses tipos de vista de olhos de ligações, como, leia [diagramas de topologia de ligação](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Controlos de segurança
Uma rede Virtual do Azure fornece uma rede lógica e segura que está isolada das outras redes virtuais e suporte a vários controlos de segurança que utilizar nas suas redes no local. Aos clientes criam sua própria estrutura com: sub-redes — eles utilizar seu próprio intervalo de endereços IP privados, configurar tabelas de rotas, grupos de segurança de rede, acessar listas de controle (ACLs), gateways e aplicações virtuais para executar as cargas de trabalho na cloud.

Seguem-se controlos de segurança, que pode usar em suas redes virtuais do Azure:

-   Controlos de acesso de rede

-   Rotas definidas pelo utilizador

-   Aplicação de segurança de rede

-   Gateway de Aplicação

-   Firewall de aplicações Web do Azure

-   Controlo de disponibilidade de rede

#### <a name="network-access-controls"></a>Controlos de acesso de rede
Enquanto a rede Virtual do Azure (VNet) é o fundamento do modelo de redes do Azure e fornece isolamento e proteção, o [grupos de segurança de rede (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) são a principal ferramenta que utilizar para impor e controlar as regras de tráfego de rede no nível de rede.

![ Controlos de acesso de rede](media/azure-network-security/azure-network-security-fig-8.png)


Pode controlar o acesso ao permitir ou negar a comunicação entre as cargas de trabalho dentro de uma rede virtual, de sistemas em redes do cliente por meio de conectividade em vários locais, ou direcionar a comunicação com a Internet.

No diagrama, VNets e NSGs residirem numa camada específica no Azure geral pilha do security, onde os NSGs, UDR e aplicações virtuais de rede podem ser utilizadas para criar limites de segurança para proteger as implementações de aplicações na rede protegida.

NSGs utilizam uma 5 cadeias de identificação para avaliar o tráfego (e são utilizados nas regras que configurar para o NSG):

-   [Endereço IP de origem e de destino](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Porta de origem e destino](https://technet.microsoft.com/library/dd197515)

-   Protocolo: [O protocolo de controlo de transmissão (TCP)](https://technet.microsoft.com/library/cc940037.aspx) ou [o protocolo de datagrama de utilizador (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Isso significa que pode controlar o acesso entre uma VM única e um grupo de VMs ou uma única VM para outra VM única ou entre sub-redes inteiras. Novamente, tenha em atenção que este é o pacote com monitoração de estado simple, filtragem, inspeção de pacotes não completa. Não existe validação do protocolo ou IDS de nível de rede ou capacidade IPS num grupo de segurança de rede.

Um NSG é fornecido com algumas regras incorporadas que deve estar atento. Nomeadamente:

-   **Permitir todo o tráfego dentro de uma rede virtual específico:** Todas as VMs na mesma rede Virtual do Azure podem comunicar entre si.

-   **Permitir a entrada de balanceamento de carga do Azure:** esta regra permite o tráfego de qualquer endereço de origem para qualquer endereço de destino para o Balanceador de carga do Azure.

-   **Negar toda a entrada:** esta regra bloqueia todo o tráfego da Internet que tem a permissão explicitamente de fornecimento.

-   **Permitir todo o tráfego de saída à Internet:** Esta regra permite que as VMs iniciar as ligações à Internet. Se não pretender que estas ligações iniciadas, terá de criar uma regra para bloquear essas ligações ou impor o túnel forçado.

#### <a name="system-routes-and-user-defined-routes"></a>Rotas de sistema e as rotas definidas pelo utilizador

Quando adiciona máquinas virtuais (VMs) a uma rede virtual (VNet) no Azure, notar que as VM conseguem comunicar entre si através da rede, automaticamente. Não é necessário especificar um gateway, apesar das VM estarem em sub-redes diferentes.

O mesmo se verifica para a comunicação entre as VM e a Internet pública, e até mesmo na sua rede no local quando está presente uma ligação híbrida a partir do Azure para o seu centro de dados.

![Rotas de sistema](media/azure-network-security/azure-network-security-fig-9.png)

Este fluxo de comunicação é possível porque o Azure utiliza uma série de rotas de sistema para definir como flui o tráfego IP. As rotas de sistema controlam o fluxo de comunicação nos seguintes cenários:

-   A partir da mesma sub-rede.

-   A partir de uma sub-rede para outra numa VNet.

-   A partir das VM para a Internet.

-   A partir de uma VNet para outra VNet através de um gateway VPN.

-   A partir de uma VNet para outra VNet através do Peering de VNet ([encadeamento de serviços](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   A partir de um VNet para a sua rede no local através de um gateway VPN.

Muitas empresas têm segurança rigorosos e as políticas de requisitos de conformidade que necessitam de inspeção de locais de todos os pacotes de rede para impor específico. O Azure fornece um mecanismo chamado [túnel forçado](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) que encaminha o tráfego das VMs no local através da criação de uma rota personalizada ou por [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) anúncios através do ExpressRoute ou VPN.

O túnel forçado no Azure está configurado por meio de rotas definidas pelo utilizador da rede virtual (UDR). Redirecionar o tráfego para um site no local é expressa como uma rota predefinida para o gateway de VPN do Azure.

A secção seguinte mostra a limitação atual da tabela de encaminhamento e rotas de uma rede Virtual do Azure:

- Cada sub-rede da rede virtual tem uma tabela de roteamento interno, do sistema. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:

  -  **Rotas de VNet locais:** Diretamente para o destino de VMs na mesma rede virtual

  - **Sobre rotas no local:** Para o gateway de VPN do Azure

  -  **Rota predefinida:** Diretamente à Internet. Pacotes destinados aos endereços IP privados, não abrangidos pelas anteriores duas rotas são ignorados.

- Com o lançamento das rotas definidas pelo utilizador, pode criar uma tabela de encaminhamento para adicionar uma rota predefinida e, em seguida, associar a tabela de encaminhamento para a sub-rede de VNet para ativar o protocolo de túnel forçado nessas sub-redes.

- Tem de definir um "site predefinido" entre os sites locais em vários locais ligado à rede virtual.

- O túnel forçado tem de ser associado a uma VNet com um gateway de VPN encaminhamento dinâmico (não um gateway estático).

- Túnel forçado do ExpressRoute não está configurado por intermédio deste mecanismo, mas em vez disso, está ativado por uma rota predefinida por meio de sessões de peering de BGP de ExpressRoute de publicidade.

> [!Note]
> Para obter mais informações, consulte a [documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para obter mais informações.

#### <a name="network-security-appliances"></a>Aplicações de segurança de rede
Embora os grupos de segurança de rede e as rotas definidas pelo utilizador, podem fornecer uma determinada medida de segurança de rede em camadas de rede e transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), lá vai ser situações em que quer ou precisa ativar a segurança em obter níveis mais altos da pilha de rede. Nesse tipo de situação, recomendamos que implemente aplicações de segurança de rede virtual fornecidas pelos parceiros do Azure.

![Aplicações de segurança de rede](./media/azure-network-security/azure-network-security-fig-10.png)

Aplicações de segurança de rede do Azure melhoram a segurança de VNet e funções de rede, e elas podem ser de vários fornecedores através da [do Azure Marketplace](https://azuremarketplace.microsoft.com). Estas aplicações de segurança virtual podem ser implementadas para fornecer:

-   Firewalls de elevada disponibilidade

-   Prevenção de intrusão

-   Detecção de intrusão

-   Firewalls de aplicações Web (WAFs)

-   Otimização de WAN

-   Encaminhamento

-   Balanceamento de carga

-   VPN

-   Gestão de certificados

-   Active Directory

-   Autenticação multifator

#### <a name="application-gateway"></a>Gateway de aplicação

[O Gateway de aplicação do Microsoft Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) é uma aplicação virtual dedicada que fornece um controlador de entrega de aplicações (ADC) como um serviço.

 ![Gateway de Aplicação](./media/azure-network-security/azure-network-security-fig-11.png)

Gateway de aplicação permite-lhe otimizar a disponibilidade e desempenho do web farm ao descarregar a terminação SSL com utilização intensiva da CPU para o gateway de aplicação (-descarga de SSL). Ele fornece também outras capacidades de encaminhamento de camada 7 incluindo:

-   Distribuição de round robin de tráfego de entrada

-   Afinidade de sessão baseada em cookies

-   Encaminhamento baseado no caminho do URL

-   Capacidade de alojar vários Web sites atrás de um único Gateway de aplicação


R [firewall de aplicações web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) também é fornecido como parte do gateway de aplicação. Isso proporciona proteção às aplicações web do web vulnerabilidades e exploits comuns. Gateway de aplicação pode ser configurado como um gateway, gateway só interno ou uma combinação de ambos de acesso à Internet.

WAF do Gateway de aplicação pode ser executado no modo de deteção ou prevenção. É um caso de utilização comuns para administradores para serem executadas no modo de deteção para observar o tráfego de padrões maliciosos. Assim que a explorações potenciais são detectadas, mudar para modo de prevenção bloqueia o tráfego de entrada suspeito.

 ![Gateway de Aplicação](./media/azure-network-security/azure-network-security-fig-12.png)

Além disso, o WAF do Gateway de aplicação ajuda-o a monitorizar as aplicações web contra ataques através de um registo da WAF em tempo real que estão integrados [do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) e [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) para acompanhar os alertas de WAF e monitorizar tendências facilmente.

O registo de formatada do JSON vai diretamente para a conta de armazenamento do cliente. Tem controle total sobre estes registos e pode aplicar as suas políticas de retenção.

Também pode ingerir estes registos para o seu próprio sistema de análise com [Azure Log Integration](https://aka.ms/AzLog). Registos WAF também estão integrados [registos do Azure Monitor](../log-analytics/log-analytics-overview.md) pelo que pode utilizar os registos do Azure Monitor para executar consultas detalhadas sofisticadas.

#### <a name="azure-web-application-firewall-waf"></a>Firewall de aplicações web do Azure (WAF)

Cada vez mais os aplicativos Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns, como injeção SQL, ataques de script entre sites e outros ataques que aparecem no [10 de principais OWASP](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Impedir essas explorações no aplicativo exige manutenção rigorosa, a aplicação de patches e a monitorização em várias camadas da topologia da aplicação.

 ![Firewall de aplicações Web do Azure (WAF)](./media/azure-network-security/azure-network-security-fig-13.png)

Um centralizado [firewall de aplicações web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) pode proteger contra ataques web e simplifica a gestão de segurança sem a necessidade de alterações de aplicações.

Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

#### <a name="network-availability-controls"></a>Controlos de disponibilidade de rede

Existem diferentes opções para distribuir o tráfego de rede com o Microsoft Azure. Estas opções funcionam de forma diferente entre si, tendo um conjunto de funcionalidades diferente e suportando diferentes cenários. Podem todos ser utilizados em isolamento ou em conjunto.

Seguem-se os controles de disponibilidade de rede:

-   Azure Load Balancer

-   Gateway de Aplicação

-   Gestor de Tráfego

**Balanceador de carga do Azure**

Fornece elevado disponibilidade e desempenho de rede às suas aplicações. É um balanceador de carga de camada 4 (TCP, UDP) que distribui o tráfego de entrada entre instâncias de bom estado de funcionamento dos serviços definidos num conjunto com balanceamento de carga.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


O Balanceador de carga do Azure pode ser configurado para:

-   Carregar saldo tráfego da Internet para máquinas virtuais. Esta configuração é conhecida como [balanceamento de carga com acesso à Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Balancear carga de tráfego entre máquinas virtuais numa rede virtual, entre as máquinas virtuais nos serviços cloud ou entre computadores no local e máquinas virtuais numa rede virtual em vários locais. Esta configuração é conhecida como [balanceamento de carga interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Reencaminhe tráfego externo para uma máquina virtual específica.

Todos os recursos na cloud tem um endereço IP público para ser acessível a partir da Internet. A infraestrutura de nuvem no Azure utiliza endereços IP não encaminháveis internos para seus recursos. Para comunicar com a Internet, o Azure utiliza a tradução de endereços de rede (NAT) com endereços IP públicos.

 **Gateway de aplicação**

 Gateway de aplicação funciona na camada da aplicação (camada 7 na pilha de referência de rede OSI). Atua como um serviço de proxy de reversão, terminando a ligação de cliente e reencaminhando pedidos de pontos finais de back-end.

 **Gestor de tráfego**

O Gestor de tráfego do Microsoft Azure permite-lhe controlar a distribuição de tráfego de utilizador para pontos finais de serviço em datacenters diferentes. Pontos finais de serviço suportados pelo Gestor de tráfego incluem VMs do Azure, aplicações Web e serviços em nuvem. Também pode utilizar o Gestor de Tráfego com pontos finais externos, não pertencentes ao Azure.

O Gestor de tráfego utiliza o sistema de nomes de domínio (DNS) para direcionar os pedidos de cliente para o ponto de final mais adequado com base numa [método de encaminhamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) e o estado de funcionamento dos pontos finais. Gestor de tráfego oferece uma variedade de métodos de encaminhamento de tráfego de mensagens em fila para atender às necessidades de aplicação diferente, o estado de funcionamento do ponto de extremidade [monitorização](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)e a ativação pós-falha automática. O Gestor de Tráfego é resiliente a falhas, incluindo a falhas numa região do Azure inteira.

O Gestor de tráfego do Azure permite-lhe controlar a distribuição de tráfego entre os pontos de extremidade do aplicativo. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure.

Gestor de tráfego oferece duas vantagens principais:

-   Distribuição de tráfego de acordo com um dos vários [métodos de encaminhamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Monitorização contínua do Estado de funcionamento do ponto de extremidade](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) e ativação pós-falha automática quando a ativação de pontos de extremidade.

Quando um cliente tenta estabelecer ligação a um serviço, primeiro ele deverá resolver o nome DNS do serviço para um endereço IP. O cliente liga-se, em seguida, a esse endereço IP para aceder ao serviço. O Gestor de tráfego utiliza o DNS para direcionar clientes para pontos finais de serviço específico com base em regras do método de encaminhamento de tráfego. Os clientes ligam diretamente para o ponto de extremidade selecionado. O Gestor de tráfego não é um proxy ou de um gateway. O Gestor de tráfego não vê o tráfego que passa entre o cliente e o serviço.

### <a name="azure-network-validation"></a>Validação de rede do Azure

Validação de rede do Azure é garantir que a rede do Azure está a funcionar conforme está configurado e validação pode ser feita usando os serviços e funcionalidades disponíveis para monitorizar a rede. Com o observador de rede do Azure, pode aceder a uma grande quantidade de Registro em log e os recursos de diagnóstico fornecem informações para compreender o desempenho da rede e o estado de funcionamento. Estas funções são acessíveis através do Portal, o Power Shell, da CLI, Rest API e SDK.

Segurança operacional do Azure refere-se a serviços, controles e recursos disponíveis aos utilizadores para proteger seus dados, aplicações e outros ativos no Microsoft Azure. Segurança operacional do Azure baseia-se uma estrutura que incorpore os dados recolhidos através de um vários recursos que são exclusivos da Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa de centro de resposta de segurança Microsoft e o conhecimento profundo do Panorama de ameaças de segurança de cibersegurança.

-   [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Gestor de recursos do Azure

As pessoas e processos que operam o Microsoft Azure são, talvez o recurso de segurança mais importante da plataforma. Esta secção descreve os recursos de infraestrutura de datacenters da Microsoft que ajudam a aprimorar e manter a segurança, a continuidade e a privacidade.

Normalmente, a infraestrutura da sua aplicação é composta por vários componentes, como uma máquina virtual, uma conta de armazenamento e uma rede virtual, ou uma aplicação Web, uma base de dados, um servidor de base de dados e serviços de terceiros. Não vê estes componentes como entidades separadas. Em vez disso, vê-os como partes relacionadas e interdependentes de uma única entidade. Deve implementá-los, geri-los e monitorizá-los como um grupo. O Azure Resource Manager permite trabalhar com os recursos na sua solução como um grupo.

Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Utiliza um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, como de teste e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

**As vantagens da utilização do Resource Manager**

O Resource Manager oferece várias vantagens:

-   Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

-   Pode implementar repetidamente a solução durante todo o ciclo de vida de desenvolvimento e ter a confiança de que os recursos são implementados num estado consistente.

-   Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

-   Pode definir as dependências entre os recursos, para que sejam implementados na ordem correta.

-   Pode aplicar o controlo de acesso a todos os serviços no seu grupo de recursos porque o Controlo de Acesso Baseado em Funções (RBAC) está integrado de forma nativa na plataforma de gestão.

-   Pode aplicar etiquetas a recursos para organizar logicamente todos os recursos na sua subscrição.

-   Pode clarificar a faturação da sua organização visualizando os custos de um grupo de recursos partilhando a etiqueta.

> [!Note]
> O Resource Manager proporciona uma nova forma de implementar e gerir as suas soluções. Se utilizou o modelo de implementação anterior e pretende obter informações sobre as alterações, consulte [Compreender a implementação do Resource Manager e a implementação clássica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Monitorização e registo de rede do Azure

O Azure oferece diversas ferramentas para monitorizar, prevenir, detetar e responder a eventos de segurança de rede. Algumas das ferramentas mais poderosas disponíveis para nesta área incluem:

-   Observador de Rede

-   Monitorização ao nível da rede recursos

-   Registos do Azure Monitor

### <a name="network-watcher"></a>Observador de rede

[Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -monitorização baseada em cenário é fornecido com os recursos do observador de rede. Este serviço inclui a captura de pacotes, próximo salto, fluxo de IP verificar, a vista de grupo de segurança, os registos de fluxo do NSG. A monitorização ao nível do cenário fornece uma vista de ponta a ponta dos recursos de rede em comparação com a monitorização de recursos de rede individuais.

 ![Observador de Rede](./media/azure-network-security/azure-network-security-fig-15.png)

Observador de rede é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário rede, para e do Azure. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam a compreender, diagnosticar e obter informações da sua rede no Azure.

Observador de rede atualmente tem as seguintes capacidades:

#### <a name="topology"></a>Topologia

[Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) retorna um gráfico de recursos de rede numa rede virtual. O gráfico ilustra a interligação entre os recursos para representar a conectividade de rede ponto a ponto. No portal, topologia devolve os objetos de recurso no, de acordo com a base de rede virtual. As relações estão representadas por linhas entre os recursos fora da região de observador de rede, mesmo que no recurso de grupo não será apresentado. Os recursos retornados na vista de portal são um subconjunto dos componentes do sistema de rede que são representados no gráfico. Para ver a lista completa dos recursos de rede, pode utilizar [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) ou [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Como os recursos são devolvidos a ligação entre eles são modelados em duas relações.

- **Contenção** -rede Virtual contém uma sub-rede, com uma NIC.

- **Associados** -A NIC está associada uma VM.

#### <a name="variable-packet-capture"></a>Captura de pacote variável

Observador de rede [captura de pacote variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) permite-lhe criar sessões de captura de pacotes para controlar o tráfego de e para uma máquina virtual. Ajuda de captura de pacotes para diagnosticar anomalias rede de forma reativa e proactivity. Outras utilizações incluem a recolha de estatísticas de rede, obter informações sobre a invasões de rede, para depurar as comunicações cliente-servidor e muito mais.

Captura de pacotes é uma extensão de máquina virtual que é iniciada remotamente através do observador de rede. Esta capacidade alivia a carga da execução manual de uma captura de pacotes na máquina virtual pretendida, que economiza um valioso tempo. Captura de pacotes pode ser acionada através do portal, o PowerShell, a CLI ou a REST API. É um exemplo de como a captura de pacotes pode ser acionada com alertas de Máquina Virtual.

#### <a name="ip-flow-verify"></a>Verificação do fluxo de IP

[Certifique-se de fluxos de IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) verifica se um pacote é permitido ou negado para ou de uma máquina virtual com base nas informações de 5 cadeias de identificação. Estas informações consistem em direção, protocolo, local IP, remota IP, porta local e porta remota. Se o pacote for recusado por um grupo de segurança, é devolvido o nome da regra que negou o pacote. Embora qualquer IP de origem ou de destino pode ser escolhido, esta funcionalidade ajuda os administradores de diagnosticar rapidamente problemas de conectividade a partir de ou para a internet e de ou para o ambiente no local.

Fluxos de IP, verifique se destina-se uma interface de rede de uma máquina virtual. Fluxo de tráfego, em seguida, é verificado com base nas configurações de ou para essa interface de rede. Esta funcionalidade é útil para confirmar se uma regra num grupo de segurança de rede está a bloquear o tráfego de entrada ou saída de ou para uma máquina virtual.

#### <a name="next-hop"></a>Próximo salto

Determina a [próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) para os pacotes que está a ser encaminhadas para recursos de infraestrutura de rede do Azure, permitindo-lhe diagnosticar qualquer configurado incorretamente rotas definidas pelo utilizador. Tráfego de uma VM é enviado para um destino com base nas rotas efetivas associadas a uma NIC. Salto seguinte obtém o tipo de próximo salto e o endereço IP de um pacote a partir de uma máquina virtual específica e a NIC. Isto ajuda a determinar se o pacote estiver sendo direcionado para o destino ou é o tráfego a ser preto holed.

Também o salto seguinte devolve a tabela de rotas associada com o próximo salto. Ao consultar um salto seguinte se a rota é definida como uma rota definida pelo utilizador, será devolvido esse caminho. Caso contrário, o salto seguinte devolve "Rotas de sistema".

#### <a name="security-group-view"></a>Vista do grupo de segurança

Obtém as regras de segurança aplicada e eficiente que são aplicadas numa VM. Grupos de segurança de rede são associados a um nível de sub-rede ou num nível NIC. Quando associados a um nível de sub-rede, aplica-se a todas as instâncias VM na sub-rede. Rede [vista de grupo de segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) devolve todas as configurado NSGs e regras que estão associadas a um nível NIC e sub-rede de uma máquina virtual, fornece ideias sobre a configuração. Além disso, as regras de segurança efetivas são devolvidas para cada uma das NICs numa VM. Vista de grupo de segurança de rede a utilizar, pode avaliar uma VM para vulnerabilidades de rede, tais como abrir portas. Também pode validar se o seu grupo de segurança de rede está a funcionar conforme esperado com base numa [comparação entre configurada e as regras de segurança efetivas](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Registo de fluxo de NSG

 Os registos de fluxo para grupos de segurança de rede permitem-lhe capturar registos relacionados com o tráfego que é permitido ou negado pelas regras de segurança no grupo. O fluxo é definido por uma informações de 5 cadeias de identificação – IP de origem, IP de destino, porta de origem, porta de destino e protocolo.

[Os registos de fluxo do grupo de segurança de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) são uma funcionalidade do observador de rede permite-lhe ver informações sobre o tráfego IP de entrada e de saída através de um grupo de segurança de rede.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Gateway de rede virtual e a resolução de problemas de ligação

Observador de rede oferece muitos recursos que diz respeito à Noções básicas sobre os recursos de rede no Azure. Um desses recursos é o recurso de resolução de problemas. [Resolução de problemas do recurso](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) pode ser chamado pelo PowerShell, CLI ou REST API. Quando chamado, o observador de rede inspeciona o estado de funcionamento de um gateway de rede Virtual ou uma ligação e devolve conclusões.

Esta secção orienta-o por tarefas de gestão diferentes que estão atualmente disponíveis para a resolução de problemas de recursos.

-   [Resolver problemas relacionados com um gateway de rede Virtual](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Resolver problemas de uma ligação](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Limites de subscrição de rede

[Limites de subscrição de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornecer-lhe detalhes de utilização de cada um o recurso de rede numa subscrição numa região contra o número máximo de recursos disponíveis.

#### <a name="configuring-diagnostics-log"></a>Configuração de diagnósticos de registo

Observador de rede fornece uma [registos de diagnóstico](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) vista. Esta vista contém todos os recursos de rede que suportam o registo de diagnósticos. A partir desta vista, pode ativar e desativar recursos de rede rápida e conveniente.

### <a name="network-resource-level-monitoring"></a>Monitorização ao nível da rede recursos

As seguintes funcionalidades estão disponíveis para monitorização de nível de recursos:

#### <a name="audit-log"></a>Registo de auditoria

Operações efetuadas como parte da configuração de redes são registadas. Estes registos de auditoria são essenciais para estabelecer várias regulamentares. Estes registos podem ser visualizados no portal do Azure ou obtido com ferramentas da Microsoft, como o Power BI ou ferramentas de terceiros. Registos de auditoria estão disponíveis através do portal, PowerShell, CLI e Rest API.

> [!Note]
> Para obter mais informações sobre os registos de auditoria, consulte [auditar operações com o Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Registos de auditoria estão disponíveis para operações feitas em todos os recursos de rede.


#### <a name="metrics"></a>Métricas

As métricas são medidas de desempenho e contadores coletados durante um período. As métricas estão atualmente disponíveis para o Gateway de aplicação. Métricas podem ser utilizadas para acionar alertas com base num limiar. O Gateway de aplicação do Azure por predefinição monitoriza o estado de funcionamento de todos os recursos no seu conjunto de back-end e automaticamente remove qualquer recurso considerado em mau estado de funcionamento do conjunto. Gateway de aplicação continua a monitorizar as instâncias em mau estado de funcionamento e adiciona-os novamente para o conjunto de back-end em bom estado, uma vez que eles se tornarem disponíveis e respondem às sondas de estado de funcionamento. Gateway de aplicação envia que o estado de funcionamento sondas com a mesma porta que é definida nas definições de HTTP de back-end. Esta configuração garante que a sonda está a testar a mesma porta que os clientes estariam a utilizar para ligar ao back-end.

> [!Note]
> Ver [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) para ver como as métricas podem ser usadas para criar alertas.

#### <a name="diagnostic-logs"></a>Registos de diagnósticos

Eventos periódicos e espontânea são criados pelos recursos de rede e com sessão iniciados em contas de armazenamento, enviadas para um Hub de eventos ou os registos do Azure Monitor. Estes registos fornecem informações sobre o estado de funcionamento de um recurso. Estes registos podem ser visualizados em ferramentas como o Power BI e o Azure Monitor registos. Para saber como ver os registos de diagnóstico, visite [registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Os registos de diagnóstico estão disponíveis para [Balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), rotas, e [Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Observador de rede fornece que um diagnóstico regista o modo de exibição. Esta vista contém todos os recursos de rede que suportam o registo de diagnósticos. A partir desta vista, pode ativar e desativar recursos de rede rápida e conveniente.

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Registos de Monitor do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) é um serviço no Azure que monitoriza a sua cloud e ambientes para manter a disponibilidade e desempenho no local. Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens.

Registos de Monitor do Azure oferece as seguintes soluções para monitorização de suas redes:

-   Monitor de desempenho de rede (NPM)

-   Análise de Gateway de aplicação do Azure

-   Análise do grupo de segurança de rede do Azure

#### <a name="network-performance-monitor-npm"></a>Monitor de desempenho de rede (NPM)
O [Monitor de desempenho de rede](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) solução de gestão é uma solução que monitoriza o estado de funcionamento, disponibilidade e acessibilidade de redes de monitorização de rede.

É utilizado para monitorizar a conectividade entre:

-   A cloud pública e no local

-   Centros de dados e locais do usuário (filiais)

-   Sub-redes alojar várias camadas de um aplicativo de várias camadas.


#### <a name="azure-application-gateway-analytics-in-azure-monitor-logs"></a>Análise do gateway de aplicação do Azure nos registos do Azure Monitor

Os seguintes registos são suportados para Gateways de aplicação:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

As métricas seguintes são suportadas para Gateways de aplicação:

-   débito de 5 minutos

#### <a name="azure-network-security-group-analytics-in-azure-monitor-logs"></a>Análise de grupo de segurança de rede do Azure nos registos do Azure Monitor

Os seguintes registos são suportados para [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** Contém entradas para o qual NSG as regras são aplicadas a VMs e funções de instância com base no endereço MAC. O estado para estas regras é recolhido a cada 60 segundos.

- **NetworkSecurityGroupRuleCounter:** Contém entradas para quantas vezes cada regra NSG é aplicada a negar ou permitir tráfego.

## <a name="next-steps"></a>Passos Seguintes
Obter mais informações sobre segurança lendo alguns dos nossos tópicos de segurança detalhados:

-   [Registos de Monitor do Azure para grupos de segurança de rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Inovações do sistema de rede que orientam a interrupção da cloud](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: O sistema de rede de mudar de software que fornece o Cloud Global da Microsoft](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Como Microsoft baseia-se a rede global de rápida e fiável](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Disponibilizar inovação de rede](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
