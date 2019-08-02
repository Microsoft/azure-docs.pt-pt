---
title: Conceitos e requisitos de segurança de rede no Azure | Microsoft Docs
description: Este artigo fornece explicações básicas sobre os principais conceitos e requisitos de segurança de rede e informações sobre o que o Azure oferece em cada uma dessas áreas.
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
ms.openlocfilehash: 746994585dfa968a8d8d982908ad424b08c06066
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726925"
---
# <a name="azure-network-security-overview"></a>Visão geral da segurança de rede do Azure

A segurança de rede pode ser definida como o processo de proteção de recursos contra o acesso ou ataque não autorizado aplicando controles ao tráfego de rede. O objetivo é garantir que apenas o tráfego legítimo seja permitido. O Azure inclui uma infraestrutura de rede robusta para dar suporte aos seus requisitos de conectividade de serviço e aplicativo. A conectividade de rede é possível entre os recursos localizados no Azure, entre os recursos hospedados no local e o Azure e de e para a Internet e o Azure.

Este artigo aborda algumas das opções que o Azure oferece na área de segurança de rede. Você pode aprender sobre:

* Rede do Azure
* Controle de acesso à rede
* Azure Firewall
* Acesso remoto seguro e conectividade entre locais
* Disponibilidade
* Resolução de nomes
* Arquitetura de rede de perímetro (DMZ)
* Proteção contra DDoS do Azure
* Porta frontal do Azure
* Gestor de tráfego
* Monitoramento e detecção de ameaças

## <a name="azure-networking"></a>Rede do Azure

O Azure exige que as máquinas virtuais estejam conectadas a uma rede virtual do Azure. Uma rede virtual é uma construção lógica criada sobre a malha de rede física do Azure. Cada rede virtual é isolada de todas as outras redes virtuais. Isso ajuda a garantir que o tráfego de rede em suas implantações não seja acessível a outros clientes do Azure.

Saiba mais:

* [Visão geral da rede virtual](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Controle de acesso à rede

O controle de acesso à rede é o ato de limitar a conectividade de e para dispositivos ou sub-redes específicas em uma rede virtual. O objetivo do controle de acesso à rede é limitar o acesso às suas máquinas virtuais e serviços a usuários e dispositivos aprovados. Os controles de acesso baseiam-se em decisões para permitir ou negar conexões de e para sua máquina virtual ou serviço.

O Azure dá suporte a vários tipos de controle de acesso à rede, como:

* Controle de camada de rede
* Controle de rota e túnel forçado
* Dispositivos de segurança de rede virtual

### <a name="network-layer-control"></a>Controle de camada de rede

Qualquer implantação segura requer alguma medida de controle de acesso à rede. O objetivo do controle de acesso à rede é restringir a comunicação da máquina virtual com os sistemas necessários. Outras tentativas de comunicação são bloqueadas.

> [!NOTE]
> Os firewalls de armazenamento são abordados no artigo [visão geral de segurança do armazenamento do Azure](storage-overview.md)

#### <a name="network-security-rules-nsgs"></a>NSGs (regras de segurança de rede)

Se você precisar de um controle de acesso básico no nível da rede (com base no endereço IP e nos protocolos TCP ou UDP), poderá usar NSGs (grupos de segurança de rede). Um NSG é um firewall de filtragem de pacotes básico, com estado, e permite controlar o acesso com base em uma [tupla de 5](https://www.techopedia.com/definition/28190/5-tuple). NSGs incluem funcionalidade para simplificar o gerenciamento e reduzir as chances de erros de configuração:

* **Regras de segurança aumentadas** simplificam a definição de regra NSG e permitem que você crie regras complexas em vez de ter que criar várias regras simples para obter o mesmo resultado.
* **Marcas de serviço** são rótulos criados pela Microsoft que representam um grupo de endereços IP. Eles são atualizados dinamicamente para incluir intervalos IP que atendam às condições que definem a inclusão no rótulo. Por exemplo, se você quiser criar uma regra que se aplica a todo o armazenamento do Azure na região leste, você pode usar armazenamento. Lesteus
* Os **grupos de segurança de aplicativo** permitem que você implante recursos em grupos de aplicativos e controle o acesso a esses recursos criando regras que usam esses grupos de aplicativos. Por exemplo, se você tiver servidores implantados no grupo de aplicativos ' WebServers ', poderá criar uma regra que aplique um NSG permitindo o tráfego de 443 da Internet para todos os sistemas no grupo de aplicativos ' WebServers '.

NSGs não fornecem inspeção de camada de aplicativo ou controles de acesso autenticados.

Saiba mais:

* [Grupos de segurança de rede](../../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>Acesso à VM just in time ASC

A [central de segurança do Azure](../../security-center/security-center-intro.md) pode gerenciar o NSGs em VMs e bloquear o acesso à VM até que um usuário com as permissões [RBAC](/azure/role-based-access-control/overview) do controle de acesso baseado em função seja acessado. Quando o usuário é autorizado com êxito, o ASC faz modificações no NSGs para permitir o acesso às portas selecionadas durante o tempo especificado. Quando o tempo expira, os NSGs são restaurados para o estado protegido anterior.

Saiba mais:

* [Acesso just-in-time da central de segurança do Azure](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Pontos finais de serviço

Os pontos de extremidade de serviço são outra maneira de aplicar o controle sobre o tráfego. Você pode limitar a comunicação com os serviços com suporte para apenas seu VNets em uma conexão direta. O tráfego de sua VNet para o serviço do Azure especificado permanece na rede de backbone Microsoft Azure.  

Saiba mais:

* [Pontos finais de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md#securing-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Controle de rota e túnel forçado

A capacidade de controlar o comportamento de roteamento em suas redes virtuais é essencial. Se o roteamento estiver configurado incorretamente, os aplicativos e serviços hospedados em sua máquina virtual poderão se conectar a dispositivos não autorizados, incluindo sistemas de propriedade e operados por invasores potenciais.

A rede do Azure dá suporte à capacidade de personalizar o comportamento de roteamento para o tráfego de rede em suas redes virtuais. Isso permite que você altere as entradas da tabela de roteamento padrão em sua rede virtual. O controle do comportamento de roteamento ajuda a garantir que todo o tráfego de um determinado dispositivo ou grupo de dispositivos entra ou sai da sua rede virtual por meio de um local específico.

Por exemplo, você pode ter um dispositivo de segurança de rede virtual em sua rede virtual. Você deseja certificar-se de que todo o tráfego de e para sua rede virtual passe pelo dispositivo de segurança virtual. Você pode fazer isso Configurando UDRs ( [rotas definidas pelo usuário](../../virtual-network/virtual-networks-udr-overview.md) ) no Azure.

O [túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que você pode usar para garantir que seus serviços não tenham permissão para iniciar uma conexão com dispositivos na Internet. Observe que isso é diferente de aceitar conexões de entrada e, em seguida, respondê-las. Os servidores Web front-end precisam responder às solicitações de hosts da Internet e, portanto, o tráfego de origem da Internet tem permissão de entrada para esses servidores Web e os servidores Web têm permissão para responder.

O que você não deseja permitir é um servidor Web front-end para iniciar uma solicitação de saída. Essas solicitações podem representar um risco de segurança, pois essas conexões podem ser usadas para baixar malware. Mesmo que você queira que esses servidores front-end iniciem solicitações de saída para a Internet, convém forçá-los a passar pelos proxies da Web locais. Isso permite que você aproveite a filtragem de URL e o registro em log.

Em vez disso, você desejaria usar o túnel forçado para evitar isso. Quando você habilita o túnel forçado, todas as conexões com a Internet são forçadas por meio do gateway local. Você pode configurar o túnel forçado tirando proveito do UDRs.

Saiba mais:

* [O que são rotas definidas pelo usuário e encaminhamento de IP](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Dispositivos de segurança de rede virtual

Embora o NSGs, o UDRs e o túnel forçado forneçam um nível de segurança nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), talvez você também queira habilitar a segurança em níveis superiores à rede.

Por exemplo, os requisitos de segurança podem incluir:

* Autenticação e autorização antes de permitir o acesso ao seu aplicativo
* Detecção de intrusão e resposta de intrusão
* Inspeção da camada de aplicativo para protocolos de alto nível
* Filtragem de URL
* Antivírus e antimalware no nível da rede
* Proteção contra bot
* Controle de acesso do aplicativo
* Proteção adicional contra DDoS (acima da proteção contra DDoS fornecida pela própria malha do Azure)

Você pode acessar esses recursos de segurança de rede aprimorados usando uma solução de parceiro do Azure. Você pode encontrar as soluções de segurança de rede do parceiro do Azure mais atuais visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/)e procurando por "segurança" e "segurança de rede".

## <a name="azure-firewall"></a>Azure Firewall

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É uma firewall com total monitoração de estado como um serviço com elevada disponibilidade incorporada e escalabilidade da cloud sem restrições. Alguns recursos incluem:

* Elevada disponibilidade
* Escalabilidade da nuvem
* Regras de filtragem de FQDN de aplicação
* Regras de filtragem de tráfego de rede

Saiba mais:

* [Visão geral do firewall do Azure](/azure/firewall/overview)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Acesso remoto seguro e conectividade entre locais

A instalação, a configuração e o gerenciamento de seus recursos do Azure precisam ser feitos remotamente. Além disso, talvez você queira implantar soluções de [ti híbridas](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) que têm componentes locais e na nuvem pública do Azure. Esses cenários exigem acesso remoto seguro.

A rede do Azure dá suporte aos seguintes cenários de acesso remoto seguro:

* Conectar estações de trabalho individuais a uma rede virtual
* Conectar sua rede local a uma rede virtual com uma VPN
* Conectar sua rede local a uma rede virtual com um link WAN dedicado
* Conectar redes virtuais entre si

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Conectar estações de trabalho individuais a uma rede virtual

Talvez você queira permitir que desenvolvedores individuais ou equipe de operações gerenciem máquinas virtuais e serviços no Azure. Por exemplo, digamos que você precise de acesso a uma máquina virtual em uma rede virtual. Mas sua política de segurança não permite acesso remoto RDP ou SSH a máquinas virtuais individuais. Nesse caso, você pode usar uma conexão [VPN ponto a site](../../vpn-gateway/point-to-site-about.md) .

A conexão VPN ponto a site permite que você configure uma conexão privada e segura entre o usuário e a rede virtual. Quando a conexão VPN é estabelecida, o usuário pode RDP ou SSH sobre o link VPN em qualquer máquina virtual na rede virtual. (Isso pressupõe que o usuário pode autenticar e está autorizado.) A VPN ponto a site dá suporte a:

* SSTP, um protocolo de VPN com base em SSL proprietário. Uma solução de VPN SSL pode penetrar em firewalls, pois a maioria dos firewalls abre a porta TCP 443, que usa SSL. O SSTP só tem suporte em dispositivos Windows. O Azure dá suporte a todas as versões do Windows que têm o SSTP (Windows 7 e posterior).

* VPN IKEv2, uma solução de VPN IPsec baseada em normas. A VPN IKEv2 pode ser utilizada para ligar a partir de dispositivos Mac (versões de OSX 10.11 e superiores).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Saiba mais:

* [Configurar uma conexão ponto a site com uma rede virtual usando o PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Conectar sua rede local a uma rede virtual com uma VPN

Talvez você queira conectar toda a rede corporativa, ou partes dela, a uma rede virtual. Isso é comum em cenários de ti híbridos, em que as organizações estendem [seu datacenter local para o Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Em muitos casos, as organizações hospedam partes de um serviço no Azure e partes locais. Por exemplo, eles podem fazer isso quando uma solução inclui servidores Web front-end no Azure e bancos de dados back-end locais. Esses tipos de conexões "entre instalações" também tornam o gerenciamento de recursos localizados do Azure mais seguros e permitem cenários como estender Active Directory controladores de domínio para o Azure.

Uma maneira de fazer isso é usar uma [VPN site a site](https://www.techopedia.com/definition/30747/site-to-site-vpn). A diferença entre uma VPN site a site e uma VPN ponto a site é que o último conecta um único dispositivo a uma rede virtual. Uma VPN site a site conecta uma rede inteira (como sua rede local) a uma rede virtual. As VPNs site a site para uma rede virtual usam o protocolo VPN do modo de encapsulamento IPsec altamente seguro.

Saiba mais:

* [Criar uma VNet do Resource Manager com uma conexão VPN site a site usando o portal do Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Acerca do Gateway de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Conectar sua rede local a uma rede virtual com um link WAN dedicado

As conexões VPN ponto a site e site a site são eficazes para habilitar a conectividade entre locais. No entanto, algumas organizações consideram que elas tenham as seguintes desvantagens:

* As conexões VPN movem dados pela Internet. Isso expõe essas conexões a possíveis problemas de segurança envolvidos na movimentação de dados em uma rede pública. Além disso, a confiabilidade e a disponibilidade de conexões com a Internet não podem ser garantidas.
* As conexões VPN com redes virtuais podem não ter a largura de banda para alguns aplicativos e finalidades, pois elas são máximas em cerca de 200 Mbps.

As organizações que precisam do nível mais alto de segurança e disponibilidade para suas conexões entre locais normalmente usam links WAN dedicados para se conectar a sites remotos. O Azure fornece a capacidade de usar um link WAN dedicado que você pode usar para conectar sua rede local a uma rede virtual. Azure ExpressRoute, rota expressa direta e alcance global de rota expressa habilite isso.

Saiba mais:

* [Visão geral técnica do ExpressRoute](../../expressroute/expressroute-introduction.md)
* [ExpressRoute direto](../../expressroute/expressroute-erdirect-about.md)
* [Alcance global de rota expressa](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Conectar redes virtuais entre si

É possível usar muitas redes virtuais para suas implantações. Há várias razões pelas quais você pode fazer isso. Talvez você queira simplificar o gerenciamento, ou talvez queira aumentar a segurança. Independentemente da motivação para colocar recursos em diferentes redes virtuais, pode haver ocasiões em que você queira que os recursos em cada uma das redes se conectem entre si.

Uma opção é fazer com que os serviços em uma rede virtual se conectem aos serviços em outra rede virtual, fazendo o "looping novamente" pela Internet. A conexão inicia em uma rede virtual, passa pela Internet e volta à rede virtual de destino. Essa opção expõe a conexão com os problemas de segurança inerentes em qualquer comunicação baseada na Internet.

Uma opção melhor pode ser criar uma VPN site a site que se conecte entre duas redes virtuais. Esse método usa o mesmo protocolo de [modo de túnel IPSec](https://technet.microsoft.com/library/cc786385.aspx) que a conexão VPN site a site entre locais mencionada acima.

A vantagem dessa abordagem é que a conexão VPN é estabelecida pela malha de rede do Azure, em vez de conectar-se pela Internet. Isso fornece uma camada extra de segurança, em comparação com as VPNs site a site que se conectam pela Internet.

Saiba mais:

* [Configurar uma conexão de VNet para VNet usando o Azure Resource Manager e o PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Outra maneira de conectar suas redes virtuais é o [emparelhamento VNET](../../virtual-network/virtual-network-peering-overview.md). Esse recurso permite que você se conecte a duas redes do Azure para que a comunicação entre elas ocorra na infra-estrutura de backbone da Microsoft sem que ela passe pela Internet. O emparelhamento VNET pode conectar duas VNETs na mesma região ou duas VNETs nas regiões do Azure. NSGs pode ser usado para limitar a conectividade entre diferentes sub-redes ou sistemas.

## <a name="availability"></a>Disponibilidade

A disponibilidade é um componente-chave de qualquer programa de segurança. Se os usuários e sistemas não puderem acessar o que precisam para acessar pela rede, o serviço poderá ser considerado comprometido. O Azure tem tecnologias de rede que dão suporte aos seguintes mecanismos de alta disponibilidade:

* Balanceamento de carga baseado em HTTP
* Balanceamento de carga no nível da rede
* Balanceamento de carga global

O balanceamento de carga é um mecanismo projetado para distribuir igualmente as conexões entre vários dispositivos. Os objetivos do balanceamento de carga são:

* Para aumentar a disponibilidade. Quando você equilibra a carga de conexões entre vários dispositivos, um ou mais dos dispositivos podem ficar indisponíveis sem comprometer o serviço. Os serviços em execução nos dispositivos online restantes podem continuar a fornecer o conteúdo do serviço.
* Para aumentar o desempenho. Ao balancear a carga de conexões entre vários dispositivos, um único dispositivo não precisa lidar com todo o processamento. Em vez disso, as demandas de processamento e memória para o fornecimento do conteúdo são distribuídas entre vários dispositivos.

### <a name="http-based-load-balancing"></a>Balanceamento de carga baseado em HTTP

As organizações que executam serviços baseados na Web muitas vezes querem ter um balanceador de carga baseado em HTTP na frente desses serviços Web. Isso ajuda a garantir níveis adequados de desempenho e alta disponibilidade. Os balanceadores de carga tradicionais baseados em rede contam com protocolos de rede e de camada de transporte. Os balanceadores de carga baseados em HTTP, por outro lado, tomam decisões com base nas características do protocolo HTTP.

Aplicativo Azure Gateway fornece balanceamento de carga baseado em HTTP para seus serviços baseados na Web. O gateway de aplicativo dá suporte a:

* Afinidade de sessão baseada em cookie. Essa funcionalidade garante que as conexões estabelecidas com um dos servidores por trás desse balanceador de carga permaneçam intactas entre o cliente e o servidor. Isso garante a estabilidade das transações.
* Descarregamento de SSL. Quando um cliente se conecta com o balanceador de carga, essa sessão é criptografada usando o protocolo HTTPS (SSL). No entanto, para aumentar o desempenho, você pode usar o protocolo HTTP (não criptografado) para se conectar entre o balanceador de carga e o servidor Web por trás do balanceador de carga. Isso é chamado de "descarregamento de SSL", porque os servidores Web por trás do balanceador de carga não experimentam a sobrecarga do processador envolvida na criptografia. Os servidores Web podem, portanto, atender às solicitações mais rapidamente.
* Roteamento de conteúdo baseado em URL. Esse recurso possibilita que o balanceador de carga tome decisões sobre onde encaminhar conexões com base na URL de destino. Isso fornece muito mais flexibilidade do que as soluções que fazem decisões de balanceamento de carga com base em endereços IP.

Saiba mais:

* [Visão geral do gateway de aplicativo](/azure/application-gateway/application-gateway-introduction)

### <a name="network-level-load-balancing"></a>Balanceamento de carga no nível da rede

Ao contrário do balanceamento de carga baseado em HTTP, o balanceamento de carga no nível de rede faz decisões com base no endereço IP e nos números de porta (TCP ou UDP).
Você pode obter os benefícios do balanceamento de carga no nível de rede no Azure usando Azure Load Balancer. Algumas das principais características do Load Balancer incluem:

* Balanceamento de carga no nível da rede com base no endereço IP e nos números de porta.
* Suporte para qualquer protocolo de camada de aplicativo.
* Balanceamento de carga para máquinas virtuais do Azure e instâncias de função de serviços de nuvem.
* Pode ser usado para aplicativos voltados para a Internet (balanceamento de carga externo) e para máquinas virtuais (balanceamento de carga interno) e para fora da Internet.
* Monitoramento de ponto de extremidade, que é usado para determinar se algum dos serviços por trás do balanceador de carga se tornou indisponível.

Saiba mais:

* [Balanceador de carga voltado para a Internet entre várias máquinas virtuais ou serviços](/azure/load-balancer/load-balancer-internet-overview)
* [Visão geral do balanceador de carga interno](/azure/load-balancer/load-balancer-internal-overview)

### <a name="global-load-balancing"></a>Balanceamento de carga global

Algumas organizações desejam o mais alto nível de disponibilidade possível. Uma maneira de alcançar esse objetivo é hospedar aplicativos em data centers distribuídos globalmente. Quando um aplicativo é hospedado em data centers localizados em todo o mundo, é possível que uma região geopolítica inteira fique indisponível e ainda tenha o aplicativo em funcionamento.

Essa estratégia de balanceamento de carga também pode gerar benefícios de desempenho. Você pode direcionar solicitações para o serviço para o datacenter que é mais próximo do dispositivo que está fazendo a solicitação.

No Azure, você pode obter os benefícios do balanceamento de carga global usando o Gerenciador de tráfego do Azure.

Saiba mais:

* [O que é o Gestor de Tráfego?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Resolução de nomes

A resolução de nomes é uma função crítica para todos os serviços que você hospeda no Azure. Do ponto de vista da segurança, o comprometimento da função de resolução de nomes pode levar a um invasor redirecionando as solicitações de seus sites para o site de um invasor. A resolução de nomes segura é um requisito para todos os seus serviços hospedados na nuvem.

Há dois tipos de resolução de nomes que você precisa abordar:

* Resolução de nomes interna. Isso é usado pelos serviços em suas redes virtuais, suas redes locais ou ambos. Nomes usados para resolução de nome interno não podem ser acessados pela Internet. Para uma segurança ideal, é importante que o esquema de resolução de nome interno não esteja acessível a usuários externos.
* Resolução de nomes externos. Isso é usado por pessoas e dispositivos fora de suas redes locais e redes virtuais. Esses são os nomes que são visíveis para a Internet e são usados para direcionar a conexão com seus serviços baseados em nuvem.

Para a resolução de nomes internos, você tem duas opções:

* Um servidor DNS de rede virtual. Quando você cria uma nova rede virtual, um servidor DNS é criado para você. Esse servidor DNS pode resolver os nomes dos computadores localizados nessa rede virtual. Esse servidor DNS não é configurável, é gerenciado pelo Azure Fabric Manager e, portanto, pode ajudá-lo a proteger sua solução de resolução de nomes.
* Traga seu próprio servidor DNS. Você tem a opção de colocar um servidor DNS de sua escolha em sua rede virtual. Esse servidor DNS pode ser um servidor DNS integrado Active Directory ou uma solução de servidor DNS dedicada fornecida por um parceiro do Azure, que você pode obter do Azure Marketplace.

Saiba mais:

* [Visão geral da rede virtual](../../virtual-network/virtual-networks-overview.md)
* [Gerenciar servidores DNS usados por uma rede virtual](../../virtual-network/manage-virtual-network.md#change-dns-servers)

Para a resolução de nomes externos, você tem duas opções:

* Hospede seu próprio servidor DNS externo no local.
* Hospede seu próprio servidor DNS externo com um provedor de serviços.

Muitas organizações de grande porte hospedam seus próprios servidores DNS localmente. Eles podem fazer isso porque têm a experiência de rede e a presença global para fazer isso.

Na maioria dos casos, é melhor hospedar seus serviços de resolução de nomes DNS com um provedor de serviços. Esses provedores de serviços têm o conhecimento de rede e a presença global para garantir uma disponibilidade muito alta para seus serviços de resolução de nomes. A disponibilidade é essencial para os serviços DNS, porque se os serviços de resolução de nomes falharem, ninguém poderá acessar os serviços voltados para a Internet.

O Azure fornece uma solução de DNS externo altamente disponível e de alto desempenho na forma de DNS do Azure. Essa solução de resolução de nomes externos aproveita a infraestrutura de DNS Mundial do Azure. Ele permite que você hospede seu domínio no Azure, usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. Como parte do Azure, ele também herda os fortes controles de segurança internos da plataforma.

Saiba mais:

* [Visão geral do DNS do Azure](../../dns/dns-overview.md)
* As [zonas privadas do DNS do Azure](../../dns/private-dns-overview.md) permitem configurar nomes DNS privados para recursos do Azure em vez de nomes atribuídos automaticamente, sem a necessidade de adicionar uma solução DNS personalizada.

## <a name="perimeter-network-architecture"></a>Arquitetura de rede de perímetro

Muitas organizações de grande porte usam redes de perímetro para segmentar suas redes e criar uma zona de buffer entre a Internet e seus serviços. A parte de perímetro da rede é considerada uma zona de baixa segurança e nenhum ativo de alto valor é colocado nesse segmento de rede. Normalmente, você verá dispositivos de segurança de rede que têm uma interface de rede no segmento de rede de perímetro. Outra interface de rede está conectada a uma rede que tem máquinas virtuais e serviços que aceitam conexões de entrada da Internet.

Você pode criar redes de perímetro de várias maneiras diferentes. A decisão de implantar uma rede de perímetro e o tipo de rede de perímetro a ser usado se você decidir usar uma dependerá dos requisitos de segurança de rede.

Saiba mais:

* [Serviços de Microsoft Cloud e segurança de rede](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Proteção contra DDoS do Azure

Os ataques de Denial of Service distribuído (DDoS) são algumas das maiores preocupações de disponibilidade e segurança relativamente aos clientes que estão a mover as suas aplicações para a cloud. Um ataque de DDoS tenta esgotar os recursos de um aplicativo, tornando o aplicativo indisponível para usuários legítimos. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet.
A Microsoft fornece proteção contra DDoS conhecida como **básica** como parte da plataforma Azure. Isso é fornecido sem custos e inclui o monitoramento do Always on e a mitigação em tempo real de ataques comuns no nível da rede. Além das proteções incluídas com a proteção contra DDoS **básica** , você pode habilitar a opção **padrão** . Os recursos padrão de proteção contra DDoS incluem:

* **Integração da plataforma nativa:** Integrado nativamente ao Azure. Inclui a configuração por meio do portal do Azure. A proteção contra DDoS Standard compreende seus recursos e a configuração de recursos.
* **Proteção por chave:** A configuração simplificada protege imediatamente todos os recursos em uma rede virtual assim que a proteção contra DDoS Standard está habilitada. Nenhuma intervenção ou definição de usuário é necessária. A proteção contra DDoS Standard reduz instantaneamente e automaticamente o ataque, uma vez detectado.
* **Monitoramento de tráfego AlwaysOn:** Os padrões de tráfego do aplicativo são monitorados 24 horas por dia, 7 dias por semana, procurando indicadores de ataques de DDoS. A mitigação é executada quando as políticas de proteção são excedidas.
* **Relatórios** de mitigação de ataque Os relatórios de mitigação de ataque usam dados de fluxo de rede agregados para fornecer informações detalhadas sobre ataques direcionados a seus recursos.
* **Logs de fluxo** de mitigação de ataque Os logs de fluxo de mitigação de ataque permitem que você examine o tráfego descartado, o tráfego encaminhado e outros dados de ataque quase em tempo real durante um ataque de DDoS ativo.
* **Ajuste adaptativo:** A criação de perfil de tráfego inteligente aprende o tráfego do seu aplicativo ao longo do tempo e seleciona e atualiza o perfil que é o mais adequado para o seu serviço. O perfil é ajustado conforme o tráfego é alterado ao longo do tempo. Proteção de camada 3 para camada 7: Fornece proteção contra DDoS de pilha completa, quando usada com um firewall do aplicativo Web.
* **Escala de mitigação extensiva:** Mais de 60 tipos de ataque diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques de DDoS conhecidos.
* **Métricas de ataque:** As métricas resumidas de cada ataque podem ser acessadas por meio de Azure Monitor.
* **Alerta de ataque:** Os alertas podem ser configurados no início e na interrupção de um ataque e na duração do ataque, usando métricas de ataque internas. Os alertas integram-se ao seu software operacional, como Microsoft Azure logs de monitor, Splunk, armazenamento do Azure, email e o portal do Azure.
* **Garantia de custo:**  Créditos de serviço de transferência de dados e de expansão de aplicativos para ataques de DDoS documentados.
* **DDoS responsivo rápido** Os clientes padrão de proteção contra DDoS agora têm acesso à equipe de resposta rápida durante um ataque ativo. O DRR pode ajudar com a investigação de ataque, atenuações personalizadas durante uma análise de ataque e após o ataque.


Saiba mais:

* [Visão geral da proteção contra DDOS](../../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Porta frontal do Azure

O serviço de porta frontal do Azure permite que você defina, gerencie e monitore o roteamento global do seu tráfego da Web. Ele otimiza o roteamento do tráfego para melhor desempenho e alta disponibilidade. O Azure Front Door permite criar regras de firewall de aplicações Web (WAF) personalizadas para o controlo de acesso de forma a proteger a carga de trabalho HTTP/HTTPS da exploração com base em endereços IP de cliente, no indicativo e em parâmetros http. Além disso, a porta frontal também permite que você crie regras de limitação de taxa para a batalha de tráfego de bot mal-intencionado, inclui descarregamento de SSL e solicitação por HTTP/HTTPS, processamento de camada de aplicativo.

A própria plataforma de porta frontal é protegida pela proteção contra DDoS do Azure básica. Para aumentar a proteção, é possível ativar a proteção padrão do Azure DDoS Protection nas suas VNETs e recursos de salvaguarda contra ataques de camada de rede (TCP/UDP) através de ajuste automático e mitigação. A porta frontal é um proxy reverso de camada 7, ela só permite que o tráfego da Web passe para os servidores back-end e bloqueie outros tipos de tráfego por padrão.

Saiba mais:

* Para obter mais informações sobre o conjunto completo de recursos de porta frontal do Azure, você pode examinar a [visão geral da porta de frente do Azure](../../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Gerenciador de tráfego do Azure

O Gestor de Tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que lhe permite distribuir o tráfego de forma otimizada para serviços nas regiões globais do Azure, proporcionando, ao mesmo tempo, elevada disponibilidade e capacidade de resposta. O Gestor de Tráfego utiliza o DNS para direcionar os pedidos do cliente para o ponto final de serviço mais adequado com base num método de encaminhamento de tráfego e no estado de funcionamento dos pontos finais. Os pontos finais são serviços com acesso à Internet alojados dentro ou fora do Azure. O Gerenciador de tráfego monitora os pontos de extremidade e não direciona o tráfego para nenhum ponto de extremidade que não esteja disponível.

Saiba mais:

* [Visão geral do Gerenciador de tráfego do Azure](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitoramento e detecção de ameaças

O Azure fornece recursos para ajudá-lo nessa área principal com a detecção, o monitoramento e a coleta de início e de análise do tráfego de rede.

### <a name="azure-network-watcher"></a>Observador de rede do Azure

O observador de rede do Azure pode ajudá-lo a solucionar problemas e fornece um novo conjunto de ferramentas para ajudá-lo na identificação de problemas de segurança.

A [exibição do grupo de segurança](../../network-watcher/network-watcher-security-group-view-overview.md) ajuda na auditoria e na conformidade de segurança das máquinas virtuais. Use esse recurso para executar auditorias programáticas, comparando as políticas de linha de base definidas por sua organização para regras efetivas para cada uma de suas VMs. Isso pode ajudá-lo a identificar qualquer descompasso de configuração.

A [captura de pacotes](../../network-watcher/network-watcher-packet-capture-overview.md) permite capturar o tráfego de rede de e para a máquina virtual. Você pode coletar estatísticas de rede e solucionar problemas de aplicativos, o que pode ser inestimável na investigação de invasões de rede. Você também pode usar esse recurso junto com Azure Functions para iniciar capturas de rede em resposta a alertas específicos do Azure.

Para obter mais informações sobre o observador de rede e como começar a testar algumas das funcionalidades em seus laboratórios, consulte [visão geral de monitoramento do observador de rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Para obter as notificações mais atualizadas sobre a disponibilidade e o status desse serviço, verifique a [página atualizações do Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Centro de Segurança do Azure

A central de segurança do Azure ajuda você a prevenir, detectar e responder a ameaças, além de oferecer maior visibilidade e controle sobre a segurança de seus recursos do Azure. Ele fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com um grande conjunto de soluções de segurança.

A central de segurança ajuda a otimizar e monitorar a segurança de rede:

* Fornecendo recomendações de segurança de rede.
* Monitorando o estado de sua configuração de segurança de rede.
* Alertando sobre ameaças baseadas em rede, tanto nos níveis de ponto de extremidade quanto de rede.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](../../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>TAP de Rede Virtual

O TAP (ponto de acesso ao terminal) da rede virtual do Azure permite transmitir continuamente o tráfego de rede da máquina virtual para uma ferramenta de análise ou coletor de pacotes de rede. O coletor ou a ferramenta de análise é fornecida por um parceiro de solução de virtualização de rede. Você pode usar o mesmo recurso de toque de rede virtual para agregar o tráfego de várias interfaces de rede na mesma assinatura ou em assinaturas diferentes.

Saiba mais:

* [TOQUE da rede virtual](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Registo

O registro em log em um nível de rede é uma função fundamental para qualquer cenário de segurança de rede. No Azure, você pode registrar as informações obtidas para NSGs para obter informações de log no nível de rede. Com o registro em log do NSG, você obtém informações de:

* [Logs de atividade](../../azure-monitor/platform/activity-logs-overview.md). Use esses logs para exibir todas as operações enviadas para suas assinaturas do Azure. Esses logs são habilitados por padrão e podem ser usados dentro do portal do Azure. Eles eram anteriormente conhecidos como logs de auditoria ou operacionais.
* Logs de eventos. Esses logs fornecem informações sobre quais regras de NSG foram aplicadas.
* Logs de contador. Esses logs permitem que você saiba quantas vezes cada regra NSG foi aplicada para negar ou permitir o tráfego.

Você também pode usar o [Microsoft Power bi](https://powerbi.microsoft.com/what-is-power-bi/), uma poderosa ferramenta de visualização de dados, para exibir e analisar esses logs.
Saiba mais:

* [Logs de Azure Monitor para NSGs (grupos de segurança de rede)](../../virtual-network/virtual-network-nsg-manage-log.md)
