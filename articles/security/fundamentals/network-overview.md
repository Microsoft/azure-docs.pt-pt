---
title: Conceitos e requisitos de segurança da rede em Azure Microsoft Docs
description: Este artigo fornece explicações básicas sobre conceitos e requisitos fundamentais de segurança da rede, e informações sobre o que o Azure oferece em cada uma destas áreas.
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
ms.openlocfilehash: 2293618b0685fe71ae553a95797fe8bfe1fe968c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749948"
---
# <a name="azure-network-security-overview"></a>Visão geral da segurança da rede Azure

A segurança da rede poderia ser definida como o processo de proteção dos recursos contra acesso ou ataque não autorizado, aplicando controlos ao tráfego da rede. O objetivo é garantir que apenas o tráfego legítimo seja permitido. O Azure inclui uma robusta infraestrutura de networking para suportar os requisitos de conectividade de aplicação e serviço. A conectividade da rede é possível entre os recursos localizados no Azure, entre as instalações e o Azure hospedados recursos, e de e para a internet e Azure.

Este artigo aborda algumas das opções que o Azure oferece na área da segurança da rede. Pode aprender sobre:

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

O Azure exige que as máquinas virtuais sejam ligadas a uma Rede Virtual Azure. Uma rede virtual é uma construção lógica construída em cima do tecido de rede azul a física. Cada rede virtual está isolada de todas as outras redes virtuais. Isto ajuda a garantir que o tráfego de rede nas suas implementações não é acessível a outros clientes Azure.

Saiba mais:

* [Visão geral da rede virtual](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Controlo de acesso à rede

O controlo de acesso à rede é o ato de limitar a conectividade de e para dispositivos ou subredes específicos dentro de uma rede virtual. O objetivo do controlo de acesso à rede é limitar o acesso às suas máquinas e serviços virtuais a utilizadores e dispositivos aprovados. Os controlos de acesso baseiam-se em decisões de permitir ou negar ligações de e para a sua máquina virtual ou serviço.

O Azure suporta vários tipos de controlo de acesso à rede, tais como:

* Controlo da camada de rede
* Controlo de rotas e túneis forçados
* Aparelhos de segurança de rede virtuais

### <a name="network-layer-control"></a>Controlo da camada de rede

Qualquer implementação segura requer alguma medida de controlo de acesso à rede. O objetivo do controlo de acesso à rede é restringir a comunicação virtual de máquinas aos sistemas necessários. Outras tentativas de comunicação estão bloqueadas.

> [!NOTE]
> Firewalls de armazenamento estão cobertos no artigo de segurança de [armazenamento Azure](storage-overview.md)

#### <a name="network-security-rules-nsgs"></a>Regras de segurança da rede (NSGs)

Se necessitar de um controlo básico do nível de rede (com base no endereço IP e nos protocolos TCP ou UDP), pode utilizar grupos de segurança de rede (NSGs). Um NSG é uma firewall básica, imponente e filtrante de pacotes, e permite controlar o acesso com base num [túnica de 5-tuple](https://www.techopedia.com/definition/28190/5-tuple). Os NSGs incluem funcionalidade sinuosa mente para simplificar a gestão e reduzir as chances de erros de configuração:

* **As regras de segurança aumentadas** simplificam a definição de regras do NSG e permitem-lhe criar regras complexas em vez de ter de criar múltiplas regras simples para obter o mesmo resultado.
* **As etiquetas** de serviço são etiquetas criadas pela Microsoft que representam um grupo de endereços IP. Atualizam dinamicamente para incluir gamas IP que vão ao encontro das condições que definem a inclusão no rótulo. Por exemplo, se quiser criar uma regra que se aplique a todo o armazenamento azure na região leste, pode usar Storage.EastUS
* **Os grupos** de segurança de aplicações permitem-lhe mobilizar recursos para grupos de aplicação e controlar o acesso a esses recursos através da criação de regras que utilizam esses grupos de aplicações. Por exemplo, se tiver webservers implantados no grupo de aplicações 'Webservers', pode criar uma regra que aplica um NSG permitindo o tráfego de 443 da Internet para todos os sistemas do grupo de aplicações 'Webservers'.

Os NSGs não fornecem inspeção da camada de aplicação ou controlos de acesso autenticados.

Saiba mais:

* [Grupos de Segurança de Rede](../../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC mesmo a tempo o acesso vM

O [centro de segurança Azure](../../security-center/security-center-intro.md) pode gerir os NSGs em VMs e bloquear o acesso ao VM até que um utilizador com o controlo de acesso baseado em funções apropriado [solicita](/azure/role-based-access-control/overview) acesso. Quando o utilizador é autorizado com sucesso, o ASC faz modificações nas NSGs para permitir o acesso a portas selecionadas durante o tempo especificado. Quando o tempo expirar, os NSGs são restaurados ao seu estado anterior seguro.

Saiba mais:

* [Centro de Segurança Azure apenas no acesso ao tempo](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Pontos finais de serviço

Os pontos finais do serviço são outra forma de aplicar o controlo sobre o seu tráfego. Pode limitar a comunicação com serviços suportados apenas aos seus VNets sobre uma ligação direta. O tráfego do seu VNet para o serviço Azure especificado permanece na rede de espinha dorsal do Microsoft Azure.  

Saiba mais:

* [Pontos finais de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Controlo de rotas e túneis forçados

A capacidade de controlar o comportamento de encaminhamento nas suas redes virtuais é fundamental. Se o encaminhamento for configurado incorretamente, as aplicações e serviços hospedados na sua máquina virtual podem ligar-se a dispositivos não autorizados, incluindo sistemas de propriedade e operados por potenciais atacantes.

A rede Azure suporta a capacidade de personalizar o comportamento de encaminhamento para o tráfego de rede nas suas redes virtuais. Isto permite alterar as entradas de tabela de encaminhamento predefinidos na sua rede virtual. O controlo do comportamento de encaminhamento ajuda-o a certificar-se de que todo o tráfego de um determinado dispositivo ou grupo de dispositivos entra ou sai da sua rede virtual através de uma localização específica.

Por exemplo, pode ter um aparelho de segurança de rede virtual na sua rede virtual. Certifique-se de que todo o tráfego de e para a sua rede virtual passa por aquele aparelho de segurança virtual. Pode fazê-lo configurando [rotas definidas](../../virtual-network/virtual-networks-udr-overview.md) pelo utilizador (UDRs) em Azure.

[A escavação forçada](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para garantir que os seus serviços não estão autorizados a iniciar uma ligação a dispositivos na internet. Note que isto é diferente de aceitar ligações de entrada e, em seguida, responder a elas. Os servidores web front-end precisam responder aos pedidos dos anfitriões da Internet, pelo que o tráfego de origem na Internet é permitido entrar nestes servidores web e os servidores web são autorizados a responder.

O que não quer permitir é um servidor web frontal para iniciar um pedido de saída. Tais pedidos podem representar um risco de segurança porque estas ligações podem ser usadas para descarregar malware. Mesmo que queira que estes servidores front-end iniciem pedidos de saída para a internet, é melhor forçá-los a passar pelos seus proxies web no local. Isto permite-lhe tirar partido da filtragem e registo de URL.

Em vez disso, gostaria de usar túneis forçados para evitar isto. Quando permite túneis forçados, todas as ligações à internet são forçadas através da sua porta de entrada no local. Pode configurar túneis forçados aproveitando-se de UDRs.

Saiba mais:

* [O que são Rotas Definidas pelo Utilizador e Reencaminhamento IP](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Aparelhos de segurança de rede virtuais

Enquanto nsGs, UDRs e túneis forçados fornecem-lhe um nível de segurança na rede e camadas de transporte do [modelo OSI,](https://en.wikipedia.org/wiki/OSI_model)você também pode querer permitir a segurança em níveis mais altos que a rede.

Por exemplo, os seus requisitos de segurança podem incluir:

* Autenticação e autorização antes de permitir o acesso à sua aplicação
* Deteção de intrusão e resposta à intrusão
* Inspeção da camada de aplicação para protocolos de alto nível
* Filtragem de URL
* Antivírus de nível de rede e antimalware
* Proteção anti-bot
* Controlo de acesso a aplicações
* Proteção adicional do DDoS (acima da proteção DDoS fornecida pelo próprio tecido Azure)

Pode aceder a estas funcionalidades de segurança de rede melhoradas utilizando uma solução de parceiro Azure. Pode encontrar as soluções de segurança de rede de parceiros mais atuais do Azure visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/), e procurando "segurança" e "segurança de rede".

## <a name="azure-firewall"></a>Azure Firewall

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É uma firewall com total monitoração de estado como um serviço com elevada disponibilidade incorporada e escalabilidade da cloud sem restrições. Algumas funcionalidades incluem:

* Elevada disponibilidade
* Escalabilidade das nuvens
* Regras de filtragem de FQDN de aplicação
* Regras de filtragem de tráfego de rede

Saiba mais:

* [Visão geral da Firewall Azure](/azure/firewall/overview)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Acesso remoto seguro e conectividade transversal

A configuração, configuração e gestão dos seus recursos Azure têm de ser feitas remotamente. Além disso, é melhor utilizar soluções [híbridas de TI](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) que tenham componentes no local e na nuvem pública de Azure. Estes cenários requerem acesso remoto seguro.

A rede Azure suporta os seguintes cenários seguros de acesso remoto:

* Ligar estações de trabalho individuais a uma rede virtual
* Ligue a sua rede no local a uma rede virtual com uma VPN
* Ligue a sua rede no local a uma rede virtual com uma ligação WAN dedicada
* Ligar redes virtuais entre si

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Ligar estações de trabalho individuais a uma rede virtual

É melhor permitir que desenvolvedores individuais ou pessoal de operações gerem máquinas e serviços virtuais no Azure. Por exemplo, digamos que precisa de acesso a uma máquina virtual numa rede virtual. Mas a sua política de segurança não permite o acesso remoto rdp ou SSH a máquinas virtuais individuais. Neste caso, pode utilizar uma ligação [VPN ponto-a-local.](../../vpn-gateway/point-to-site-about.md)

A ligação VPN ponto-a-site permite-lhe configurar uma ligação privada e segura entre o utilizador e a rede virtual. Quando a ligação VPN é estabelecida, o utilizador pode RDP ou SSH através da ligação VPN em qualquer máquina virtual da rede virtual. (Isto pressupõe que o utilizador pode autenticar e está autorizado.) Suportes VPN ponto-a-local:

* Protocolo de Túnel de Tomada Segura (SSTP), um protocolo VPN baseado em SSL. Uma solução SSL VPN pode penetrar firewalls, uma vez que a maioria das firewalls abrem a porta TCP 443, que o SSL utiliza. O SSTP só é suportado em dispositivos Windows. O Azure suporta todas as versões do Windows que tenham SSTP (Windows 7 e mais tarde).

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Saiba mais:

* [Configure uma ligação ponto-a-local a uma rede virtual usando powerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Ligue a sua rede no local a uma rede virtual com uma VPN

Talvez queira ligar toda a sua rede corporativa, ou partes dela, a uma rede virtual. Isto é comum em cenários de TI híbridos, onde as organizações [estendem o seu centro de dados no local para o Azure.](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84) Em muitos casos, as organizações acolhem partes de um serviço em Azure, e partes no local. Por exemplo, podem fazê-lo quando uma solução inclui servidores web front-end em Azure e bases de dados de back-end no local. Este tipo de conexões "cross-premises" também tornam a gestão dos recursos localizados do Azure mais seguro, e permitem cenários como o alargamento dos controladores de domínio ative diretório sinuoso para o Azure.

Uma maneira de o conseguir é usar uma [VPN site-to-site](https://www.techopedia.com/definition/30747/site-to-site-vpn). A diferença entre uma VPN site-a-site e uma VPN ponto-a-site é que este último liga um único dispositivo a uma rede virtual. Uma VPN site-to-site liga toda uma rede (como a sua rede no local) a uma rede virtual. VPNs site-to-site para uma rede virtual usam o protocolo VPN modo de túnel IPsec altamente seguro.

Saiba mais:

* [Criar um Gestor de Recursos VNet com uma ligação VPN site-to-site usando o portal Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Acerca do VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Ligue a sua rede no local a uma rede virtual com uma ligação WAN dedicada

As ligações VPN ponto-a-local e local-a-local são eficazes para permitir a conectividade transversal. No entanto, algumas organizações consideram-nas como as seguintes desvantagens:

* As ligações VPN movem dados pela internet. Isto expõe estas ligações a potenciais problemas de segurança envolvidos na transferência de dados para uma rede pública. Além disso, a fiabilidade e disponibilidade para ligações à Internet não podem ser garantidas.
* As ligações VPN a redes virtuais podem não ter a largura de banda para algumas aplicações e propósitos, uma vez que são máximas em cerca de 200 Mbps.

As organizações que necessitam do mais alto nível de segurança e disponibilidade para as suas ligações transversais normalmente usam ligações WAN dedicadas para se conectarem a sites remotos. O Azure oferece-lhe a capacidade de utilizar uma ligação WAN dedicada que pode utilizar para ligar a sua rede no local a uma rede virtual. O Azure ExpressRoute, a rota express direta e a rota express permitem isso.

Saiba mais:

* [Descrição geral técnica do ExpressRoute](../../expressroute/expressroute-introduction.md)
* [ExpressRoute direto](../../expressroute/expressroute-erdirect-about.md)
* [Rota expressa alcance global](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Ligar redes virtuais entre si

É possível utilizar muitas redes virtuais para as suas implementações. Há várias razões para fazer isto. Talvez queira simplificar a gestão, ou talvez queira uma maior segurança. Independentemente da motivação para colocar recursos em diferentes redes virtuais, pode haver momentos em que quer recursos em cada uma das redes para se conectarentre si.

Uma opção é que os serviços de uma rede virtual se conectem a serviços noutra rede virtual, "voltando" pela internet. A ligação começa numa rede virtual, passa pela internet e volta à rede virtual de destino. Esta opção expõe a ligação às questões de segurança inerentes a qualquer comunicação baseada na Internet.

Uma melhor opção poderia ser criar uma VPN site-to-site que se conecta entre duas redes virtuais. Este método utiliza o mesmo protocolo de modo de [túnel IPSec](https://technet.microsoft.com/library/cc786385.aspx) que a ligação VPN local-local acima mencionada.

A vantagem desta abordagem é que a ligação VPN é estabelecida sobre o tecido da rede Azure, em vez de se ligar através da internet. Isto fornece-lhe uma camada extra de segurança, em comparação com VPNs site-to-site que se conectam através da internet.

Saiba mais:

* [Configure uma ligação VNet-to-VNet utilizando o Gestor de Recursos Azure e a PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Outra forma de ligar as suas redes virtuais é [o vNET peering](../../virtual-network/virtual-network-peering-overview.md). Esta funcionalidade permite-lhe ligar duas redes Azure para que a comunicação entre elas aconteça através da infraestrutura da espinha dorsal da Microsoft sem nunca passar pela Internet. O peering VNET pode ligar dois VNETs dentro da mesma região ou dois VNETs em todas as regiões de Azure. Os NSGs podem ser usados para limitar a conectividade entre diferentes subredes ou sistemas.

## <a name="availability"></a>Disponibilidade

A disponibilidade é um componente chave de qualquer programa de segurança. Se os seus utilizadores e sistemas não conseguirem aceder ao que precisam para aceder em toda a rede, o serviço pode ser considerado comprometido. O Azure dispõe de tecnologias de networking que suportam os seguintes mecanismos de alta disponibilidade:

* Equilíbrio de carga baseado em HTTP
* Equilíbrio de carga de nível de rede
* Equilíbrio global de carga

O equilíbrio de carga é um mecanismo concebido para distribuir igualmente ligações entre vários dispositivos. Os objetivos de equilibrar a carga são:

* Para aumentar a disponibilidade. Quando carrega ligações de equilíbrio em vários dispositivos, um ou mais dispositivos podem ficar indisponíveis sem comprometer o serviço. Os serviços em execução nos restantes dispositivos online podem continuar a servir o conteúdo do serviço.
* Para aumentar o desempenho. Quando carrega ligações de equilíbrio em vários dispositivos, um único dispositivo não tem de lidar com todo o processamento. Em vez disso, o processamento e a memória exigem servir o conteúdo estão espalhados por vários dispositivos.

### <a name="http-based-load-balancing"></a>Equilíbrio de carga baseado em HTTP

As organizações que executam serviços baseados na Web muitas vezes desejam ter um equilibrador de carga baseado em HTTP em frente a esses serviços web. Isto ajuda a garantir níveis de desempenho adequados e elevada disponibilidade. Os equilibradores tradicionais de carga baseados em rede dependem de protocolos de rede e de camada de transporte. Por outro lado, os equilibradores de carga baseados em HTTP tomam decisões com base nas características do protocolo HTTP.

O Portal de Aplicações Azure fornece um equilíbrio de carga baseado em HTTP para os seus serviços baseados na Web. Suportes de Gateway de aplicação:

* Afinidade de sessão baseada em cookies. Esta capacidade garante que as ligações estabelecidas a um dos servidores por trás desse equilíbrio de carga permanecem intactas entre o cliente e o servidor. Isto garante a estabilidade das transações.
* SSL descarregar. Quando um cliente se conecta com o equilibrista de carga, essa sessão é encriptada utilizando o protocolo HTTPS (SSL). No entanto, para aumentar o desempenho, pode utilizar o protocolo HTTP (não encriptado) para ligar entre o equilibrador de carga e o servidor web por trás do equilibrador de carga. Isto é referido como "ssl offload", porque os servidores web por trás do equilibrista de carga não experimentam a sobrecarga do processador envolvida na encriptação. Os servidores web podem, portanto, solicitar mais rapidamente.
* Encaminhamento de conteúdo baseado em URL. Esta funcionalidade permite que o equilibrador de carga tome decisões sobre onde encaminhar ligações com base no URL alvo. Isto proporciona muito mais flexibilidade do que soluções que tomam decisões de equilíbrio de carga com base em endereços IP.

Saiba mais:

* [Descrição geral do Gateway de Aplicação](/azure/application-gateway/application-gateway-introduction)

### <a name="network-level-load-balancing"></a>Equilíbrio de carga de nível de rede

Em contraste com o equilíbrio de carga baseado em HTTP, o equilíbrio de carga de nível de rede toma decisões com base nos números de endereço IP e porta (TCP ou UDP).
Pode obter os benefícios do equilíbrio de carga de nível de rede em Azure utilizando o Azure Load Balancer. Algumas características-chave do Balancer de Carga incluem:

* Equilíbrio de carga de nível de rede com base no endereço IP e nos números de porta.
* Suporte para qualquer protocolo de camada de aplicação.
* Equilibre os saldos para máquinas virtuais Azure e exemplos de serviços na nuvem.
* Pode ser utilizado tanto para aplicações viradas para a Internet (equilíbrio de carga externa) como para aplicações não viradas para a Internet (equilíbrio interno de carga) e máquinas virtuais.
* A monitorização do ponto final, que é utilizada para determinar se algum dos serviços por detrás do equilibrista de carga se tornou indisponível.

Saiba mais:

* [Equilibrador de carga virado para a Internet entre várias máquinas ou serviços virtuais](/azure/load-balancer/load-balancer-internet-overview)
* [Visão geral do equilíbrio de carga interna](/azure/load-balancer/load-balancer-internal-overview)

### <a name="global-load-balancing"></a>Equilíbrio global de carga

Algumas organizações querem o mais alto nível de disponibilidade possível. Uma forma de alcançar este objetivo é acolher aplicações em centros de dados distribuídos globalmente. Quando uma aplicação é hospedada em datacenters localizados em todo o mundo, é possível que toda uma região geopolítica fique indisponível, e ainda tenha a aplicação em funcionamento.

Esta estratégia de equilíbrio de carga também pode gerar benefícios de desempenho. Pode dirigir pedidos para o serviço ao datacenter mais próximo do dispositivo que está a fazer o pedido.

Em Azure, você pode obter os benefícios do equilíbrio global de carga usando O Gestor de Tráfego Azure.

Saiba mais:

* [O que é o Gestor de Tráfego?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Resolução de nomes

A resolução de nomes é uma função crítica para todos os serviços que hospeda em Azure. Do ponto de vista da segurança, o compromisso da função de resolução de nomes pode levar um intruso a redirecionar pedidos dos seus sites para o site de um intruso. A resolução de nome seguro é um requisito para todos os seus serviços hospedados em nuvem.

Existem dois tipos de resolução de nomes que precisa de abordar:

* Resolução interna de nomes. Isto é usado por serviços nas suas redes virtuais, nas suas redes no local, ou em ambos. Os nomes utilizados para a resolução de nomes internos não são acessíveis através da internet. Para uma segurança ótima, é importante que o seu sistema interno de resolução de nomes não seja acessível a utilizadores externos.
* Resolução de nome externo. Isto é usado por pessoas e dispositivos fora das suas redes no local e redes virtuais. Estes são os nomes que são visíveis para a internet, e são usados para direcionar a ligação aos seus serviços baseados na nuvem.

Para resolução de nomeinterno, tem duas opções:

* Um servidor DNS de rede virtual. Quando cria uma nova rede virtual, é criado um servidor DNS para si. Este servidor DNS pode resolver os nomes das máquinas localizadas nessa rede virtual. Este servidor DNS não é configurável, é gerido pelo gestor de tecidos Azure, e pode, portanto, ajudá-lo a garantir a solução de resolução de nome.
* Traga o seu próprio servidor DNS. Tem a opção de colocar um servidor DNS à sua escolha na sua rede virtual. Este servidor DNS pode ser um servidor DNS integrado ative Directory, ou uma solução dedicada de servidor DNS fornecida por um parceiro Azure, que pode obter do Azure Marketplace.

Saiba mais:

* [Visão geral da rede virtual](../../virtual-network/virtual-networks-overview.md)
* [Gerir servidores DNS usados por uma rede virtual](../../virtual-network/manage-virtual-network.md#change-dns-servers)

Para resolução de nome externo, tem duas opções:

* Apresente o seu próprio servidor DNS externo no local.
* Hospedar o seu próprio servidor DNS externo com um prestador de serviços.

Muitas grandes organizações hospedam os seus próprios servidores DNS no local. Podem fazê-lo porque têm a experiência em rede e a presença global para o fazer.

Na maioria dos casos, é melhor hospedar os seus serviços de resolução de nomes DNS com um prestador de serviços. Estes prestadores de serviços têm a experiência da rede e a presença global para garantir uma disponibilidade muito elevada para os serviços de resolução de nomes. A disponibilidade é essencial para os serviços DNS, porque se os seus serviços de resolução de nomefalharem, ninguém poderá chegar aos seus serviços virados para a Internet.

O Azure fornece-lhe uma solução dNS externa altamente disponível e de alto desempenho sob a forma de DNS Azure. Esta solução de resolução de nomes externos tira partido da infraestrutura mundial do DNS Azure. Permite-lhe hospedar o seu domínio em Azure, utilizando as mesmas credenciais, APIs, ferramentas e faturação que os seus outros serviços Azure. Como parte do Azure, também herda os fortes controlos de segurança incorporados na plataforma.

Saiba mais:

* [Visão geral do DNS azure](../../dns/dns-overview.md)
* [As zonas privadas Azure DNS](../../dns/private-dns-overview.md) permitem configurar nomes privados de DNS para recursos Azure em vez dos nomes automaticamente atribuídos sem a necessidade de adicionar uma solução DNS personalizada.

## <a name="perimeter-network-architecture"></a>Arquitetura da rede de perímetro

Muitas grandes organizações usam redes de perímetro para segmentar as suas redes, e criar uma zona tampão entre a internet e os seus serviços. A parte do perímetro da rede é considerada uma zona de baixa segurança, e nenhum ativo de alto valor é colocado nesse segmento de rede. Normalmente, verá dispositivos de segurança de rede que possuem uma interface de rede no segmento da rede do perímetro. Outra interface de rede está ligada a uma rede que tem máquinas e serviços virtuais que aceitam ligações de entrada a partir da internet.

Pode desenhar redes de perímetro de várias maneiras diferentes. A decisão de implantar uma rede de perímetro, e depois que tipo de rede de perímetro usar se decidir utilizar uma, depende dos requisitos de segurança da sua rede.

Saiba mais:

* [Microsoft Cloud Services e Segurança de Rede](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Proteção contra DDoS do Azure

Os ataques de Denial of Service distribuído (DDoS) são algumas das maiores preocupações de disponibilidade e segurança relativamente aos clientes que estão a mover as suas aplicações para a cloud. Um ataque DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para utilizadores legítimos. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet.
A Microsoft fornece proteção DDoS conhecida como **Basic** como parte da Plataforma Azure. Isto não é cobrado de forma gratuita e inclui sempre a monitorização e a mitigação em tempo real de ataques comuns ao nível da rede. Além das proteções incluídas com proteção DDoS **Basic** pode ativar a opção **Standard.** As funcionalidades padrão de proteção DDoS incluem:

* **Integração da plataforma nativa:** Integrado em Azure. Inclui configuração através do portal Azure. A Norma de Proteção DDoS compreende os seus recursos e configuração de recursos.
* **Proteção chave-de-mão:** A configuração simplificada protege imediatamente todos os recursos numa rede virtual assim que a Norma de Proteção DDoS estiver ativada. Não é necessária nenhuma intervenção ou definição de utilizador. A Norma de Proteção DDoS atenua instantaneamente e automaticamente o ataque, uma vez detetado.
* **Monitorização de tráfego sempre ligado:** Os padrões de tráfego da sua aplicação são monitorizados 24 horas por dia, 7 dias por semana, à procura de indicadores de ataques dDoS. A mitigação é realizada quando as políticas de proteção são ultrapassadas.
* **Relatórios de Mitigação** de Ataques Os Relatórios de Mitigação de Ataques utilizam dados agregados de fluxo de rede para fornecer informações detalhadas sobre ataques direcionados aos seus recursos.
* **Registos** de fluxo de mitigação de ataque Os Registos de Fluxo de Mitigação de Ataque permitem-lhe rever o tráfego perdido, o tráfego reencaminhado e outros dados de ataque em quase tempo real durante um ataque ativo do DDoS.
* **Afinação adaptável:** O perfil inteligente de tráfego aprende o tráfego da sua aplicação ao longo do tempo, e seleciona e atualiza o perfil que é o mais adequado para o seu serviço. O perfil ajusta-se à medida que o tráfego muda ao longo do tempo. Proteção camada 3 para camada 7: Fornece proteção DDoS de pilha completa, quando utilizado com uma firewall de aplicação web.
* **Escala extensiva de mitigação:** Mais de 60 tipos de ataques diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques dDoS conhecidos.
* **Métricas de ataque:** As métricas resumidas de cada ataque são acessíveis através do Monitor Azure.
* **Alerta de ataque:** Os alertas podem ser configurados no início e paragem de um ataque, e durante a duração do ataque, usando métricas de ataque incorporadas. Os alertas integram-se no seu software operacional como registos do Microsoft Azure Monitor, Splunk, Azure Storage, Email e o portal Azure.
* **Garantia de custos:**  Créditos de serviço de transferência de dados e de aplicação para ataques dDoS documentados.
* **DDoS Resposta rápida** Os clientes da DDoS Protection Standard têm agora acesso à equipa de Resposta Rápida durante um ataque ativo. A RDR pode ajudar na investigação do ataque, mitigações personalizadas durante um ataque e análise pós-ataque.


Saiba mais:

* [Visão geral da proteção do DDOS](../../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

O Azure Front Door Service permite-lhe definir, gerir e monitorizar o encaminhamento global do seu tráfego web. Otimiza o encaminhamento do seu tráfego para melhor desempenho e alta disponibilidade. O Azure Front Door permite criar regras de firewall de aplicações Web (WAF) personalizadas para o controlo de acesso de forma a proteger a carga de trabalho HTTP/HTTPS da exploração com base em endereços IP de cliente, no indicativo e em parâmetros http. Além disso, a Porta Frontal também lhe permite criar regras de limitação de tarifas para combater o tráfego de bots maliciosos, inclui ndo descarregamento sl e pedido por HTTP/HTTPS, processamento de camada de aplicação.

A plataforma do Front Door encontra-se protegida pela proteção básica do Azure DDoS Protection. Para aumentar a proteção, é possível ativar a proteção padrão do Azure DDoS Protection nas suas VNETs e recursos de salvaguarda contra ataques de camada de rede (TCP/UDP) através de ajuste automático e mitigação. A Porta frontal é uma camada 7 de procuração inversa, só permite que o tráfego da web passe para servidores finais e bloqueie outros tipos de tráfego por padrão.

Saiba mais:

* Para mais informações sobre todo o conjunto de capacidades da porta da Frente Azure, você pode rever a [visão geral da Porta da Frente Azure](../../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Gestor de Tráfego Azure

O Gestor de Tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que lhe permite distribuir o tráfego de forma otimizada para serviços nas regiões globais do Azure, proporcionando, ao mesmo tempo, elevada disponibilidade e capacidade de resposta. O Gestor de Tráfego utiliza o DNS para direcionar os pedidos do cliente para o ponto final de serviço mais adequado com base num método de encaminhamento de tráfego e no estado de funcionamento dos pontos finais. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure. O gestor de tráfego monitoriza os pontos finais e não direciona o tráfego para quaisquer pontos finais que não estejam disponíveis.

Saiba mais:

* [Visão geral do gestor de tráfego azure](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitorização e deteção de ameaças

O Azure oferece capacidades para o ajudar nesta área chave com deteção, monitorização e recolha precoce do tráfego da rede.

### <a name="azure-network-watcher"></a>Observador de Rede do Azure

O Azure Network Watcher pode ajudá-lo a resolver problemas e fornece um novo conjunto de ferramentas para ajudar na identificação de problemas de segurança.

[A Visão de Grupo](../../network-watcher/network-watcher-security-group-view-overview.md) de Segurança ajuda na auditoria e conformidade de segurança das Máquinas Virtuais. Utilize esta funcionalidade para realizar auditorias programáticas, comparando as políticas de base definidas pela sua organização com regras eficazes para cada um dos seus VMs. Isto pode ajudá-lo a identificar qualquer deriva de configuração.

[A captura de pacotes](../../network-watcher/network-watcher-packet-capture-overview.md) permite-lhe capturar o tráfego da rede de e para a máquina virtual. Pode recolher estatísticas de rede e problemas de aplicação de problemas, o que pode ser inestimável na investigação de intrusões de rede. Também pode utilizar esta funcionalidade juntamente com as Funções Azure para iniciar capturas de rede em resposta a alertas específicos do Azure.

Para obter mais informações sobre o Network Watcher e como começar a testar algumas das funcionalidades nos seus laboratórios, consulte a visão geral do monitor da [rede Azure](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Para obter as notificações mais atualizadas sobre disponibilidade e estado deste serviço, consulte a página de [atualizações do Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Centro de Segurança do Azure

O Azure Security Center ajuda-o a prevenir, detetar e responder a ameaças, e proporciona-lhe uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições do Azure, ajuda a detetar ameaças que de outra forma podem passar despercebidas, e trabalha com um grande conjunto de soluções de segurança.

O Security Center ajuda-o a otimizar e monitorizar a segurança da rede através de:

* Fornecendo recomendações de segurança da rede.
* Monitorizando o estado da configuração de segurança da sua rede.
* Alertando-o para ameaças baseadas em rede, tanto nos níveis finais como nos níveis de rede.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](../../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>Rede Virtual TAP

A rede virtual Azure TAP (Terminal Access Point) permite-lhe transmitir continuamente o tráfego da sua rede virtual de máquinas para uma ferramenta de coleção de pacotes de rede ou de análise. A ferramenta de coleção ou análise é fornecida por um parceiro de aparelho virtual de rede. Pode utilizar o mesmo recurso TAP da rede virtual para agregar o tráfego de várias interfaces de rede nas mesmas ou diferentes subscrições.

Saiba mais:

* [TAP de rede virtual](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Registo

O registo a nível de rede é uma função chave para qualquer cenário de segurança da rede. No Azure, pode registar informações obtidas para nsgs para obter informações de registo de nível de rede. Com a exploração madeireira da NSG, obtém-se informações de:

* [Registos de atividade](../../azure-monitor/platform/platform-logs-overview.md). Utilize estes registos para visualizar todas as operações submetidas às suas subscrições Do Azure. Estes registos são ativados por defeito e podem ser utilizados dentro do portal Azure. Eram anteriormente conhecidos como registos de auditoria ou operacionais.
* Registos de eventos. Estes registos fornecem informações sobre as regras da NSG aplicadas.
* Contra-troncos. Estes registos permitem-lhe saber quantas vezes cada regra do NSG foi aplicada para negar ou permitir o tráfego.

Também pode utilizar o [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), uma poderosa ferramenta de visualização de dados, para visualizar e analisar estes registos.
Saiba mais:

* [Registos do Monitor Azure para Grupos de Segurança da Rede (NSGs)](../../virtual-network/virtual-network-nsg-manage-log.md)
