---
title: Conceitos e requisitos de segurança da rede em Azure | Microsoft Docs
description: Este artigo fornece explicações básicas sobre conceitos e requisitos de segurança da rede fundamentais, e informações sobre o que a Azure oferece em cada uma destas áreas.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 4fb4981925593a22ed65df9d3ed0da5f45062f23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578930"
---
# <a name="azure-network-security-overview"></a>Descrição geral da segurança de rede do Azure

A segurança da rede poderia ser definida como o processo de proteção dos recursos contra o acesso ou ataque não autorizados, aplicando controlos ao tráfego de rede. O objetivo é garantir que apenas o tráfego legítimo seja permitido. O Azure inclui uma infraestrutura robusta de rede para suportar os requisitos de conectividade de aplicação e serviço. A conectividade da rede é possível entre os recursos localizados em Azure, entre os recursos alojados no local e a Azure, e de e para a internet e Azure.

Este artigo abrange algumas das opções que a Azure oferece na área da segurança da rede. Pode aprender sobre:

* Rede Azure
* Controlo de acesso à rede
* Azure Firewall
* Acesso remoto seguro e conectividade transversal
* Disponibilidade
* Resolução de nomes
* Arquitetura da rede de perímetro (DMZ)
* Proteção contra DDoS do Azure
* Azure Front Door
* Gestor de tráfego
* Monitorização e deteção de ameaças

## <a name="azure-networking"></a>Rede Azure

O Azure requer que as máquinas virtuais estejam ligadas a uma Rede Virtual Azure. Uma rede virtual é uma construção lógica construída em cima do tecido físico da rede Azure. Cada rede virtual está isolada de todas as outras redes virtuais. Isto ajuda a garantir que o tráfego de rede nas suas implementações não seja acessível a outros clientes Azure.

Saiba mais:

* [Descrição geral da rede virtual](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Controlo de acesso à rede

O controlo do acesso à rede é o ato de limitar a conectividade de e para dispositivos ou subesí redes específicos dentro de uma rede virtual. O objetivo do controlo de acesso à rede é limitar o acesso às suas máquinas e serviços virtuais a utilizadores e dispositivos aprovados. Os controlos de acesso baseiam-se em decisões que permitem ou negam ligações de e para a sua máquina ou serviço virtual.

O Azure suporta vários tipos de controlo de acesso à rede, tais como:

* Controlo da camada de rede
* Controlo de rotas e túneis forçados
* Aparelhos de segurança de rede virtual

### <a name="network-layer-control"></a>Controlo da camada de rede

Qualquer implementação segura requer alguma medida de controlo de acesso à rede. O objetivo do controlo de acesso à rede é restringir a comunicação de máquinas virtuais aos sistemas necessários. Outras tentativas de comunicação estão bloqueadas.

> [!NOTE]
> As firewalls de armazenamento estão cobertas pelo artigo [de segurança de armazenamento Azure](../../storage/blobs/security-recommendations.md)

#### <a name="network-security-rules-nsgs"></a>Regras de segurança da rede (NSGs)

Se precisar de um controlo básico de acesso ao nível da rede (com base no endereço IP e nos protocolos TCP ou UDP), pode utilizar grupos de segurança de rede (NSGs). Um NSG é uma firewall de filtragem de pacotes básicos, imponentes, e permite-lhe controlar o acesso com base num [5-tuple](https://www.techopedia.com/definition/28190/5-tuple). Os NSGs incluem funcionalidade para simplificar a gestão e reduzir as chances de erros de configuração:

* **As regras de segurança aumentadas** simplificam a definição de regras NSG e permitem criar regras complexas em vez de ter de criar várias regras simples para obter o mesmo resultado.
* **As etiquetas de** serviço são etiquetas criadas pela Microsoft que representam um grupo de endereços IP. Atualizam-se dinamicamente para incluir gamas de IP que satisfaçam as condições que definem a inclusão no rótulo. Por exemplo, se quiser criar uma regra que se aplique a todo o armazenamento Azure na região leste, pode utilizar o Storage.EastUS
* **Os grupos de segurança** de aplicações permitem-lhe implantar recursos para grupos de aplicação e controlar o acesso a esses recursos, criando regras que utilizam esses grupos de aplicações. Por exemplo, se tiver webservers implantados no grupo de aplicação 'Webservers' pode criar uma regra que aplica um NSG permitindo 443 tráfego da Internet para todos os sistemas do grupo de aplicação 'Webservers'.

Os NSGs não fornecem inspeção da camada de aplicação ou controlos de acesso autenticados.

Saiba mais:

* [Grupos de Segurança de Rede](../../virtual-network/network-security-groups-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC mesmo a tempo do acesso vm

[O centro de segurança Azure](../../security-center/security-center-introduction.md) pode gerir os NSGs em VMs e bloquear o acesso ao VM até que um utilizador com o controlo de acesso adequado baseado em funções [Azure RBAC](../../role-based-access-control/overview.md) solicite acesso. Quando o utilizador é autorizado com sucesso, a ASC faz modificações nos NSGs para permitir o acesso a portas selecionadas durante o tempo especificado. Quando o tempo expirar, os NSGs são restaurados ao seu estado de segurança anterior.

Saiba mais:

* [Centro de Segurança Azure Logo no Acesso ao Tempo](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Pontos finais de serviço

Os pontos finais de serviço são outra forma de aplicar o controlo sobre o seu tráfego. Pode limitar a comunicação com serviços suportados apenas aos seus VNets sobre uma ligação direta. O tráfego do seu VNet para o serviço Azure especificado permanece na rede de espinha dorsal do Microsoft Azure.  

Saiba mais:

* [Pontos finais de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Controlo de rotas e túneis forçados

A capacidade de controlar o comportamento de encaminhamento nas suas redes virtuais é fundamental. Se o encaminhamento estiver configurado incorretamente, as aplicações e serviços alojados na sua máquina virtual poderão ligar-se a dispositivos não autorizados, incluindo sistemas detidos e operados por potenciais atacantes.

A azure networking suporta a capacidade de personalizar o comportamento de encaminhamento para o tráfego de rede nas suas redes virtuais. Isto permite-lhe alterar as entradas de tabela de encaminhamento predefinidos na sua rede virtual. O controlo do comportamento de encaminhamento ajuda-o a certificar-se de que todo o tráfego de um determinado dispositivo ou grupo de dispositivos entra ou deixa a sua rede virtual através de um local específico.

Por exemplo, pode ter um aparelho de segurança de rede virtual na sua rede virtual. Você deve ter certeza de que todo o tráfego de e para a sua rede virtual passa por esse aparelho de segurança virtual. Pode fazê-lo configurando [as Rotas Definidas](../../virtual-network/virtual-networks-udr-overview.md) pelo Utilizador (UDRs) em Azure.

[O túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para garantir que os seus serviços não estão autorizados a iniciar uma ligação a dispositivos na internet. Note que isto é diferente de aceitar ligações recebidas e, em seguida, responder a elas. Os servidores web front-end precisam de responder a pedidos de anfitriões de internet, e assim o tráfego de origem na Internet é permitido a entrada nestes servidores web e os servidores da web são autorizados a responder.

O que não quer é permitir que um servidor web frontal inicie um pedido de saída. Tais pedidos podem representar um risco de segurança, uma vez que estas ligações podem ser usadas para descarregar malware. Mesmo que pretenda que estes servidores frontais iniciem pedidos de saída para a internet, talvez queira forçá-los a passar pelos seus proxies web no local. Isto permite-lhe tirar partido da filtragem e registo de URL.

Em vez disso, gostaria de usar um túnel forçado para evitar isto. Quando permite a escavação forçada, todas as ligações à internet são forçadas através do seu portal no local. Pode configurar um túnel forçado aproveitando-se das UDRs.

Saiba mais:

* [Quais são as rotas definidas pelo utilizador e o encaminhamento IP](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Aparelhos de segurança de rede virtual

Enquanto os NSGs, UDRs e túneis forçados fornecem-lhe um nível de segurança na rede e camadas de transporte do [modelo OSI,](https://en.wikipedia.org/wiki/OSI_model)você também pode querer ativar a segurança em níveis superiores à rede.

Por exemplo, os seus requisitos de segurança podem incluir:

* Autenticação e autorização antes de permitir o acesso à sua aplicação
* Deteção de intrusões e resposta à intrusão
* Inspeção da camada de aplicação para protocolos de alto nível
* Filtragem de URL
* Antivírus e Antimalware de nível de rede
* Proteção anti-bot
* Controlo de acesso a aplicações
* Proteção adicional do DDoS (acima da proteção DDoS fornecida pelo próprio tecido Azure)

Pode aceder a estas funcionalidades de segurança de rede melhoradas utilizando uma solução parceira Azure. Pode encontrar as soluções de segurança de rede de parceiros Azure mais atuais, visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/)e procurando "segurança" e "segurança na rede".

## <a name="azure-firewall"></a>Azure Firewall

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É uma firewall com total monitoração de estado como um serviço com elevada disponibilidade incorporada e escalabilidade da cloud sem restrições. Algumas funcionalidades incluem:

* Elevada disponibilidade
* Escalabilidade da nuvem
* Regras de filtragem de FQDN de aplicação
* Regras de filtragem de tráfego de rede

Saiba mais:

* [Visão geral do Azure Firewall](../../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Acesso remoto seguro e conectividade transversal

A configuração, configuração e gestão dos seus recursos Azure tem de ser feita remotamente. Além disso, é melhor implementar soluções [híbridas](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) de TI que tenham componentes no local e na nuvem pública Azure. Estes cenários requerem acesso remoto seguro.

A ligação em rede Azure suporta os seguintes cenários de acesso remoto seguros:

* Ligar estações de trabalho individuais a uma rede virtual
* Ligue a sua rede no local a uma rede virtual com uma VPN
* Ligue a sua rede no local a uma rede virtual com uma ligação WAN dedicada
* Ligue redes virtuais entre si

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Ligar estações de trabalho individuais a uma rede virtual

É melhor permitir que desenvolvedores individuais ou pessoal de operações gerem máquinas e serviços virtuais em Azure. Por exemplo, digamos que precisa de acesso a uma máquina virtual numa rede virtual. Mas a sua política de segurança não permite o acesso remoto RDP ou SSH a máquinas virtuais individuais. Neste caso, pode utilizar uma ligação [VPN ponto a local.](../../vpn-gateway/point-to-site-about.md)

A ligação VPN ponto-a-local permite-lhe configurar uma ligação privada e segura entre o utilizador e a rede virtual. Quando a ligação VPN estiver estabelecida, o utilizador pode pDR ou SSH sobre a ligação VPN em qualquer máquina virtual da rede virtual. (Isto pressupõe que o utilizador pode autenticar e está autorizado.) Suportes VPN ponto a local:

* Secure Socket Tunneling Protocol (SSTP), um protocolo VPN baseado em SSL. Uma solução SSL VPN pode penetrar firewalls, uma vez que a maioria das firewalls abrem a porta TCP 443, que o TLS/SSL utiliza. O SSTP só é suportado em dispositivos Windows. O Azure suporta todas as versões do Windows que tenham SSTP (Windows 7 e mais tarde).

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Saiba mais:

* [Configure uma ligação ponto-a-local a uma rede virtual utilizando o PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Ligue a sua rede no local a uma rede virtual com uma VPN

Talvez queira ligar toda a sua rede corporativa, ou partes dela, a uma rede virtual. Isto é comum em cenários de TI híbridos, onde as organizações [estendem o seu datacenter no local para a Azure.](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84) Em muitos casos, as organizações acolhem partes de um serviço em Azure, e partes no local. Por exemplo, podem fazê-lo quando uma solução inclui servidores web front-end em Azure e bases de dados de back-end no local. Estes tipos de ligações "cross-premises" também tornam a gestão dos recursos localizados aZure mais segura, e permitem cenários como o alargamento dos controladores de domínio do Diretório Ativo em Azure.

Uma maneira de o conseguir é utilizar uma [VPN site-to-site.](https://www.techopedia.com/definition/30747/site-to-site-vpn) A diferença entre uma VPN local e uma VPN ponto-a-local é que este último liga um único dispositivo a uma rede virtual. Uma VPN site-to-site liga toda uma rede (como a sua rede no local) a uma rede virtual. As VPNs do site para uma rede virtual utilizam o protocolo VPN do modo de túnel IPsec altamente seguro.

Saiba mais:

* [Criar um VNet gestor de recursos com uma ligação VPN site-to-site utilizando o portal Azure](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [Acerca do VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Ligue a sua rede no local a uma rede virtual com uma ligação WAN dedicada

As ligações VPN ponto-a-local e local-a-local são eficazes para permitir a conectividade entre instalações. No entanto, algumas organizações consideram-nos como inconvenientes:

* As ligações VPN movem dados pela internet. Isto expõe estas ligações a potenciais problemas de segurança envolvidos na movimentação de dados por uma rede pública. Além disso, a fiabilidade e disponibilidade para ligações à Internet não podem ser garantidas.
* As ligações VPN a redes virtuais podem não ter a largura de banda para algumas aplicações e propósitos, uma vez que atingem o máximo em cerca de 200 Mbps.

As organizações que precisam do mais alto nível de segurança e disponibilidade para as suas ligações inter-instalações normalmente usam links WAN dedicados para se conectarem a sites remotos. O Azure fornece-lhe a capacidade de usar um link WAN dedicado que pode usar para ligar a sua rede no local a uma rede virtual. Azure ExpressRoute, Rota Expresso direta, e Alcance global da rota Express permitem isso.

Saiba mais:

* [Descrição geral técnica do ExpressRoute](../../expressroute/expressroute-introduction.md)
* [ExpressRoute direto](../../expressroute/expressroute-erdirect-about.md)
* [Alcance global da rota expressa](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Ligue redes virtuais entre si

É possível utilizar muitas redes virtuais para as suas implementações. Há várias razões para fazer isto. Talvez queira simplificar a gestão, ou talvez queira um maior segurança. Independentemente da motivação para colocar recursos em diferentes redes virtuais, pode haver momentos em que se quer que os recursos em cada uma das redes se conectem entre si.

Uma opção é que os serviços numa rede virtual se conectem a serviços noutra rede virtual, "voltando atrás" através da internet. A ligação começa numa rede virtual, passa pela internet e depois volta para a rede virtual de destino. Esta opção expõe a ligação às questões de segurança inerentes a qualquer comunicação baseada na Internet.

Uma melhor opção pode ser criar uma VPN site-to-site que se conecta entre duas redes virtuais. Este método utiliza o mesmo protocolo do [modo de túnel IPSec](/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) que a ligação VPN local-local mencionada acima.

A vantagem desta abordagem é que a ligação VPN é estabelecida através do tecido da rede Azure, em vez de se ligar através da internet. Isto proporciona-lhe uma camada extra de segurança, em comparação com as VPNs site-to-site que se conectam através da internet.

Saiba mais:

* [Configure uma Ligação VNet-to-VNet utilizando o Azure Resource Manager e o PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Outra forma de ligar as suas redes virtuais é [o VNET a espreitar.](../../virtual-network/virtual-network-peering-overview.md) Esta funcionalidade permite-lhe ligar duas redes Azure para que a comunicação entre elas aconteça através da infraestrutura da espinha dorsal da Microsoft sem que esta nunca passe pela Internet. O estofo VNET pode ligar dois VNETs na mesma região ou dois VNETs em todas as regiões de Azure. Os NSGs podem ser usados para limitar a conectividade entre diferentes sub-redes ou sistemas.

## <a name="availability"></a>Disponibilidade

A disponibilidade é um componente chave de qualquer programa de segurança. Se os seus utilizadores e sistemas não conseguirem aceder ao que precisam para aceder em toda a rede, o serviço pode ser considerado comprometido. A Azure dispõe de tecnologias de networking que suportam os seguintes mecanismos de alta disponibilidade:

* Equilíbrio de carga baseado em HTTP
* Equilíbrio de carga de nível de rede
* Equilíbrio global da carga

O equilíbrio de carga é um mecanismo concebido para distribuir igualmente as ligações entre vários dispositivos. Os objetivos do equilíbrio da carga são:

* Para aumentar a disponibilidade. Quando carrega ligações de equilíbrio em vários dispositivos, um ou mais dispositivos podem ficar indisponíveis sem comprometer o serviço. Os serviços em execução nos restantes dispositivos online podem continuar a servir o conteúdo do serviço.
* Para aumentar o desempenho. Quando carrega ligações de equilíbrio em vários dispositivos, um único dispositivo não tem de lidar com todo o processamento. Em vez disso, o processamento e as exigências de memória para servir o conteúdo são espalhados por vários dispositivos.

### <a name="http-based-load-balancing"></a>Equilíbrio de carga baseado em HTTP

As organizações que gerem serviços baseados na Web muitas vezes desejam ter um equilibrador de carga baseado em HTTP na frente desses serviços web. Isto ajuda a garantir níveis adequados de desempenho e alta disponibilidade. Os equilibradores de carga tradicionais baseados em rede dependem de protocolos de rede e camadas de transporte. Os equilibradores de carga baseados em HTTP, por outro lado, tomam decisões com base nas características do protocolo HTTP.

O Azure Application Gateway fornece um equilíbrio de carga baseado em HTTP para os seus serviços baseados na Web. Suportes do Gateway de Aplicação:

* Afinidade da sessão baseada em cookies. Esta capacidade garante que as ligações estabelecidas a um dos servidores por trás desse balanceador de carga permanecem intactas entre o cliente e o servidor. Isto garante a estabilidade das transações.
* Descarga TLS. Quando um cliente se conecta com o equilibrador de carga, essa sessão é encriptada utilizando o protocolo HTTPS (TLS). No entanto, para aumentar o desempenho, pode utilizar o protocolo HTTP (não encriptado) para ligar entre o equilibrista de carga e o servidor web por trás do balançador de carga. Isto é referido como "descarga TLS", porque os servidores web por trás do balanceador de carga não experimentam a sobrecarga do processador envolvida com encriptação. Os servidores web podem, portanto, solicitar pedidos de serviço mais rapidamente.
* Encaminhamento de conteúdo baseado em URL. Esta funcionalidade permite ao equilibrador de carga tomar decisões sobre onde encaminhar as ligações com base no URL-alvo. Isto proporciona muito mais flexibilidade do que soluções que tomam decisões de equilíbrio de carga com base em endereços IP.

Saiba mais:

* [Descrição geral do Gateway de Aplicação](../../application-gateway/overview.md)

### <a name="network-level-load-balancing"></a>Equilíbrio de carga de nível de rede

Em contraste com o equilíbrio de carga baseado em HTTP, o equilíbrio da carga de nível de rede toma decisões com base nos números ip e porta (TCP ou UDP).
Pode obter os benefícios do equilíbrio de carga de nível de rede em Azure utilizando o Azure Load Balancer. Algumas características-chave do Balanceador de Carga incluem:

* Equilíbrio de carga de nível de rede com base no endereço IP e números de porta.
* Suporte para qualquer protocolo de camada de aplicação.
* Carregue os saldos das máquinas virtuais Azure e dos serviços de nuvem.
* Pode ser utilizado tanto para aplicações viradas para a Internet (equilíbrio de carga externa) como para aplicações não-internet (equilíbrio interno de carga) e máquinas virtuais.
* Monitorização de pontos finais, que é utilizada para determinar se algum dos serviços por trás do balançador de carga ficou indisponível.

Saiba mais:

* [Equilibrador de carga virado para a Internet entre várias máquinas ou serviços virtuais](../../load-balancer/load-balancer-overview.md)
* [Visão geral do balançador de carga interna](../../load-balancer/load-balancer-overview.md)

### <a name="global-load-balancing"></a>Equilíbrio global da carga

Algumas organizações querem o mais alto nível de disponibilidade possível. Uma forma de alcançar este objetivo é acolher aplicações em datacenters distribuídos globalmente. Quando uma aplicação é hospedada em datacenters localizados em todo o mundo, é possível que toda uma região geopolítica fique indisponível, e ainda tenha a aplicação em funcionamento.

Esta estratégia de equilíbrio de carga também pode trazer benefícios de desempenho. Pode direcionar os pedidos do serviço para o datacenter que está mais próximo do dispositivo que está a fazer o pedido.

Em Azure, você pode ganhar os benefícios do equilíbrio global de carga usando Azure Traffic Manager.

Saiba mais:

* [O que é o Gestor de Tráfego?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Resolução de nomes

A resolução de nomes é uma função crítica para todos os serviços que hospeda no Azure. Do ponto de vista da segurança, o compromisso da função de resolução de nomes pode levar a que um intruso redirecione pedidos dos seus sites para o site de um intruso. A resolução de nomes seguros é um requisito para todos os seus serviços de cloud hospedados.

Há dois tipos de resolução de nomes que precisa de abordar:

* Resolução de nome interno. Isto é utilizado por serviços nas suas redes virtuais, nas suas redes no local, ou em ambos. Os nomes utilizados para a resolução de nomes internos não são acessíveis através da internet. Para uma segurança ótima, é importante que o seu sistema interno de resolução de nomes não seja acessível a utilizadores externos.
* Resolução de nomes externos. Isto é usado por pessoas e dispositivos fora das suas redes no local e redes virtuais. Estes são os nomes que são visíveis para a internet, e são usados para direcionar a ligação aos seus serviços baseados na nuvem.

Para resolução de nomes internos, tem duas opções:

* Um servidor DNS de rede virtual. Quando cria uma nova rede virtual, é criado um servidor DNS para si. Este servidor DNS pode resolver os nomes das máquinas localizadas nessa rede virtual. Este servidor DNS não é configurável, é gerido pelo gestor de tecidos Azure, e pode, portanto, ajudá-lo a garantir a sua solução de resolução de nomes.
* Traga o seu próprio servidor DNS. Tem a opção de colocar um servidor DNS à sua escolha na sua rede virtual. Este servidor DNS pode ser um servidor DNS integrado ao Ative Directory ou uma solução de servidor DNS dedicada fornecida por um parceiro Azure, que pode obter a partir do Azure Marketplace.

Saiba mais:

* [Descrição geral da rede virtual](../../virtual-network/virtual-networks-overview.md)
* [Gerir servidores DNS utilizados por uma rede virtual](../../virtual-network/manage-virtual-network.md#change-dns-servers)

Para resolução de nomes externos, tem duas opções:

* Hospedar o seu próprio servidor DNS externo no local.
* Hospedar o seu próprio servidor DNS externo com um fornecedor de serviços.

Muitas grandes organizações acolhem os seus próprios servidores DNS no local. Podem fazê-lo porque têm a experiência de networking e a presença global para o fazer.

Na maioria dos casos, é melhor hospedar os seus serviços de resolução de nomes DNS com um prestador de serviços. Estes prestadores de serviços possuem a experiência da rede e a presença global para garantir uma disponibilidade muito elevada para os serviços de resolução do seu nome. A disponibilidade é essencial para os serviços DNS, porque se os serviços de resolução de nome falharem, ninguém poderá chegar aos seus serviços de internet.

O Azure fornece-lhe uma solução de DNS externo altamente disponível e de alto desempenho sob a forma de DNS Azure. Esta solução de resolução de nomes externos tira partido da infraestrutura global de DNS Azure. Permite-lhe hospedar o seu domínio em Azure, utilizando as mesmas credenciais, APIs, ferramentas e faturação que os seus outros serviços Azure. Como parte do Azure, também herda os fortes controlos de segurança incorporados na plataforma.

Saiba mais:

* [Visão geral do Azure DNS](../../dns/dns-overview.md)
* [As zonas privadas Azure DNS](../../dns/private-dns-overview.md) permitem configurar nomes privados de DNS para recursos Azure em vez dos nomes automaticamente atribuídos sem a necessidade de adicionar uma solução de DNS personalizada.

## <a name="perimeter-network-architecture"></a>Arquitetura de rede de perímetro

Muitas grandes organizações usam redes de perímetro para segmentar as suas redes, e criar uma zona-tampão entre a internet e os seus serviços. A parte do perímetro da rede é considerada uma zona de baixa segurança, e nenhum ativo de alto valor é colocado nesse segmento de rede. Normalmente, vemos dispositivos de segurança de rede que têm uma interface de rede no segmento da rede de perímetro. Outra interface de rede está ligada a uma rede que tem máquinas e serviços virtuais que aceitam ligações de entrada a partir da internet.

Pode projetar redes de perímetro de várias maneiras. A decisão de implantar uma rede de perímetro, e depois que tipo de rede de perímetro utilizar se decidir utilizar uma, depende dos requisitos de segurança da sua rede.

Saiba mais:

* [Microsoft Cloud Services e Segurança de Rede](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Proteção contra DDoS do Azure

Os ataques de Denial of Service distribuído (DDoS) são algumas das maiores preocupações de disponibilidade e segurança relativamente aos clientes que estão a mover as suas aplicações para a cloud. Um ataque DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para utilizadores legítimos. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet.
A Microsoft fornece uma proteção DDoS conhecida como **Basic** como parte da Plataforma Azure. Isto não é cobrado e inclui sempre a monitorização e a mitigação em tempo real de ataques comuns ao nível da rede. Além das proteções incluídas na proteção DDoS **Basic,** pode ativar a opção **Standard.** As funcionalidades padrão de proteção DDoS incluem:

* **Integração de plataformas nativas:** Integrado nativo no Azure. Inclui a configuração através do portal Azure. A DDoS Protection Standard compreende os seus recursos e a configuração dos recursos.
* **Proteção da chave-de-turno:** A configuração simplificada protege imediatamente todos os recursos numa rede virtual assim que o DDoS Protection Standard estiver ativado. Não é necessária qualquer intervenção ou definição de utilizador. A Norma de Proteção DDoS atenua instantaneamente e atenua automaticamente o ataque, uma vez detetado.
* **Monitorização do tráfego sempre ligado:** Os seus padrões de tráfego de aplicações são monitorizados 24 horas por dia, 7 dias por semana, à procura de indicadores de ataques de DDoS. A mitigação é efetuada quando as políticas de proteção são ultrapassadas.
* **Relatórios de Mitigação de Ataques** Os Relatórios de Mitigação de Ataques utilizam dados agregados de fluxo de rede para fornecer informações detalhadas sobre ataques direcionados aos seus recursos.
* **Registos de fluxo de mitigação de ataques** Os Registos de Fluxo de Mitigação de Ataques permitem-lhe rever o tráfego desatado, tráfego reencaminhado e outros dados de ataque em quase tempo real durante um ataque DDoS ativo.
* **Sintonização adaptativa:** O perfil de tráfego inteligente aprende o tráfego da sua aplicação ao longo do tempo e seleciona e atualiza o perfil que é o mais adequado para o seu serviço. O perfil ajusta-se à medida que o tráfego muda ao longo do tempo. Proteção da camada 3 à camada 7: Fornece proteção DDoS de pilha completa, quando utilizado com uma firewall de aplicação web.
* **Escala de mitigação extensiva:** Mais de 60 tipos de ataques diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques DDoS conhecidos.
* **Métricas de ataque:** As métricas resumidas de cada ataque são acessíveis através do Monitor Azure.
* **Alerta de ataque:** Os alertas podem ser configurados no início e na paragem de um ataque, e durante a duração do ataque, usando métricas de ataque incorporadas. Os alertas integram-se no seu software operacional como os registos do Microsoft Azure Monitor, Splunk, Azure Storage, Email e o portal Azure.
* **Garantia de custos:**  Créditos de serviço de transferência de dados e de aplicação para ataques DDoS documentados.
* **DDoS Resposta rápida** Os clientes DDoS Protection Standard têm agora acesso à equipa de Resposta Rápida durante um ataque ativo. A DRR pode ajudar na investigação de ataque, atenuações personalizadas durante um ataque e análise pós-ataque.


Saiba mais:

* [Visão geral da proteção do DDOS](../../ddos-protection/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

O Azure Front Door Service permite-lhe definir, gerir e monitorizar o encaminhamento global do seu tráfego web. Otimiza o encaminhamento do seu tráfego para melhor desempenho e alta disponibilidade. O Azure Front Door permite criar regras de firewall de aplicações Web (WAF) personalizadas para o controlo de acesso de forma a proteger a carga de trabalho HTTP/HTTPS da exploração com base em endereços IP de cliente, no indicativo e em parâmetros http. Além disso, a Porta Frontal também permite criar regras de limitação de tarifas para combater o tráfego malicioso de bots, inclui descarregamento de TLS e pedido por HTTP/HTTPS, processamento de camadas de aplicação.

A plataforma do Front Door encontra-se protegida pela proteção básica do Azure DDoS Protection. Para aumentar a proteção, é possível ativar a proteção padrão do Azure DDoS Protection nas suas VNETs e recursos de salvaguarda contra ataques de camada de rede (TCP/UDP) através de ajuste automático e mitigação. A Porta Frontal é um proxy inverso de camada 7, que apenas permite que o tráfego web passe para servidores de extremidade traseira e bloqueie outros tipos de tráfego por padrão.

Saiba mais:

* Para mais informações sobre todo o conjunto de capacidades da porta Azure Front, pode rever a [visão geral da Porta frontal do Azure](../../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Gestor de Tráfego Azure

O Gestor de Tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que lhe permite distribuir o tráfego de forma otimizada para serviços nas regiões globais do Azure, proporcionando, ao mesmo tempo, elevada disponibilidade e capacidade de resposta. O Gestor de Tráfego utiliza o DNS para direcionar os pedidos do cliente para o ponto final de serviço mais adequado com base num método de encaminhamento de tráfego e no estado de funcionamento dos pontos finais. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure. O gestor de tráfego monitoriza os pontos finais e não direciona o tráfego para quaisquer pontos finais que não estejam disponíveis.

Saiba mais:

* [Visão geral do gestor de tráfego de Azure](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitorização e deteção de ameaças

O Azure fornece capacidades para o ajudar nesta área-chave com deteção, monitorização e recolha e revisão do tráfego de rede.

### <a name="azure-network-watcher"></a>Observador de Rede do Azure

O Azure Network Watcher pode ajudá-lo a resolver problemas e fornece um novo conjunto de ferramentas para ajudar na identificação de problemas de segurança.

[A Security Group View](../../network-watcher/network-watcher-security-group-view-overview.md) ajuda na auditoria e conformidade de segurança das Máquinas Virtuais. Utilize esta funcionalidade para realizar auditorias programáticas, comparando as políticas de base definidas pela sua organização com regras eficazes para cada um dos seus VMs. Isto pode ajudá-lo a identificar qualquer deriva de configuração.

[A captura de pacotes](../../network-watcher/network-watcher-packet-capture-overview.md) permite-lhe capturar tráfego de rede de e para a máquina virtual. Pode recolher estatísticas de rede e problemas de aplicação de resolução de problemas, que podem ser inestimáveis na investigação de intrusões na rede. Também pode utilizar esta funcionalidade juntamente com as Funções Azure para iniciar capturas de rede em resposta a alertas específicos do Azure.

Para obter mais informações sobre o Network Watcher e como começar a testar algumas das funcionalidades nos seus laboratórios, consulte [a visão geral do monitor de rede Azure](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Para obter as notificações mais atualizadas sobre disponibilidade e estado deste serviço, consulte a página de atualizações do [Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Centro de Segurança do Azure

O Centro de Segurança Azure ajuda-o a prevenir, detetar e responder a ameaças, e fornece-lhe uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições Azure, ajuda a detetar ameaças que de outra forma poderiam passar despercebidas, e trabalha com um grande conjunto de soluções de segurança.

O Security Center ajuda-o a otimizar e monitorizar a segurança da rede através de:

* Fornecendo recomendações de segurança de rede.
* Monitorização do estado da sua configuração de segurança de rede.
* Alertando-o para ameaças baseadas em rede, tanto no ponto final como nos níveis de rede.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](../../security-center/security-center-introduction.md)

### <a name="virtual-network-tap"></a>Rede Virtual TAP

A rede virtual Azure TAP (Terminal Access Point) permite-lhe transmitir continuamente o tráfego da rede de máquinas virtuais para um coletor de pacotes de rede ou uma ferramenta de análise. A ferramenta de coletor ou de análise é fornecida por um parceiro de aparelho virtual de rede. Pode utilizar o mesmo recurso de rede virtual TAP para agregar tráfego de várias interfaces de rede nas mesmas ou diferentes subscrições.

Saiba mais:

* [TAP de rede virtual](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Registo

Registar-se a nível de rede é uma função-chave para qualquer cenário de segurança da rede. Em Azure, pode registar informações obtidas para os NSGs para obter informações de registo de nível de rede. Com o registo do NSG, obtém informações a partir de:

* [Registos de atividade](../../azure-monitor/essentials/platform-logs-overview.md). Utilize estes registos para visualizar todas as operações submetidas às suas subscrições Azure. Estes registos são ativados por padrão e podem ser utilizados dentro do portal Azure. Eram anteriormente conhecidos como registos de auditoria ou de funcionamento.
* Registos de eventos. Estes registos fornecem informações sobre as regras da NSG aplicadas.
* Registos de contadores. Estes registos permitem-lhe saber quantas vezes cada regra NSG foi aplicada para negar ou permitir o tráfego.

Também pode utilizar [o Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), uma poderosa ferramenta de visualização de dados, para visualizar e analisar estes registos.
Saiba mais:

* [Registos do Monitor Azure para grupos de segurança de rede (NSGs)](../../virtual-network/virtual-network-nsg-manage-log.md)