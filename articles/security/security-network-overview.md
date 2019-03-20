---
title: Requisitos no Azure e de conceitos de segurança de rede | Documentos da Microsoft
description: Este artigo fornece explicações básicas sobre conceitos de segurança de rede principal e os requisitos e informações sobre o que o Azure oferece em cada uma dessas áreas.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 2aabe3d1fa8a6034c2dab38c8d6fa6da4b00ac1b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080344"
---
# <a name="azure-network-security-overview"></a>Descrição geral da segurança de rede do Azure

Segurança de rede pode ser definida como o processo de proteger os recursos de acesso não autorizado ou ataques ao aplicar controlos para tráfego de rede. O objetivo é garantir que apenas legítimo tráfego é permitido. Azure inclui uma infraestrutura de rede robusta para suportar a sua aplicação e os requisitos de conectividade do serviço. Conectividade de rede é possível entre recursos localizados no Azure, entre no local e recursos, alojados no Azure e de e para a internet e o Azure.

Este artigo aborda algumas das opções que o Azure oferece na área de segurança de rede. Pode aprender sobre:

* Redes do Azure
* Controlo de acesso de rede
* Azure Firewall
* Proteger o acesso remoto e em vários locais conectividade
* Disponibilidade
* Resolução de nomes
* Arquitetura de rede (rede de Perímetro) de perímetro
* Proteção contra DDoS do Azure
* Porta de entrada do Azure
* Gestor de tráfego
* Monitorização e deteção de ameaças

## <a name="azure-networking"></a>Redes do Azure

O Azure requer máquinas virtuais para ser ligada a uma rede Virtual do Azure. Uma rede virtual é uma construção lógica criada sobre os recursos de infraestrutura do Azure de rede física. Cada rede virtual é isolada de todas as outras redes virtuais. Isto ajuda a garantir que o tráfego de rede das implementações não está acessível a outros clientes do Azure.

Saiba mais:

* [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Controlo de acesso de rede

Controlo de acesso de rede é o ato de limitar a conectividade de e para dispositivos específicos ou sub-redes numa rede virtual. O objetivo de controlo de acesso de rede é limitar o acesso às suas máquinas virtuais e serviços para os utilizadores aprovados e dispositivos. Controlos de acesso são baseados em decisões para permitir ou Negar ligações de e para a máquina virtual ou serviço.

O Azure suporta vários tipos de controlo de acesso à rede, tais como:

* Controlo de camada de rede
* Encaminhar o controle e túnel forçado
* Aplicações de segurança de rede virtual

### <a name="network-layer-control"></a>Controlo de camada de rede

Qualquer implementação segura requer alguma medida de controlo de acesso de rede. É o objetivo de controlo de acesso de rede restringir a comunicação de máquina virtual para os sistemas necessários. Outras tentativas de comunicação são bloqueadas.

> [!NOTE]
> Firewalls de armazenamento são abordados os [descrição geral da segurança de armazenamento do Azure](security-storage-overview.md) artigo

#### <a name="network-security-rules-nsgs"></a>Regras de segurança de rede (NSGs)

Se precisar de controlo de acesso de nível de rede básico (com base no endereço IP e os protocolos TCP ou UDP), pode utilizar grupos de segurança de rede (NSGs). Um NSG é um básico, com monitoração de estado, filtragem de firewall de pacotes e permite-lhe controlar o acesso com base numa [5 cadeias de identificação](https://www.techopedia.com/definition/28190/5-tuple). NSGs incluem a funcionalidade para simplificar o gerenciamento e reduzir as hipóteses de erros de configuração:

* **Regras de segurança aumentadas** Simplifique a definição da regra NSG e permitem-lhe criar regras complexas, em vez de ter de criar várias regras simples para alcançar o mesmo resultado.
* **Etiquetas de serviço** é a Microsoft criou as etiquetas que representam um grupo de endereços IP. Sejam atualizados dinamicamente para incluir os intervalos IP que cumprem as condições que definem a inclusão na etiqueta. Por exemplo, se quiser criar uma regra que se aplica a todo o armazenamento do Azure na região Leste pode utilizar eastus
* **Grupos de segurança de aplicativo** permitem-lhe implementar os recursos a grupos de aplicativo e controlar o acesso a esses recursos através da criação de regras que usam esses grupos de aplicações. Por exemplo, se tiver servidores Web implementado para o grupo de aplicações 'Webservers' pode criar uma regra que aplica-se um NSG a permitir o tráfego de 443 da Internet para todos os sistemas no grupo de aplicações de servidores "Web".

Os NSGs não fornecem inspeção da camada de aplicativo ou autenticado controlos de acesso.

Saiba mais:

* [Grupos de segurança de rede](../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC just-in-acesso à VM do tempo

[Centro de segurança do Azure](../security-center/security-center-intro.md) pode gerir os NSGs em VMs e bloquear o acesso à VM até que um usuário com o controlo de acesso baseado em funções apropriadas [RBAC](../role-based-access-control/overview.md) acesso de pedidos de permissões. Quando o usuário estiver com êxito o ASC autorizado torna modificações para os NSGs para permitir o acesso a portas selecionadas para o período de tempo especificado. Quando o período expira os NSGs são restaurados para seus protegida estado anterior.

Saiba mais:

* [Centro de segurança do Azure no momento exato o acesso](../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Pontos finais de serviço

Pontos finais de serviço são outra forma de aplicar o controlo sobre o seu tráfego. Pode limitar a comunicação com os serviços com suporte para apenas as suas VNets através de uma ligação direta. Tráfego da sua VNet ao serviço do Azure especificado permanece na rede backbone do Microsoft Azure.  

Saiba mais:

* [Pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md#securing-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Encaminhar o controle e túnel forçado

A capacidade de controlar o comportamento do encaminhamento nas suas redes virtuais é fundamental. Se o encaminhamento é configurado incorretamente, aplicações e serviços alojados na sua máquina virtual podem ligar a dispositivos não autorizados, incluindo sistemas detidas e operadas por invasores potenciais.

Redes do Azure suportam a capacidade de personalizar o comportamento de encaminhamento de tráfego de rede nas suas redes virtuais. Isto permite-lhe alterar o padrão de encaminhamento de entradas de tabela na sua rede virtual. Controlo do comportamento do encaminhamento ajuda-o a certificar-se de que todo o tráfego de um determinado dispositivo ou grupo de dispositivos entra ou sai da rede virtual através de uma localização específica.

Por exemplo, pode ter uma aplicação de segurança de rede virtual na sua rede virtual. Pretende certificar-se de que todo o tráfego de e para a rede virtual passa por que a aplicação virtual de segurança. Pode fazer isso configurando [rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md) (UDRs) no Azure.

[Túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para se certificar de que os seus serviços não têm permissão para iniciar uma ligação para dispositivos na internet. Tenha em atenção que isto é diferente da aceitar ligações de entrada e, em seguida, responder a elas. Servidores web front-end tem de responder a pedidos a partir de anfitriões da internet e, então, obtido de internet o tráfego é permitido de entrada para estes servidores web e os servidores web estão autorizados a responder.

O que não quiser que é um servidor web front-end para iniciar um pedido de saída. Esses pedidos podem representar um risco de segurança, uma vez que estas ligações podem ser utilizadas para transferir o software maligno. Mesmo se quiser que estes servidores front-end para iniciar pedidos de saída à internet, pode querer forçá-los para ir até os proxies da web no local. Isto permite-lhe tirar partido do URL de filtragem e de registo.

Em vez disso, gostaria de utilizar o túnel forçado para evitar esta situação. Quando ativar o protocolo de túnel forçado, são forçadas todas as ligações à internet através do gateway no local. Pode configurar o túnel forçado, aproveitando as UDRs.

Saiba mais:

* [O que são rotas definidas pelo utilizador e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Aplicações de segurança de rede virtual

Embora os NSGs, UDRs e protocolo de túnel forçado fornecem-lhe um nível de segurança em camadas de rede e transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), pode também querer ativar a segurança em níveis superiores da rede.

Por exemplo, os seus requisitos de segurança podem incluir:

* Autenticação e autorização antes de permitir o acesso à sua aplicação
* Deteção de intrusões e resposta de intrusão
* Inspeção da camada de aplicativo para protocolos de alto nível
* Filtragem de URL
* Antivírus de nível de rede e Antimalware
* Proteção de anti-bot
* Controlo de acesso de aplicações
* Proteção contra DDoS adicional (acima da proteção de DDoS fornecida pelos recursos de infraestrutura do Azure em si)

Pode acessar esses recursos de segurança de rede aprimorada com uma solução de parceiro do Azure. Pode encontrar a rede de parceiros do Azure mais recente soluções de segurança ao aceder a [do Azure Marketplace](https://azure.microsoft.com/marketplace/)e ao procurar por "security" e "segurança de rede".

## <a name="azure-firewall"></a>Azure Firewall

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É uma firewall com total monitoração de estado como um serviço com elevada disponibilidade incorporada e escalabilidade da cloud sem restrições. Algumas funcionalidades incluem:

* Elevada disponibilidade
* Escalabilidade de cloud
* Regras de filtragem de FQDN de aplicação
* Regras de filtragem de tráfego de rede

Saiba mais:

* [Descrição geral do Firewall do Azure](../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Proteger o acesso remoto e em vários locais conectividade

Instalação, configuração e gestão das suas necessidades de recursos do Azure para ser feita remotamente. Além disso, pode querer implantar [TI híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) soluções que tem componentes no local e na cloud pública do Azure. Estes cenários requerem acesso remoto seguro.

Redes do Azure suportam os seguintes cenários de acesso remoto seguro:

* Ligue-se de estações de trabalho individuais a uma rede virtual
* Ligar a sua rede no local a uma rede virtual com uma VPN
* Ligar a sua rede no local a uma rede virtual com um link WAN dedicado
* Ligar redes virtuais entre si

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Ligue-se de estações de trabalho individuais a uma rede virtual

Pode querer permitir que desenvolvedores individuais ou pessoal de operações gerir máquinas virtuais e serviços no Azure. Por exemplo, digamos que precisa de acesso a uma máquina virtual numa rede virtual. Mas a política de segurança não permite o acesso remoto, RDP ou SSH, a máquinas virtuais individuais. Neste caso, pode utilizar um [VPN point-to-site](../vpn-gateway/point-to-site-about.md) ligação.

A ligação de VPN ponto a site permite-lhe configurar uma ligação privada e segura entre o utilizador e a rede virtual. Quando a ligação VPN é estabelecida, o utilizador pode RDP ou SSH através da ligação VPN em qualquer máquina virtual na rede virtual. (Isto pressupõe que o usuário pode autenticar e está autorizado.) Suporta a VPN ponto a site:

* Secure Socket Tunneling Protocol (SSTP), um protocolo proprietário de VPN baseado em SSL. Uma solução de SSL VPN consegue penetrar firewalls, uma vez que a maioria das firewalls abre a porta TCP 443, que usa SSL. O SSTP só é suportado em dispositivos Windows. O Azure suporta todas as versões do Windows que têm de SSTP (Windows 7 e posterior).

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Saiba mais:

* [Configurar uma ligação de ponto a site a uma rede virtual com o PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Ligar a sua rede no local a uma rede virtual com uma VPN

Pode querer ligar-se a toda a rede corporativa ou partes do mesmo, a uma rede virtual. Isso é comum em TI híbridos cenários, em que as organizações [expandir o datacenter no local para o Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Em muitos casos, as organizações alojam partes de um serviço no Azure e partes no local. Por exemplo, eles podem fazê-lo quando uma solução inclui servidores web front-end no Azure e bases de dados do back-end no local. Esses tipos de ligações "entre locais" também tornam a gestão do Azure localizado recursos mais seguro e permitir cenários como expandir os controladores de domínio do Active Directory para o Azure.

Uma forma para realizar isso é utilizar um [VPN site a site](https://www.techopedia.com/definition/30747/site-to-site-vpn). A diferença entre uma VPN de site a site e uma VPN ponto a site é que o último liga-se um único dispositivo a uma rede virtual. Uma VPN de site a site liga-se uma rede inteira (por exemplo, a sua rede no local) a uma rede virtual. VPNs de site a site a uma rede virtual, utilize o modo de encapsulamento IPsec protocolo VPN altamente seguro.

Saiba mais:

* [Criar uma VNet do Resource Manager com uma ligação de VPN de site a site com o portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Acerca do Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Ligar a sua rede no local a uma rede virtual com um link WAN dedicado

Ligações de VPN ponto a site e de site a site são eficazes para ativar a conectividade entre locais. No entanto, algumas organizações consideram-o para as seguintes desvantagens:

* Ligações VPN mover dados através da internet. Isso expõe estas ligações para potenciais problemas de segurança envolvidas com a transferência de dados através de uma rede pública. Além disso, fiabilidade e disponibilidade para ligações de internet não podem ser garantidas.
* Ligações de VPN para redes virtuais podem não ter a largura de banda para algumas aplicações e os efeitos, à medida que o máximo horizontalmente em cerca de 200 Mbps.

As organizações que precisam, normalmente, o maior nível de segurança e a disponibilidade para as suas ligações entre locais utilizam ligações WAN dedicadas para ligar a sites remotos. Azure fornece-lhe a capacidade de utilizar uma ligação WAN dedicada que pode utilizar para ligar a sua rede no local a uma rede virtual. O Azure ExpressRoute, alcance global do Express route direto e Express route ativar esta opção.

Saiba mais:

* [Visão geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md)
* [ExpressRoute direto](../expressroute/expressroute-erdirect-about.md)
* [Express route de alcance global](..//expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Ligar redes virtuais entre si

É possível usar diversas redes virtuais para as suas implementações. Existem vários motivos por que pode fazer isso. Talvez queira simplificar a gestão ou, talvez tenha uma maior segurança. Independentemente da motivação para colocar recursos em redes virtuais diferentes, haverá ocasiões em que desejará recursos em cada uma das redes para se ligar entre si.

Uma opção é para os serviços numa rede virtual para ligar aos serviços em outra rede virtual, por "novamente um loop" através da internet. A ligação inicia numa rede virtual, passa pela internet e, em seguida, volta para a rede virtual de destino. Esta opção expõe a ligação para os problemas de segurança inerentes a qualquer comunicação baseado na internet.

Uma opção melhor seria criar uma VPN de site a site que estabelece ligação entre duas redes virtuais. Este método utiliza o mesmo [modo de encapsulamento IPSec](https://technet.microsoft.com/library/cc786385.aspx) protocolo da ligação de VPN de site a site em vários locais mencionadas acima.

A vantagem dessa abordagem é que a ligação VPN é estabelecida sobre os recursos de infraestrutura de rede do Azure, em vez de ligar através da internet. Isso fornece uma camada extra de segurança, em comparação comparada VPNs de site a site que se ligam através da internet.

Saiba mais:

* [Configurar uma ligação VNet a VNet com o Azure Resource Manager e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Outra forma de ligar as suas redes virtuais é [VNET peering](../virtual-network/virtual-network-peering-overview.md). Esta funcionalidade permite-lhe ligar duas redes do Azure, para que ocorra de comunicação entre eles através da infraestrutura principal da Microsoft sem ele passar através da Internet. O VNET peering pode ligar duas VNETs na mesma região ou duas VNETs entre regiões do Azure. Podem ser utilizados NSGs para limitar a conectividade entre sistemas ou sub-redes diferentes.

## <a name="availability"></a>Disponibilidade

Disponibilidade é um componente fundamental de qualquer programa de segurança. Se os utilizadores e os sistemas não é possível acessar o que precisam de aceder através da rede, o serviço pode ser considerado comprometido. O Azure tem as tecnologias de rede que suportam os seguintes mecanismos de elevada disponibilidade:

* Balanceamento de carga baseado em HTTP
* Balanceamento de carga ao nível de rede
* Balanceamento de carga global

Balanceamento de carga é um mecanismo projetado para distribuir igualmente conexões entre vários dispositivos. Os objetivos de balanceamento de carga são:

* Para aumentar a disponibilidade. Quando carrega o saldo ligações em vários dispositivos, um ou mais dos dispositivos podem se tornar indisponível sem comprometer o serviço. Os serviços em execução nos restantes dispositivos online podem continuar a servir o conteúdo do serviço.
* Para aumentar o desempenho. Quando carrega o saldo ligações em vários dispositivos, um único dispositivo não tem de lidar com todo o processamento. Em vez disso, as necessidades de processamento e memória para satisfazer as necessidades o conteúdo é distribuído por vários dipositivos.

### <a name="http-based-load-balancing"></a>Balanceamento de carga baseado em HTTP

As organizações que executam serviços baseados na web, muitas vezes, vontade de ter um balanceador de carga baseado em HTTP à frente esses serviços web. Isto ajuda a garantir níveis adequados de desempenho e de elevada disponibilidade. Balanceadores de carga tradicional, com base em rede dependem de protocolos de camada de transporte e de rede. Balanceadores de carga baseado em HTTP, por outro lado, tomar decisões com base nas características do protocolo HTTP.

O Gateway de aplicação do Azure fornece para os seus serviços baseados na web de balanceamento de carga baseado em HTTP. Gateway de aplicação suporta:

* Afinidade de sessão baseada em cookies. Esta capacidade torna-se de que as ligações estabelecidas para um dos servidores por trás do Balanceador de carga que permanece intacta entre o cliente e servidor. Isto garante a estabilidade de transações.
* Descarga de SSL. Quando um cliente liga-se com o Balanceador de carga, essa sessão é encriptada utilizando o protocolo HTTPS (SSL). No entanto, para aumentar o desempenho, pode utilizar o protocolo HTTP (não encriptada) para estabelecer ligação entre o Balanceador de carga e o servidor de web por trás do Balanceador de carga. Isto é referido como "Descarga de SSL", porque os servidores web por trás do Balanceador de carga não experienciar a sobrecarga de processador envolvida com a encriptação. Os servidores web, por conseguinte, podem atender solicitações mais rapidamente.
* Com base no URL de encaminhamento de conteúdo. Esta funcionalidade torna possível para o Balanceador de carga para tomar decisões sobre onde para ligações de encaminhamento com base no URL de destino. Isto proporciona flexibilidade muito mais que as soluções que tornam a carregar balanceamento decisões com base em endereços IP.

Saiba mais:

* [Descrição geral do Gateway de aplicação](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Balanceamento de carga ao nível de rede

Em contraste com baseado em HTTP balanceamento de carga, o balanceamento de carga ao nível de rede faz decisões com base no IP endereço e a porta (TCP ou UDP) números.
Pode aproveitar os benefícios do nível balanceamento de carga no Azure com o Azure Load Balancer. Algumas características importantes do Balanceador de carga incluem:

* Balanceamento de carga ao nível de rede com base nos números de porta e o endereço IP.
* Suporte para qualquer protocolo de camada de aplicativo.
* Faz o balanceamento de carga para máquinas virtuais do Azure e instâncias de função de serviços em nuvem.
* Pode ser utilizado para acesso à internet (Balanceamento de carga externo) e não de internet voltada para máquinas virtuais e aplicações (Balanceamento de carga interno).
* Monitorização do ponto final, que é utilizado para determinar se qualquer um dos serviços atrás do Balanceador de carga tornaram-se indisponível.

Saiba mais:

* [Balanceador de carga com acesso à Internet entre várias máquinas virtuais ou serviços](../load-balancer/load-balancer-internet-overview.md)
* [Descrição geral do Balanceador de carga interno](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Balanceamento de carga global

Algumas organizações esperam o nível mais elevado de disponibilidade possível. Uma forma de alcançar esse objetivo é para alojar as aplicações em datacenters globalmente distribuídos. Quando um aplicativo está alojado em datacenters localizados em todo o mundo, é possível que toda uma região geopolítica se tornar indisponível, e ainda ter a aplicação de cópia de segurança e em execução.

Esta estratégia de balanceamento de carga também pode trazer benefícios de desempenho. Pode direcionar os pedidos para o serviço para o datacenter que estejam mais próximos do dispositivo que está fazendo a solicitação.

No Azure, pode obter os benefícios global de balanceamento de carga utilizando o Gestor de tráfego do Azure.

Saiba mais:

* [O que é o Gestor de Tráfego?](../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Resolução de nomes

Resolução de nomes é uma função essencial para todos os serviços que alojar no Azure. Da perspectiva de segurança, o comprometimento da função de resolução de nome pode levar a um atacante redirecionar pedidos de seus sites para o site de um atacante. Resolução de nome seguro é um requisito para todos os seus serviços de cloud alojada.

Existem dois tipos de resolução de nomes que tem de cumprir:

* Resolução de nomes interna. Isto é utilizado pelos serviços em suas redes virtuais, as redes no local ou ambos. Os nomes utilizados para a resolução do nome interno não estão acessíveis através da internet. Para garantir a segurança, é importante que o esquema de resolução de nome interno não está acessível a utilizadores externos.
* Resolução de nomes externos. Isto é utilizado por pessoas e dispositivos fora de suas redes no local e redes virtuais. Estes são os nomes que estão visíveis para a internet e são utilizados para direcionar a ligação aos seus serviços com base na cloud.

Para a resolução do nome interno, tem duas opções:

* Um servidor DNS de rede virtual. Quando cria uma nova rede virtual, um servidor DNS é criado para. Este servidor DNS pode resolver os nomes dos computadores localizados na rede virtual. Este servidor DNS não é configurável, é gerido pelo Gestor de recursos de infraestrutura do Azure e pode, portanto, ajudá-lo a proteger a sua solução de resolução de nome.
* Traga seu próprio servidor DNS. Tem a opção de colocar um servidor DNS à sua escolha na sua rede virtual. Este servidor DNS pode ser que um Active Directory integrado servidor DNS ou uma solução de servidor DNS dedicada fornecida por um parceiro do Azure, o que pode obter no Azure Marketplace.

Saiba mais:

* [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md)
* [Gerir servidores DNS utilizados por uma rede virtual](../virtual-network/manage-virtual-network.md#change-dns-servers)

Para resolução de nomes externos, tem duas opções:

* Aloje o seu próprio externo DNS server no local.
* Aloje o seu próprio servidor DNS externo com um fornecedor de serviços.

Muitas organizações de grandes alojam o seus próprio DNS servidores no local. Eles podem fazer isso porque têm o conhecimento de rede e a presença global para fazê-lo.

Na maioria dos casos, é melhor alojar os serviços de resolução de nome DNS com um fornecedor de serviços. Esses provedores de serviço tem o conhecimento de rede e a presença global para muito elevada disponibilidade dos seus serviços de resolução de nome. Disponibilidade é essencial para os serviços DNS, porque se falharem a seus serviços de resolução de nome, ninguém será capaz de alcançar com acesso a serviços à internet.

O Azure disponibiliza uma elevada disponibilidade e elevado desempenho externa DNS solução sob a forma de DNS do Azure. Esta solução de resolução de nome externo tira partido da infraestrutura de DNS do Azure em todo o mundo. Permite-lhe alojar o seu domínio no Azure, com as mesmas credenciais, APIs, ferramentas e de faturação dos outros serviços do Azure. Como parte do Azure, também herda os controlos de segurança forte incorporados na plataforma.

Saiba mais:

* [Descrição geral do DNS do Azure](../dns/dns-overview.md)
* [Zonas privadas do DNS do Azure](../dns/private-dns-overview.md) permite-lhe configurar nomes DNS privados para recursos do Azure, em vez dos nomes atribuídos automaticamente sem a necessidade de adicionar uma solução DNS personalizada.

## <a name="perimeter-network-architecture"></a>Arquitetura de rede de perímetro

Muitas organizações de grandes utilizam redes de perímetro para segmentar as suas redes e criar uma zona de memória intermédia entre a internet e seus serviços. A parte de perímetro da rede é considerada uma zona de segurança baixa, e não existem recursos de elevado valor são colocados nesse segmento de rede. Verá, normalmente, dispositivos de segurança de rede que têm uma interface de rede no segmento de rede de perímetro. Outra interface de rede está ligado a uma rede que tenha máquinas virtuais e serviços que aceitam ligações de entrada da internet.

Pode criar redes de perímetro de diversas formas diferentes. A decisão de implementar uma rede de perímetro e, em seguida, o tipo de perímetro de rede a utilizar se optar por utilizar um, depende dos requisitos de segurança da rede.

Saiba mais:

* [Serviços Cloud da Microsoft e segurança de rede](../best-practices-network-security.md)

## <a name="azure-ddos-protection"></a>Proteção contra DDoS do Azure

Os ataques de Denial of Service distribuído (DDoS) são algumas das maiores preocupações de disponibilidade e segurança relativamente aos clientes que estão a mover as suas aplicações para a cloud. Um ataque de DDoS tenta a esgotar os recursos de um aplicativo, que a aplicação disponível para os usuários legítimos. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet.
A Microsoft fornece proteção contra DDoS, conhecida como **básica** como parte da plataforma Azure. Isto é fornecido sem custos e inclui sempre na monitorização e em tempo real mitigação de ataques de nível de rede comuns. Além de proteções incluídas com a proteção contra DDoS **básica** pode habilitar o **padrão** opção. Funcionalidades padrão do DDoS Protection incluem:

* **Integração de plataforma nativa:** Integrado de forma nativa no Azure. Inclui a configuração através do portal do Azure. Padrão de proteção contra DDoS compreende os recursos e a configuração do recurso.
* **Proteção de chave na mão:** Configuração simplificada protege imediatamente todos os recursos numa rede virtual assim que o padrão de proteção contra DDoS está ativada. Nenhuma definição de utilizador ou intervenção é necessária. Padrão de proteção de DDoS instantaneamente mitiga e automática o ataque, assim que este é detetado.
* **Monitorização de tráfego sempre ativa:** Os padrões de tráfego de aplicativo são monitorizados 24 horas por dia, 7 dias por semana, à procura de indicadores de ataques de DDoS. Atenuação é executada quando as políticas de proteção são excedidas.
* **Relatórios de mitigação de ataque** relatórios de mitigação de ataque utilizar dados de fluxo de rede agregados para fornecer informações detalhadas sobre ataques destinados a seus recursos.
* **Registos de fluxo de mitigação de ataque** registos de fluxo de mitigação de ataque permite-lhe rever o tráfego ignorado, tráfego reencaminhado e outros dados de ataque em tempo quase real durante um ataque de DDoS Active Directory.
* **Otimização adaptável:** A criação de perfis de tráfego inteligente aprende o tráfego da aplicação ao longo do tempo e seleciona e atualiza o perfil que é mais adequado para o seu serviço. O perfil ajusta à medida tráfego muda ao longo do tempo. Camada 3 para proteção de camada 7: Fornece proteção contra DDoS de pilha completa, quando utilizado com uma firewall de aplicações web.
* **Escala de atenuação extenso:** Mais de 60 tipos de ataques diferentes podem ser atenuados com capacidade global, para proteger contra os ataques de DDoS conhecidos maior.
* **Métricas de ataque:** Métricas resumidas de cada ameaça são acessíveis através do Azure Monitor.
* **Alertas de ataque:** Alertas podem ser configurados no início e paragem de um ataque e ao longo da duração do ataque, uso de métricas de ataque incorporada. Alertas de integrar o seu software operacional, como o Microsoft Azure Monitor registos, Splunk, armazenamento do Azure, E-Mail e o portal do Azure.
* **Garantia de custo:**  Transferência de dados e aplicações créditos de serviço de escalamento horizontal para ataques de DDoS documentados.
* **Rápida de DDoS reativa** clientes DDoS Protection padrão têm agora acesso a equipe de resposta rápida durante um ataque de Active Directory. DRR pode ajudar a investigação de ataque, atenuações personalizadas durante um ataque e a análise de pós ataque de.


Saiba mais:

* [Descrição geral da proteção de DDOS](../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Porta de entrada do Azure

O serviço de porta de entrada do Azure permite-lhe definir, gerir e monitorizar o encaminhamento do tráfego web global. Otimiza o tráfego encaminhamento para melhor desempenho e elevada disponibilidade. O Azure Front Door permite criar regras de firewall de aplicações Web (WAF) personalizadas para o controlo de acesso de forma a proteger a carga de trabalho HTTP/HTTPS da exploração com base em endereços IP de cliente, no indicativo e em parâmetros http. Além disso, a porta de entrada também lhe permite criar regras para tráfego de bot malicioso à exaustão de limitação de taxas, inclui descarga de SSL e o pedido por-HTTP/HTTPS, processamento de camada de aplicativo.

Própria plataforma de porta de entrada está protegida pelo Azure DDoS Protection básico. Para aumentar a proteção, é possível ativar a proteção padrão do Azure DDoS Protection nas suas VNETs e recursos de salvaguarda contra ataques de camada de rede (TCP/UDP) através de ajuste automático e mitigação. Porta de entrada é um proxy inverso de camada 7, só permite o tráfego web para passagem para fazer uma cópia de servidores de fim e bloquear outros tipos de tráfego por predefinição.

Saiba mais:

* Para obter mais informações sobre o conjunto completo de front-Azure porta capacidades, pode rever o [descrição geral de porta de entrada do Azure](../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Gestor de tráfego do Azure

O Gestor de Tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que lhe permite distribuir o tráfego de forma otimizada para serviços nas regiões globais do Azure, proporcionando, ao mesmo tempo, elevada disponibilidade e capacidade de resposta. O Gestor de Tráfego utiliza o DNS para direcionar os pedidos do cliente para o ponto final de serviço mais adequado com base num método de encaminhamento de tráfego e no estado de funcionamento dos pontos finais. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure. O Gestor de tráfego monitoriza os pontos finais e não a direcionar o tráfego para pontos finais que não estão disponíveis.

Saiba mais:

* [Descrição geral de Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitorização e deteção de ameaças

O Azure fornece recursos para ajudá-lo nessa área de chaves com detecção antecipada, monitorização, e recolher e analisar o tráfego de rede.

### <a name="azure-network-watcher"></a>Observador de rede do Azure

O observador de rede do Azure pode ajudá-lo a resolver problemas e fornece um novo conjunto de ferramentas para auxiliar na identificação de problemas de segurança.

[Vista de grupo de segurança](../network-watcher/network-watcher-security-group-view-overview.md) ajuda a conformidade de auditoria e segurança de máquinas virtuais. Utilize esta funcionalidade para efetuar auditorias programáticas, comparando as políticas de linha de base definidas pela sua organização para as regras em vigor para cada uma das suas VMs. Isto pode ajudar a identificar qualquer descompassos de configuração.

[Captura de pacotes](../network-watcher/network-watcher-packet-capture-overview.md) permite capturar o tráfego de rede de e para a máquina virtual. Pode coletar estatísticas de rede e resolver problemas de aplicações, que podem ser inestimáveis na investigação de intrusão de rede. Também pode utilizar esta funcionalidade em conjunto com as funções do Azure para iniciar as capturas de rede em resposta aos alertas do Azure específicos.

Para obter mais informações sobre o observador de rede e como começar a testar algumas das funcionalidades nos seus laboratórios, consulte [descrição geral da monitorização de observador de rede do Azure](../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Para as notificações mais atualizadas sobre a disponibilidade e o estado deste serviço, consulte a [página de atualizações do Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Centro de Segurança do Azure

Centro de segurança do Azure ajuda-o a prevenir, detetar e responder a ameaças e fornece que maior visibilidade e controle sobre, a segurança dos seus recursos do Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um conjunto de soluções de segurança.

Centro de segurança ajuda-o a otimizar e monitorizar a segurança de rede por:

* Fornecendo recomendações de segurança de rede.
* Monitorizar o estado da sua configuração de segurança de rede.
* Alerta-o à rede com base em ameaças, os dois níveis de ponto final e de rede.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>TAP de Rede Virtual

TOQUE (ponto de acesso de Terminal) de rede virtual do Azure permite-lhe para o stream continuamente o tráfego de rede de máquina virtual para uma ferramenta de recoletor ou análise da pacotes de rede. O recoletor ou a ferramenta de análise é fornecida por um parceiro de aplicação virtual de rede. Pode utilizar a mesma rede virtual recursos de TOQUE para agregar o tráfego de várias interfaces de rede nas subscrições idêntica ou diferentes.

Saiba mais:

* [TESTE de rede virtual](../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Registo

Registro em log num nível de rede é uma função de chave para qualquer cenário de segurança de rede. No Azure, pode se conectar informações obtidas para NSGs obter informações de registo de nível de rede. Com o Registro em log NSG, obtém informações a partir de:

* [Registos de atividades](../azure-monitor/platform/activity-logs-overview.md). Utilize estes registos para ver todas as operações submetidas às suas subscrições do Azure. Estes registos estão ativados por predefinição e podem ser utilizados no portal do Azure. Eles foram anteriormente conhecidos como auditoria ou registos operacionais.
* Registos de eventos. Estes registos fornecem informações sobre as regras do NSG que foram aplicadas.
* Logs de contador. Estes registos permitem-lhe saber quantas vezes cada regra NSG foi aplicada ao negar ou permitir tráfego.

Também pode utilizar [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), uma ferramenta de visualização de dados poderosas, para ver e analisar estes registos.
Saiba mais:

* [Registos de Monitor do Azure para grupos de segurança de rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)
