---
title: Controlando o tráfego de entrada na Austrália do Azure
description: Um guia para controlar o tráfego de entrada na Austrália do Azure para atender aos requisitos governamentais australianos de gateways de Internet seguros
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 3885b9fa346047a50a49c7b2f9b96b6a8f95e51f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779358"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>Controlando o tráfego de entrada na Austrália do Azure

Um dos principais elementos da proteção de sistemas de ICT é o controle do tráfego de rede. O tráfego deve ser restrito apenas ao que for necessário para que um sistema funcione para reduzir o potencial de comprometimento.

Este guia fornece detalhes sobre como o tráfego de rede de entrada (entrada) funciona no Azure e recomendações para implementar controles de segurança de rede para um sistema conectado à Internet.

Os controles de rede se alinham com as diretrizes de consumidor Australian ACSC (centro de segurança da Austrália) e a intenção do ISM (manual de segurança da informação) da ACSC.

## <a name="requirements"></a>Requisitos

Os requisitos de segurança gerais para sistemas da Comunidade são definidos no ISM. Para auxiliar as entidades da Comunidade na implementação da segurança de rede, [o acsc publicou o acsc Protect: Implementando a segmentação e](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm)a diferenciação de rede e para auxiliar na proteção de sistemas em ambientes de nuvem, o acsc publicou a [segurança de computação em nuvem para locatários](https://www.acsc.gov.au/publications/protect/Cloud_Computing_Security_for_Tenants.pdf).

Esses guias descrevem o contexto para implementar a segurança de rede e controlar o tráfego e incluem recomendações práticas para design e configuração de rede.

O guia de [segurança de computação em nuvem da Microsoft para locatários de Microsoft Azure](https://aka.ms/au-irap) na página da Austrália do portal de confiança do serviço realça as tecnologias específicas da Microsoft que permitem que você atenda aos conselhos nas publicações do acsc.

Os seguintes requisitos principais, identificados nas publicações do ACSC, são importantes para controlar o tráfego de entrada no Azure:

|Descrição|Source|
|---|---|
|**Implemente a segmentação de rede e a diferenciação, por exemplo, a arquitetura de n camadas, usando firewalls baseados em host e controles de acesso de rede do CSP para limitar a conectividade de rede VM de entrada e saída a apenas portas/protocolos necessários.**| _Computação em nuvem para locatários_|
|**Implemente alta largura de banda adequada, baixa latência e conectividade de rede confiável** entre o locatário (incluindo os usuários remotos do locatário) e o serviço de nuvem para atender aos requisitos de disponibilidade do locatário  | _Computação em nuvem para locatários_|
|**Aplique tecnologias em mais do que apenas na camada de rede**. Cada host e rede devem ser segmentados e separados, sempre que possível, no nível mais baixo que possa ser praticamente gerenciado. Na maioria dos casos, segmentação e diferenciação se aplicam da camada de link de dados até e incluindo a camada de aplicativo; no entanto, em ambientes confidenciais, o isolamento físico pode ser apropriado. As medidas baseadas em host e em toda a rede devem ser implantadas de maneira complementar e ser monitoradas centralmente. Usar um firewall ou dispositivo de segurança como a única medida de segurança não é suficiente. |_ACSC proteger: Implementando a segmentação de rede e a diferenciação_|
|**Use os princípios de privilégios mínimos e precise de ‐ para ‐ saber**. Se um host, serviço ou rede não precisar se comunicar com outro host, serviço ou rede, ele não deverá ser permitido. Se um host, serviço ou rede precisar apenas se comunicar com outro host, serviço ou rede usando portas ou protocolos específicos, quaisquer outras portas ou protocolos deverão ser desabilitados. A adoção desses princípios em uma rede irá complementar a minimisation de privilégios de usuário e aumentar significativamente a postura de segurança geral do ambiente. |_ACSC proteger: Implementando a segmentação de rede e a diferenciação_|
|**Separe os hosts e as redes com base em sua sensibilidade ou importância para as operações de negócios**. A separação pode ser obtida usando diferentes hardware ou plataformas, dependendo de diferentes classificações de segurança, domínios de segurança ou requisitos de disponibilidade/integridade para determinados hosts ou redes. Em particular, separe as redes de gerenciamento e considere isolar fisicamente redes de gerenciamento fora de banda para ambientes confidenciais. |_ACSC proteger: Implementando a segmentação de rede e a diferenciação_|
|**Identificar, autenticar e autorizarr o acesso por todas as entidades a todas as outras entidades**. Todos os usuários, hosts e serviços devem ter seu acesso restrito a apenas os outros usuários, hosts e serviços necessários para realizar suas funções ou responsabilidades designadas. Todos os serviços herdados ou locais que ignoram ou desativam a força de identificação, autenticação e serviços autorizaçãos devem ser desabilitados e seu uso deve ser monitorado de forma rigorosa. |_ACSC proteger: Implementando a segmentação de rede e a diferenciação_|
|**Implementar permitir listagem de tráfego de rede em vez de negar listagem**. Só permita o acesso para tráfego de rede válido conhecido (isto é, que é identificado, autenticado e autorizado), em vez de negar acesso ao tráfego de rede incorreto conhecido (por exemplo, bloquear um endereço ou serviço específico). A lista de permissões resulta em uma política de segurança superior para a blacklist e melhora significativamente a capacidade de uma organização para detectar e avaliar possíveis invasões de rede. |_ACSC proteger: Implementando a segmentação de rede e a diferenciação_|
|

Este artigo fornece informações e recomendações sobre como esses requisitos podem ser atendidos para sistemas implantados no Azure usando IaaS (infraestrutura como serviço) e PaaS (plataforma como serviço). Você também deve ler o artigo sobre como [controlar o tráfego de saída na Austrália do Azure](gateway-egress-traffic.md) para entender totalmente o controle do tráfego de rede no Azure.

## <a name="architecture"></a>Arquitetura

Se você estiver envolvido no design ou na implementação de segurança de rede e controles de tráfego de entrada, você deve primeiro entender como o tráfego de rede de entrada funciona no Azure em IaaS e PaaS. Esta seção fornece uma visão geral dos possíveis pontos de entrada em que o tráfego de rede pode alcançar um recurso hospedado no Azure e os controles de segurança disponíveis para restringir e controlar esse tráfego. Cada um desses componentes é discutido em detalhes nas seções restantes deste guia.

### <a name="architecture-components"></a>Componentes da arquitetura

O diagrama arquitetônico mostrado aqui descreve os caminhos possíveis que o tráfego de rede pode tomar para se conectar a um serviço hospedado no Azure. Esses componentes são divididos no Azure, ingresso no IaaS, entrada de PaaS e controle de segurança, dependendo da função que eles fornecem para o tráfego de entrada.

![Arquitetura](media/ingress-traffic.png)

### <a name="azure-components"></a>Componentes do Azure

|Componente | Descrição|
|---|---|
|**Proteção contra DDOS** | Ataques de DDoS (negação de serviço distribuído) tentam esgotar os recursos de um aplicativo, tornando o aplicativo indisponível para usuários legítimos. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet. O Azure inclui proteção contra DDoS automaticamente por meio da plataforma Azure e fornece recursos de mitigação adicionais que podem ser habilitados para aplicativos específicos para um controle mais granular.|
| **Gestor de Tráfego** | O Gerenciador de tráfego do Azure é um balanceador de carga de tráfego baseado no DNS (sistema de nomes de domínio) que pode distribuir o tráfego de forma ideal para serviços em regiões do Azure, fornecendo alta disponibilidade e capacidade de resposta. O Gerenciador de tráfego usa o DNS para direcionar solicitações de cliente para o ponto de extremidade mais apropriado com base em um método de roteamento de tráfego e a integridade dos pontos de extremidade.|
| **ExpressRoute** | O ExpressRoute é uma conexão de rede dedicada para consumir serviços em nuvem da Microsoft. Ele é provisionado por meio de um provedor de conectividade e oferece mais confiabilidade, velocidades mais rápidas, latências menores e maior segurança do que conexões típicas pela Internet. Um circuito do ExpressRoute representa a conexão lógica entre a infraestrutura local e os serviços de nuvem da Microsoft por meio de um provedor de conectividade.|
| **Emparelhamento privado do ExpressRoute** | O emparelhamento privado do ExpressRoute é uma conexão entre o ambiente local e as redes virtuais privadas do Azure. O emparelhamento privado permite o acesso aos serviços do Azure, como máquinas virtuais, que são implantados em uma rede virtual. Os recursos e as redes virtuais acessados por meio do emparelhamento privado são considerados uma extensão da rede principal de uma organização. O emparelhamento privado fornece conectividade bidirecional entre a rede local e as redes virtuais do Azure usando endereços IP privados.|
| **Emparelhamento da Microsoft para ExpressRoute** | O emparelhamento da Microsoft ExpressRoute é uma conexão entre o ambiente local e os serviços públicos da Microsoft e do Azure. Isso inclui conectividade com os serviços do Office 365, Dynamics 365 e PaaS do Azure. O emparelhamento é estabelecido em endereços IP públicos que são de propriedade da organização ou do provedor de conectividade. Nenhum serviço pode ser acessado por meio do emparelhamento da Microsoft de ExpressRoute por padrão e uma organização deve aceitar os serviços que são necessários. Esse processo fornece conectividade com os mesmos pontos de extremidade que estão disponíveis na Internet.|
|

### <a name="iaas-ingress-components"></a>Componentes de ingresso de IaaS

|Componente | Descrição|
|---|---|
|**Interface de rede** | Uma interface de rede é um recurso que existe no Azure. Ele é anexado a uma máquina virtual e atribuído a um endereço IP não roteável de Internet privado da sub-rede à qual ela está associada. Esse endereço IP é atribuído dinamicamente ou estaticamente por meio de Azure Resource Manager.|
|**Sub-rede** | Uma sub-rede é um intervalo de endereços IP que é criado em uma VNet. Várias sub-redes podem ser criadas em uma VNet para segmentação de rede.|
| **Rede virtual (VNet)** | Uma VNet é um recurso fundamental do Azure que fornece uma plataforma e limite para a implantação de recursos e a habilitação da comunicação. A VNet existe em uma região do Azure e define o espaço de endereço IP e os limites de roteamento para recursos integrados de VNet, como máquinas virtuais.|
| **VNet Peering** | O emparelhamento VNet é uma opção de configuração do Azure que permite a comunicação direta entre dois VNets sem a necessidade de um gateway de rede virtual. Uma vez emparelhadas, as duas VNets podem se comunicar diretamente e a configuração adicional pode controlar o uso de gateways de rede virtual e outras opções de trânsito.|
| **IP público** | Um IP público é um recurso que reserva um dos endereços IP públicos de propriedade da Microsoft e roteáveis da região especificada para uso na rede virtual. Ele pode ser associado a um adaptador de rede específico, o que permite que o recurso seja acessível pelos sistemas Internet, ExpressRoute e PaaS.|
| **Gateway de ExpressRoute** | Um gateway do ExpressRoute é um objeto em uma rede virtual que fornece conectividade e roteamento da rede virtual para redes locais por meio de emparelhamento privado em um circuito do ExpressRoute.|
| **Gateway de VPN** | Um gateway de VPN é um objeto em uma rede virtual que fornece um túnel criptografado de uma rede virtual para uma rede externa. O túnel criptografado pode ser site a site para comunicação bidirecional com um ambiente local, outra rede virtual ou ambiente de nuvem ou ponto a site para comunicação com um único ponto de extremidade.|
| **Integração de VNet de PaaS** | Muitos recursos de PaaS podem ser implantados ou integrados a uma rede virtual. Alguns recursos de PaaS podem ser totalmente integrados a uma VNet e podem ser acessados por meio de endereços IP privados. Outros, como Azure Load Balancer e Aplicativo Azure gateway, podem ter uma interface externa com um endereço IP público e uma interface interna com um endereço IP privado dentro da rede virtual. Nessa instância, o tráfego pode entrar na rede virtual por meio do recurso de PaaS.|
|

### <a name="paas-ingress-components"></a>Componentes de entrada de PaaS

|Componente | Descrição|
|---|---|
|**Nome de Anfitrião** | Os recursos de PaaS do Azure são identificados por um nome de host público exclusivo que é atribuído quando o recurso é criado. Esse nome de host é então registrado em um domínio DNS público, onde pode ser resolvido para um endereço IP público.|
|**IP público** | A menos que seja implantado em uma configuração integrada de VNet, as funcionalidades de PaaS do Azure são acessadas por meio de um endereço IP público e roteável Esse endereço pode ser dedicado a recursos específicos, como um Load Balancer público, ou pode ser associado a um recurso específico que tem um ponto de entrada compartilhado para várias instâncias, como armazenamento ou SQL. Esse IP público endereçado pode ser acessado pela Internet, ExpressRoute ou de endereços IP públicos IaaS por meio da rede de backbone do Azure.|
|**Pontos finais de serviço** | Os pontos de extremidade de serviço fornecem uma conexão direta e privada de uma rede virtual para um recurso de PaaS específico. Pontos de extremidade de serviço, que só estão disponíveis para um subconjunto de recursos de PaaS, fornecem maior desempenho e segurança para recursos em uma VNet acessando PaaS.|
|

### <a name="security-controls"></a>Controles de segurança

|Componente | Descrição|
|---|---|
|**NSGs (grupos de segurança de rede)** | NSGs controle o tráfego dentro e fora dos recursos de rede virtual no Azure. NSGs aplicar regras para os fluxos de tráfego que são permitidos ou negados, o que inclui o tráfego no Azure e entre o Azure e redes externas, como no local ou na Internet. NSGs são aplicados a sub-redes em uma rede virtual ou a interfaces de rede individuais.|
|**Firewall de PaaS** | Muitos recursos de PaaS, como armazenamento e SQL, têm um firewall embutido para controlar o tráfego de rede de entrada para o recurso específico. As regras podem ser criadas para permitir ou negar conexões de endereços IP e/ou redes virtuais específicas.|
|**Autenticação de PaaS e controle de acesso** | Como parte de uma abordagem em camadas de segurança, os recursos de PaaS fornecem vários mecanismos para autenticar usuários e controlar privilégios e acesso.|
|**Azure Policy** | Azure Policy é um serviço no Azure para criar, atribuir e gerenciar políticas. Essas políticas usam regras para controlar os tipos de recursos que podem ser implantados e a configuração desses recursos. As políticas podem ser usadas para impor a conformidade, impedindo que os recursos sejam implantados se eles não atenderem aos requisitos ou puderem ser usados para o monitoramento para relatar o status de conformidade.|
|

## <a name="general-guidance"></a>Documentação de orientação geral

Para projetar e criar soluções seguras no Azure, é essencial entender e controlar o tráfego de rede para que somente a comunicação autorizada e assegurada possa ocorrer. A intenção desta orientação e das diretrizes de componente específicas nas seções posteriores é descrever as ferramentas e os serviços que podem ser utiliseddos para aplicar os princípios descritos na _acsc proteção: Implementando a segmentação de_ rede e a separação entre cargas de trabalho do Azure. Isso inclui detalhar como criar uma arquitetura virtual para proteger recursos quando não é possível aplicar os mesmos controles físicos e de rede tradicionais que são possíveis em um ambiente local.

### <a name="specific-focus-areas"></a>Áreas de foco específicas

* Limitar o número de pontos de entrada para redes virtuais
* Limitar o número de endereços IP públicos
* Considere utilising um design de rede hub e spoke para redes virtuais, conforme discutido na documentação do Microsoft Virtual Data Centre (VDC)
* Produtos Utilise com recursos de segurança embutidos para conexões de entrada da Internet (por exemplo, gateway de aplicativo, gateway de API, soluções de virtualização de rede)
* Restringir os fluxos de comunicação para os recursos de PaaS somente aos necessários para a funcionalidade do sistema
* Implantar PaaS em uma configuração integrada de VNet para aumentar a diferenciação e o controle
* Configurar sistemas para usar mecanismos de criptografia alinhados com as diretrizes do consumidor ACSC e ISM
* Usar proteções baseadas em identidade, como autenticação e controle de acesso baseado em função, além de controles de rede tradicionais
* Implementar o ExpressRoute para conectividade com redes locais
* Implementar VPNs para tráfego administrativo e integração com redes externas
* Utilise Azure Policy restringir as regiões e os recursos somente aos que são necessários para a funcionalidade do sistema
* Utilise Azure Policy para impor a configuração de segurança de linha de base para recursos acessíveis pela Internet

### <a name="additional-resources"></a>Recursos adicionais

|Resource | Associar|
|---|---|
|Documentos normativos da Austrália e conformidade da política, incluindo diretrizes do consumidor|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Data Center Virtual do Azure|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)|
|Segmentação de rede ACSC|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|Segurança de nuvem ACSC para locatários| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|Manual de segurança de informações do ACSC|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>Diretrizes de componente

Esta seção fornece orientações adicionais sobre os componentes individuais que são relevantes para o tráfego de entrada para sistemas implantados no Azure. Cada seção descreve a intenção do componente específico com links para documentação e guias de configuração que podem ser usados para auxiliar no design e na criação de atividades.

## <a name="azure"></a>Azure

Toda a comunicação com os recursos no Azure passa pela infraestrutura de rede mantida pela Microsoft, que fornece conectividade e funcionalidade de segurança. Uma variedade de proteções são automaticamente colocadas em vigor pela Microsoft para proteger a infraestrutura de rede e a plataforma do Azure e recursos adicionais estão disponíveis como serviços no Azure para controlar o tráfego de rede e estabelecer a segmentação de rede e separação.

### <a name="ddos-protection"></a>Proteção contra DDOS

Os recursos acessíveis à Internet são suscetíveis a ataques de DDoS. Para proteger contra esses ataques, o Azure fornece proteções de DDoS em um nível básico e um padrão.

O básico é habilitado automaticamente como parte da plataforma do Azure, incluindo o monitoramento de tráfego sempre ativo e a mitigação em tempo real de ataques comuns de nível de rede, fornecendo os mesmos delimitações utilised pelo serviços online da Microsoft. Toda a escala da rede global do Azure pode ser usada para distribuir e atenuar o tráfego de ataque entre regiões. A proteção é fornecida para endereços IP públicos do Azure IPv4 e IPv6

O Standard fornece recursos de mitigação adicionais na camada de serviço básica que são ajustadas especificamente para os recursos de rede virtual do Azure. As políticas de proteção são ajustadas através de algoritmos de aprendizado de máquina e monitoramento de tráfego dedicado. A proteção é fornecida para endereços IP públicos do Azure IPv4.

|Resource|Associar|
|---|---|
|Visão geral da proteção contra DDoS do Azure|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)|
|Práticas recomendadas de DDoS do Azure|[https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices](https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices)|
|Gerenciando a proteção contra DDoS|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)|
|

### <a name="traffic-manager"></a>Gestor de Tráfego

O Gerenciador de tráfego é usado para gerenciar o tráfego de entrada controlando quais pontos de extremidade de um aplicativo recebem conexões. Para se proteger contra uma perda de disponibilidade de sistemas ou aplicativos devido a ataques de segurança cibernéticos ou para recuperar serviços após um comprometimento do sistema, o Gerenciador de tráfego pode ser usado para redirecionar o tráfego para instâncias de aplicativos disponíveis e em funcionamento.

|Resource|Associar|
|---|---|
|Descrição geral do Gestor de Tráfego | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)|
|Recuperação de desastre usando o DNS do Azure e o guia do Gerenciador de tráfego | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)|
|

### <a name="expressroute"></a>ExpressRoute

O ExpressRoute pode ser usado para estabelecer um caminho privado de um ambiente local para sistemas hospedados no Azure. Essa conexão pode fornecer maior confiabilidade e desempenho garantido com privacidade avançada para comunicações de rede. O Express Route permite que as entidades da Comunidade controlem o tráfego de entrada do ambiente local e definam endereços dedicados específicos à organização para usar para regras de firewall de entrada e listas de controle de acesso.

|Resource | Associar|
|---|---|
|Descrição Geral do ExpressRoute | [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)|
|Modelos de conectividade do ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models)|
|

### <a name="expressroute-private-peering"></a>Emparelhamento privado do ExpressRoute

O emparelhamento privado fornece um mecanismo para estender um ambiente local para o Azure usando apenas endereços IP privados. Isso permite que as entidades da Comunidade integrem redes virtuais do Azure e intervalos de endereços com serviços e sistemas locais existentes. O emparelhamento privado fornece garantia de que a comunicação entre o ExpressRoute é apenas para redes virtuais autorizadas pela organização. Se você usar o emparelhamento privado, as entidades da Comunidade deverão implementar NVA (soluções de virtualização de rede) em vez do gateway de VPN do Azure para estabelecer a comunicação de VPN segura com suas redes locais, conforme exigido pelas diretrizes de consumidor do ACSC.

|Resource | Associar|
|---|---|
|Visão geral do emparelhamento privado do ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|Guia de instruções de emparelhamento privado do ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)|
|

### <a name="expressroute-microsoft-peering"></a>Emparelhamento da Microsoft para ExpressRoute

O emparelhamento da Microsoft fornece uma conexão de baixa latência e alta velocidade aos serviços públicos da Microsoft sem a necessidade de atravessar a Internet. Isso fornece maior confiabilidade, desempenho e privacidade para conexões. Usando filtros de rota, as entidades da Comunidade podem restringir as comunicações apenas com as regiões do Azure de que precisam, mas isso inclui serviços hospedados por outras organizações e pode exigir recursos adicionais de filtragem ou inspeção entre o ambiente local e a Microsoft.

As entidades da Comunidade podem usar os endereços IP públicos dedicados estabelecidos por meio da relação de emparelhamento para identificar exclusivamente o ambiente local para uso em firewalls e listas de controle de acesso dentro dos recursos de PaaS.

Como alternativa, as entidades da Comunidade podem usar o emparelhamento da Microsoft do ExpressRoute como uma rede underlay para estabelecer a conectividade VPN por meio do gateway de VPN do Azure. Nesse modelo, não há nenhuma comunicação ativa da rede interna local para os serviços públicos do Azure no ExpressRoute, mas a conectividade segura por meio de redes virtuais privadas é alcançada em conformidade com as diretrizes do consumidor ACSC.

|Resource | Associar|
|---|---|
|Visão geral do emparelhamento da Microsoft para ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|Guia de instruções de emparelhamento da Microsoft para ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft)|
|

## <a name="iaas-ingress"></a>Entrada de IaaS

Esta seção fornece as diretrizes de componente para controlar o tráfego de entrada para componentes de IaaS. A IaaS inclui máquinas virtuais e outros recursos de computação que podem ser implantados e gerenciados em um Entrada na Rede virtual do Azure. Para que o tráfego chegue em sistemas implantados usando o IaaS, ele deve ter um ponto de entrada para a rede virtual, que pode ser estabelecido por meio de um endereço IP público, um gateway de rede virtual ou uma relação de emparelhamento de rede virtual.

### <a name="network-interface"></a>Interface de rede

As interfaces de rede são os pontos de entrada para todo o tráfego para uma máquina virtual. As interfaces de rede permitem a configuração do endereçamento IP e podem ser usadas para aplicar NSGs ou roteamento de tráfego por meio de uma solução de virtualização de rede. As interfaces de rede para máquinas virtuais devem ser planejadas e configuradas adequadamente para alinhar com os objetivos gerais de segmentação de rede e de diferenciação.

|Resource | Associar|
|---|---|
|Criar, alterar ou excluir uma interface de rede | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)|
|Endereçamento IP da interface de rede | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)|
|

### <a name="subnet"></a>Subnet

As sub-redes são um componente crucial para segmentação de rede e diferenciação no Azure. As sub-redes podem ser usadas de forma semelhante para fornecer separação entre sistemas. NSGs pode ser aplicado a sub-redes para restringir fluxos de comunicação de entrada apenas para aqueles necessários para a funcionalidade do sistema. As sub-redes podem ser usadas como endereços de origem e de destino para regras de firewall e listas de controle de acesso e podem ser configuradas para pontos de extremidade de serviço para fornecer conectividade com os recursos de PaaS.

|Resource | Associar|
|---|---|
|Adicionar, alterar ou excluir uma sub-rede de rede virtual | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)|
|

### <a name="virtual-network-vnet"></a>Rede Virtual (VNet)

VNets são um dos blocos de construção fundamentais para a rede no Azure. As redes virtuais definem um espaço de endereço IP e o limite de roteamento a serem usados em uma variedade de sistemas. As redes virtuais são divididas em sub-redes e todas as sub-redes em uma rede virtual têm uma rota de rede direta entre si. Usando gateways de rede virtual (ExpressRoute ou VPN), os sistemas em uma rede virtual podem ser acessíveis para ambientes locais e externos. Compreender as redes virtuais e os parâmetros de configuração associados e o roteamento é crucial na compreensão e no controle do tráfego de rede de entrada.

|Resource | Associar|
|---|---|
|Visão geral das redes virtuais | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)|
|Guia de instruções do plano de redes virtuais | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
Criar um início rápido de rede virtual | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)|
|

### <a name="vnet-peering"></a>VNet Peering

O emparelhamento VNet é usado para fornecer um caminho de comunicação direta entre duas redes virtuais. Quando o emparelhamento é estabelecido, os hosts em uma rede virtual têm um caminho de roteamento de alta velocidade diretamente para hosts em outra rede virtual. O NSGs ainda se aplica ao tráfego, já que parâmetros de configuração normal e avançada podem ser usados para definir se a comunicação por meio de gateways de rede virtual ou de outros sistemas externos é permitida.

|Resource | Associar|
|---|---|
|Visão geral de emparelhamento de rede virtual |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)|
|Criar, alterar ou excluir um emparelhamento de rede virtual | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="public-ip-on-vnet"></a>IP público na VNET

Os endereços IP públicos são usados para fornecer um caminho de comunicação de entrada para os serviços implantados em uma rede virtual. As entidades da Comunidade devem planejar a alocação de endereços IP públicos com cuidado e atribuí-los a recursos em que há um requisito original. Como uma prática de design geral, os endereços IP públicos devem ser alocados a recursos com recursos de segurança internos, como o gateway de aplicativo ou soluções de virtualização de rede para fornecer um ponto de entrada público seguro e controlado a uma rede virtual.

|Resource | Associar|
|---|---|
|Visão geral de Endereços IP Públicos | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)|
|Criar, alterar ou excluir um endereço IP público | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)|
|

### <a name="expressroute-gateway"></a>Gateway de ExpressRoute

Os gateways de ExpressRoute fornecem um ponto de entrada do ambiente local e devem ser implantados para atender aos requisitos de segurança, disponibilidade, financeiro e desempenho. Gateways de ExpressRoute fornecem uma largura de banda de rede definida e incorrem em custos de uso após a implantação. As redes virtuais podem ter apenas um gateway de ExpressRoute, mas isso pode ser conectado a vários circuitos de ExpressRoute e pode ser aproveitado por várias redes virtuais por meio de emparelhamento de VNet, permitindo que várias redes virtuais compartilhem largura de banda e conectividade. Deve-se ter cuidado para configurar o roteamento entre ambientes locais e redes virtuais usando gateways de ExpressRoute para garantir a conectividade de ponta a ponta usando pontos de entrada de rede controlados conhecidos. As entidades da Comunidade usando o gateway de ExpressRoute também devem implantar soluções de virtualização de rede para estabelecer conectividade VPN com o ambiente local para conformidade com as diretrizes do consumidor ACSC.

|Resource | Associar|
|---|---|
|Visão geral do gateway de ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)|
|Configurar um gateway de rede virtual para o ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)|
|

### <a name="vpn-gateway"></a>Gateway de VPN

O gateway de VPN do Azure fornece um ponto de rede de entrada de uma rede externa para conexões seguras site a site ou ponto a site. Gateways de VPN fornecem uma largura de banda de rede definida e incorrem em custos de uso após a implantação. As entidades da Comunidade utilising gateway de VPN devem garantir que ela esteja configurada de acordo com as diretrizes de consumidor do ACSC. As redes virtuais podem ter apenas um gateway de VPN, mas isso pode ser configurado com vários túneis e pode ser aproveitado por várias redes virtuais por meio de emparelhamento de VNet, permitindo que várias redes virtuais compartilhem largura de banda e conectividade. Os gateways de VPN podem ser estabelecidos pela Internet ou via ExpressRoute por meio do emparelhamento da Microsoft.

|Resource | Associar|
|---|---|
|Visão geral do gateway de VPN | [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)|
|Planear e conceber para o Gateway de VPN | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
|Configuração do gateway de VPN para agências governamentais australianas|[Configuração de IPSEC necessária para agências governamentais australianas](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>Integração de VNet de PaaS

Aproveitar a PaaS pode fornecer funcionalidade e disponibilidade aprimoradas e reduzir a sobrecarga de gerenciamento, mas deve ser protegido adequadamente. Para aumentar o controle, impor a segmentação de rede ou fornecer um ponto de entrada de ingresso seguro para aplicativos e serviços, muitos recursos de PaaS podem ser integrados a uma rede virtual.

Para fornecer um ponto de entrada seguro, os recursos de PaaS, como o gateway de aplicativo, podem ser configurados com uma interface pública externa e uma interface interna e privada para a comunicação com os serviços de aplicativo. Isso impede a necessidade de configurar servidores de aplicativos com endereços IP públicos e expô-los a redes externas.

Para usar o PaaS como uma parte integrada da arquitetura do sistema ou do aplicativo, a Microsoft fornece vários mecanismos para implantar a PaaS em uma rede virtual. A metodologia de implantação restringe o acesso de entrada de redes externas, como a Internet, enquanto fornece conectividade e integração com sistemas e aplicativos internos. Os exemplos incluem ambientes de serviço de aplicativo, instâncias gerenciadas do SQL e muito mais.

|Resource | Associar|
|---|---|
|Integração de rede virtual para serviços do Azure | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)|
|Integre seu aplicativo com um guia de instruções da rede virtual do Azure | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)|
|

## <a name="paas-ingress"></a>Entrada de PaaS

Os recursos de PaaS fornecem oportunidades de maior capacidade e gerenciamento simplificado, mas introduzem complexidades no endereçamento de requisitos para segmentação e diferenciação de rede. Os recursos de PaaS normalmente são configurados com endereços IP públicos e podem ser acessados pela Internet.  Ao criar sistemas usando recursos de PaaS, tenha cuidado para identificar todos os fluxos de comunicação necessários entre os componentes do sistema e as regras de segurança de rede criadas para permitir apenas essa comunicação. Como parte de uma abordagem de defesa aprofundada à segurança, os recursos de PaaS devem ser configurados com criptografia, autenticação e permissões e controles de acesso apropriados.  

### <a name="hostname"></a>Nome do Anfitrião

Os recursos de PaaS são identificados exclusivamente por nomes de host para permitir que várias instâncias do mesmo serviço sejam hospedadas no mesmo endereço IP público. Os nomes de host exclusivos são especificados quando os recursos são criados e existem dentro de domínios DNS de propriedade da Microsoft. Os nomes de host específicos para serviços autorizados podem ser usados em ferramentas de segurança com recursos de filtragem de nível de aplicativo. Determinados serviços também podem ser configurados com domínios personalizados, conforme necessário.

|Resource | Associar|
|---|---|
|Muitos namespaces públicos usados pelos serviços do Azure podem ser obtidos por meio do PowerShell executando o comando Get-AzureRMEnvironment | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment)|
|Configurando um nome de domínio personalizado para um serviço de nuvem do Azure | Os serviços de aplicativos e outros podem ter domínios personalizados[https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal)|
|

### <a name="public-ip-for-paas"></a>IP público para PaaS

Os endereços IP públicos para recursos de PaaS são alocados com base na região em que o serviço é hospedado ou implantado. Para criar regras de segurança de rede e a topologia de roteamento apropriadas para segmentação de rede e diferenciação que abrangem redes virtuais do Azure, PaaS e ExpressRoute e conectividade com a Internet, uma compreensão das regiões e alocação de endereços IP públicos é Necessário. O Azure aloca endereços IP de um pool alocado para cada região do Azure. A Microsoft torna os endereços usados em cada região disponíveis para download, que são atualizados de maneira regular e controlada. Os serviços que estão disponíveis em cada região também mudam frequentemente à medida que novos serviços são liberados ou os serviços são implantados mais amplamente. As entidades da Comunidade devem revisar esses materiais regularmente e podem aproveitar a automação para manter os sistemas conforme necessário. Endereços IP específicos para alguns serviços hospedados em cada região podem ser obtidos entrando em contato com o suporte da Microsoft.

|Resource | Associar|
|---|---|
|Microsoft Azure intervalos de IP do datacenter | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|Serviços do Azure por região | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Austrália Central, Austrália-Central-2, Austrália-leste, Austrália-sudeste & Products = All](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>Pontos finais de serviço

Os pontos de extremidade de serviço de rede virtual fornecem uma conexão de rede de entrada privada de alta velocidade para sub-redes em uma rede virtual para consumir recursos de PaaS específicos. Para segmentação de rede completa e diferenciação da capacidade de PaaS, a capacidade de PaaS deve ser configurada para aceitar conexões somente das redes virtuais necessárias. Nem todos os recursos de PaaS dão suporte a uma combinação de regras de firewall que incluem pontos de extremidade de serviço e regras baseadas em endereço IP tradicionais, portanto, deve-se ter cuidado para entender o fluxo de comunicações necessário para a funcionalidade e a administração do aplicativo para que a implementação desses controles de segurança não afete a disponibilidade do serviço.

|Resource | Associar|
|---|---|
|Visão geral dos pontos de extremidade de serviço | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
|Tutorial |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

## <a name="security"></a>Segurança

A implementação de segmentação de rede e controles de diferenciação em recursos de IaaS e PaaS é obtida por meio da proteção dos próprios recursos e da implementação de caminhos de comunicação controlados a partir dos sistemas que se comunicarão com o recursos.

Projetar e criar soluções no Azure é um processo de criação de uma arquitetura lógica para entender, controlar e monitorar recursos de rede em toda a presença do Azure. Essa arquitetura lógica é um software definido na plataforma do Azure e assume o lugar de uma topologia de rede física implementada em ambientes de rede tradicionais.

A arquitetura lógica que é criada deve fornecer a funcionalidade necessária para a usabilidade, mas também deve fornecer a visibilidade e o controle necessários para segurança e integridade.

Atingir esse resultado se baseia na implementação das ferramentas de segmentação de rede e segregação necessárias, mas também na proteção e na imposição da topologia de rede e da implementação dessas ferramentas.

As informações fornecidas neste guia podem ser usadas para ajudar a identificar as fontes de tráfego de entrada que precisam ser permitidas e as maneiras como o tráfego pode ser controlado ou restrito.

### <a name="network-security-groups-nsgs"></a>NSGs (grupos de segurança de rede)

NSGs são usados para especificar o tráfego de entrada e de saída permitido para uma sub-rede ou uma interface de rede específica. Ao configurar o NSGs, as entidades da Comunidade devem usar uma abordagem de lista de permissões em que as regras são configuradas para permitir o tráfego necessário com uma regra padrão configurada para negar todo o tráfego que não corresponde a uma instrução de permissão específica. É preciso tomar cuidado ao planejar e configurar o NSGs para garantir que todo o tráfego de entrada e saída necessário seja capturado adequadamente. Isso inclui a identificação e a compreensão de todos os intervalos de endereços IP privados utilised nas redes virtuais do Azure e no ambiente local, além de serviços específicos da Microsoft, como os requisitos de gerenciamento de Azure Load Balancer e PaaS. Os indivíduos envolvidos no design e na implementação de grupos de segurança de rede também devem entender o uso de marcas de serviço e grupos de segurança de aplicativo para criar regras de segurança refinadas, de serviço e específicas do aplicativo.

|Resource | Associar|
|---|---|
|Visão geral de segurança de rede | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)
|Criar, alterar ou excluir um grupo de segurança de rede | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

## <a name="paas-firewall"></a>Firewall de PaaS

Um firewall de PaaS é um recurso de controle de acesso à rede que pode ser aplicado a determinados serviços de PaaS. Ele permite que a filtragem de endereço IP ou a filtragem de redes virtuais específicas sejam configuradas para restringir o tráfego de entrada para a instância de PaaS específica. Para recursos de PaaS que incluem um firewall, as políticas de controle de acesso à rede devem ser configuradas para permitir apenas o tráfego de entrada necessário com base nos requisitos do aplicativo.  

|Resource | Associar|
|---|---|
|Regras de firewall de IP e SQL Data Warehouse do banco de dados SQL do Azure | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)|
|Segurança de rede de armazenamento | [https://docs.microsoft.com/azure/storage/common/storage-network-security](https://docs.microsoft.com/azure/storage/common/storage-network-security)|
|

## <a name="paas-authentication-and-access-control"></a>Autenticação de PaaS e controle de acesso

Dependendo do recurso de PaaS e sua finalidade, o uso de controles de rede para restringir o acesso pode não ser possível ou prático. Como parte do modelo de segurança em camadas para PaaS, o Azure fornece uma variedade de mecanismos de autenticação e controle de acesso para restringir o acesso a um serviço, mesmo que o tráfego de rede seja permitido. Os mecanismos de autenticação típicos para recursos de PaaS incluem Azure Active Directory, autenticação no nível do aplicativo e chaves compartilhadas ou assinaturas de acesso. Depois que um usuário é identificado com segurança, as funções podem ser utilised para controlar as ações que o usuário pode executar. Essas ferramentas podem ser utilised como uma alternativa ou como uma medida complementar para restringir o acesso aos serviços.

|Resource | Associar|
|---|---|
|Controlando e concedendo acesso ao banco de dados SQL e SQL Data Warehouse | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)|
|Autorização para os serviços de armazenamento do Azure | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Política do Azure

Azure Policy é um componente fundamental para impor e manter a integridade da arquitetura lógica do ambiente do Azure. Devido à variedade de serviços e caminhos de tráfego de rede de entrada disponíveis por meio dos serviços do Azure, é crucial que as entidades da Comunidade estejam cientes dos recursos que existem em seu ambiente e dos pontos de entrada de rede disponíveis. Para garantir que os pontos de entrada de rede não autorizados não sejam criados no ambiente do Azure, as entidades da Comunidade devem aproveitar Azure Policy para controlar os tipos de recursos que podem ser implantados e a configuração desses recursos. Exemplos práticos incluem a restrição de recursos somente para os autorizados e aprovados para uso, impondo a criptografia HTTPS no armazenamento e exigindo que NSGs sejam adicionados a sub-redes.

|Resource | Associar|
|---|---|
|Visão geral de Azure Policy | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|Política de exemplo dos tipos de recursos permitidos | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)
|Garantir a política de exemplo de conta de armazenamento HTTPS|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account)_
|Forçar NSG em uma política de exemplo de sub-rede| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="next-steps"></a>Passos Seguintes

Examine o artigo sobre [Gerenciamento de tráfego de saída de gateway e controle](gateway-egress-traffic.md) para obter detalhes sobre como gerenciar fluxos de tráfego do seu ambiente do Azure para outras redes usando seus componentes de gateway no Azure.
