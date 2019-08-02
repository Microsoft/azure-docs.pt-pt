---
title: Controlando o tráfego de saída na Austrália do Azure
description: Elementos principais de controle do tráfego de saída no Azure para atender aos requisitos governamentais australianos de gateways de Internet seguros
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: grgale
ms.openlocfilehash: 7922846d161b7a0cbda32101b6bbb40a1741b323
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602078"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>Controlando o tráfego de saída na Austrália do Azure

Um componente fundamental da proteção de sistemas de ICT é o controle do tráfego de rede. Restringir a comunicação apenas com o tráfego necessário para que um sistema funcione reduz o potencial de comprometimento. Visibilidade e controle sobre os sistemas externos aos quais seus aplicativos e serviços se comunicam ajuda a detectar sistemas comprometidos e a tentar ou vazamento de dados com êxito. Este artigo fornece informações sobre como o tráfego de rede de saída (saída) funciona no Azure e fornece recomendações para a implementação de controles de segurança de rede para um sistema conectado à Internet que se alinhe com o ACSC (centro de segurança da Austrália) Orientação do consumidor e a intenção do ISM (manual de segurança da informação) da ACSC.

## <a name="requirements"></a>Requisitos

Os requisitos de segurança gerais para sistemas da Comunidade são definidos no ISM. Para auxiliar as entidades da Comunidade na implementação da segurança de rede, _o acsc publicou o acsc Protect: Implementando a segmentação e_a diferenciação de rede e para auxiliar na proteção de sistemas em ambientes de nuvem, o acsc publicou a _segurança de computação em nuvem para locatários_.

Os documentos ACSC descrevem o contexto para implementar a segurança de rede e controlar o tráfego, além de fornecer recomendações práticas para design e configuração de rede.

Os seguintes requisitos principais para controlar o tráfego de saída no Azure foram identificados nos documentos ACSC.

Descrição|Source
--------------- |------------------
**Implemente a segmentação e**a diferenciação de rede, por exemplo, use uma arquitetura de n camadas, usando firewalls baseados em host e controles de acesso à rede para limitar a conectividade de rede de entrada e saída somente para portas e protocolos necessários.| [Computação em nuvem para locatários](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
**Implemente alta largura de banda adequada, baixa latência e conectividade de rede confiável** entre o locatário (incluindo os usuários remotos do locatário) e o serviço de nuvem para atender aos requisitos de disponibilidade do locatário  | [ACSC proteger: Implementando a segmentação de rede e a diferenciação](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Aplique tecnologias em mais do que apenas na camada de rede**. Cada host e rede devem ser segmentados e separados, sempre que possível, no nível mais baixo que possa ser praticamente gerenciado. Na maioria dos casos, isso se aplica a partir da camada de link de dados até e incluindo a camada de aplicativo; no entanto, em ambientes confidenciais, o isolamento físico pode ser apropriado. As medidas baseadas em host e em toda a rede devem ser implantadas de maneira complementar e ser monitoradas centralmente. Implementar apenas um firewall ou um dispositivo de segurança como a única medida de segurança não é suficiente. |[ACSC proteger: Implementando a segmentação de rede e a diferenciação](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Use os princípios de privilégios mínimos e precise de ‐ para ‐ saber**. Se um host, serviço ou rede não precisar se comunicar com outro host, serviço ou rede, ele não deverá ser permitido. Se um host, serviço ou rede precisar apenas se comunicar com outro host, serviço ou rede em uma porta ou protocolo específico, ele deverá ser restrito apenas a essas portas e protocolos. A adoção desses princípios em uma rede irá complementar a minimisation de privilégios de usuário e aumentar significativamente a postura de segurança geral do ambiente. |[ACSC proteger: Implementando a segmentação de rede e a diferenciação](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Separe os hosts e as redes com base em sua sensibilidade ou importância para as operações de negócios**. Isso pode incluir o uso de diferentes hardware ou plataformas, dependendo de diferentes classificações de segurança, domínios de segurança ou requisitos de disponibilidade/integridade para determinados hosts ou redes. Em particular, separe as redes de gerenciamento e considere isolar fisicamente redes de gerenciamento fora de banda para ambientes confidenciais. |[ACSC proteger: Implementando a segmentação de rede e a diferenciação](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Identificar, autenticar e autorizarr o acesso por todas as entidades a todas as outras entidades**. Todos os usuários, hosts e serviços devem ter acesso a todos os outros usuários, hosts e serviços restritos apenas aos necessários para executar suas funções ou responsabilidades designadas. Todos os serviços herdados ou locais que ignoram ou desativam a força da identificação, da autenticação e dos serviços autorização devem ser desabilitados sempre que possível e têm seu uso bastante monitorado. |[ACSC proteger: Implementando a segmentação de rede e a diferenciação](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Implementar permitir listagem de tráfego de rede em vez de negar listagem**. Só permita o acesso para tráfego de rede válido conhecido (tráfego que é identificado, autenticado e autorizado), em vez de negar acesso ao tráfego de rede incorreto conhecido (por exemplo, bloqueando um endereço ou serviço específico). As listas de permissões resultam em uma política de segurança superior para negar listas e melhoram significativamente a capacidade de detectar e avaliar possíveis invasões de rede. |[ACSC proteger: Implementando a segmentação de rede e a diferenciação](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Definir uma lista de permissões de sites permitidos e bloquear todos os sites não listados** efetivamente remove uma das técnicas de entrega de dados e vazamento mais comuns usadas por um adversário. Se os usuários têm um requisito legítimo para acessar vários sites ou uma lista rápida de alterações de sites; Você deve considerar os custos de tal implementação. Até mesmo uma lista de permissões relativamente permissivas oferece melhor segurança do que depender de listas de negação ou nenhuma restrição, ao mesmo tempo que ainda reduz os custos de implementação. Um exemplo de uma lista de permissões permissivas pode estar permitindo o subdomínio australiano inteiro, ou seja, ' *. au ', ou permitindo os principais sites 1.000 da classificação do site Alexa (depois de filtrar domínios de DDNS (sistema de nomes de domínio dinâmico) e outros domínios inadequados).| [ISM (manual de segurança de informações do governo australiano)](https://www.cyber.gov.au/ism)
|

Este artigo fornece informações e recomendações sobre como o tráfego de rede que sai do ambiente do Azure é controlado. Ele abrange sistemas implantados no Azure usando IaaS (infraestrutura como serviço) e PaaS (plataforma como serviço).

O artigo [tráfego de entrada do gateway](gateway-ingress-traffic.md) aborda o tráfego de rede entrando no ambiente do Azure e é o complemento deste artigo para cobertura total de controle de rede.

## <a name="architecture"></a>Arquitetura

Para controlar adequadamente o tráfego de saída, ao projetar e implementar a segurança de rede, você deve primeiro entender como o tráfego de rede de egresso funciona no Azure em IaaS e PaaS. Esta seção fornece uma visão geral de como o tráfego de saída gerado por um recurso hospedado no Azure é processado e os controles de segurança disponíveis para restringir e controlar esse tráfego.

### <a name="architecture-components"></a>Componentes da arquitetura

O diagrama arquitetônico mostrado aqui descreve os caminhos possíveis que o tráfego de rede pode tomar ao sair de um sistema que é implantado em uma sub-rede em uma rede virtual. O tráfego em uma rede virtual é gerenciado e governado em um nível de sub-rede, com regras de roteamento e segurança aplicadas aos recursos contidos no. Os componentes relacionados ao tráfego de saída são divididos em sistemas, rotas efetivas, tipos de próximo salto, controles de segurança e egresso de PaaS.

![Arquitetura](media/egress-traffic.png)

### <a name="systems"></a>Systems

Os sistemas são os recursos do Azure e os componentes relacionados que geram tráfego de saída em uma sub-rede IP que faz parte de uma rede virtual.

| Componente | Descrição |
| --- | ---|
|Rede Virtual (VNet) | Uma VNet é um recurso fundamental do Azure que fornece uma plataforma e limite para a implantação de recursos e a habilitação da comunicação. A VNet existe em uma região do Azure e define o espaço de endereço IP e os limites de roteamento para recursos integrados de VNet, como máquinas virtuais.|
|Subnet | Uma sub-rede é um intervalo de endereços IP que é criado em uma VNet. Várias sub-redes podem ser criadas em uma VNet para segmentação de rede.|
|Interface de Rede| Uma interface de rede é um recurso que existe no Azure. Ele é anexado a uma máquina virtual e atribuído a um endereço IP não roteável de Internet privado da sub-rede à qual ela está associada. Esse endereço IP é atribuído dinamicamente ou estaticamente por meio de Azure Resource Manager.|
|IPs Públicos| Um IP público é um recurso que reserva um dos endereços IP públicos de propriedade da Microsoft e roteáveis da região especificada para uso na rede virtual. Ele pode ser associado a uma interface de rede específica ou a um recurso de PaaS, que permite que o recurso se comunique com a Internet, o ExpressRoute e outros sistemas PaaS.|
|

### <a name="routes"></a>Rotas

O caminho que o tráfego de saída usa depende das rotas efetivas para esse recurso, que é o conjunto resultante de rotas determinado pela combinação de rotas aprendit de todas as fontes possíveis e o aplicativo da lógica de roteamento do Azure.

| Componente | Descrição |
|--- | ---|
|Rotas de sistema| O Azure cria automaticamente as rotas do sistema e atribui-as a cada sub-rede numa rede virtual. As rotas do sistema não podem ser criadas ou removidas, mas algumas podem ser substituídas por rotas personalizadas. O Azure cria rotas de sistema padrão para cada sub-rede e adiciona rotas padrão adicionais opcionais a sub-redes específicas, ou a cada sub-rede, quando recursos específicos do Azure são utilised.|
|Pontos Finais de Serviço| Os pontos de extremidade de serviço fornecem uma conexão de egresso direta e privada de uma sub-rede para um recurso de PaaS específico. Pontos de extremidade de serviço, que só estão disponíveis para um subconjunto de recursos de PaaS, fornecem maior desempenho e segurança para recursos em uma VNet acessando PaaS.|
|Tabelas de Rota| Uma tabela de rotas é um recurso no Azure que pode ser criado para especificar UDRs (rotas definidas pelo usuário) que podem complementar ou substituir rotas de sistemas ou rotas aprendam por meio de Border Gateway Protocol. Cada UDR especifica uma rede, uma máscara de rede e um próximo salto. Uma tabela de rotas pode ser associada a uma sub-rede e a mesma tabela de rotas pode ser associada a várias sub-redes, mas uma sub-rede só pode ter zero ou uma tabela de rotas.|
|Border Gateway Protocol (BGP)| O BGP é um protocolo de roteamento de sistema entre autônomo. Um sistema autônomo é uma rede ou um grupo de redes em uma administração comum e com políticas de roteamento comuns. O BGP é usado para trocar informações de roteamento entre sistemas autônomos. O BGP pode ser integrado em redes virtuais por meio de gateways de rede virtual.|
|

### <a name="next-hop-types-defined"></a>Tipos do próximo salto definidos

Cada rota no Azure inclui o intervalo de rede e a máscara de sub-rede associada e o próximo salto, que determina como o tráfego é processado.

Tipo de Salto Seguinte | Descrição
---- | ----
**Rede Virtual** | Roteia o tráfego entre intervalos de endereços dentro do espaço de endereço de uma rede virtual. O Azure cria uma rota com um prefixo de endereço que corresponde a cada intervalo de endereços definido dentro do espaço de endereços de uma rede virtual. Se o espaço de endereço de rede virtual tiver vários intervalos de endereços definidos, o Azure criará uma rota individual para cada intervalo de endereços. O Azure roteia automaticamente o tráfego entre sub-redes em uma VNet usando as rotas criadas para cada intervalo de endereços.
**VNet peering** | Quando um emparelhamento de rede virtual é criado entre duas redes virtuais, uma rota é adicionada para cada intervalo de endereços de cada rede virtual para a rede virtual à qual ela está emparelhada. O tráfego é roteado entre as redes virtuais emparelhadas da mesma forma que as sub-redes em uma rede virtual.
**Gateway de rede virtual** | Uma ou mais rotas com o gateway de rede virtual listado como o tipo do próximo salto são adicionadas quando um gateway de rede virtual é adicionado a uma rede virtual. As rotas incluídas são aquelas configuradas no recurso de gateway de rede local e em todas as rotas aprendidas via BGP.
**Dispositivo virtual** | Uma solução de virtualização normalmente executa um aplicativo de rede, como um firewall. O dispositivo virtual permite que o processamento adicional do tráfego ocorra, como filtragem, inspeção ou conversão de endereços. Cada rota com o tipo de salto de dispositivo virtual também deve especificar um endereço IP do próximo salto.
**VirtualNetworkServiceEndpoint** | Os endereços IP públicos para um serviço específico são adicionados como rotas para uma sub-rede com um próximo salto de VirtualNetworkServiceEndpoint quando um ponto de extremidade de serviço é habilitado. Os pontos de extremidade de serviço são habilitados para serviços individuais em sub-redes individuais em uma rede virtual. Os endereços IP públicos dos serviços do Azure mudam periodicamente. O Azure gere os endereços na tabela de rotas automaticamente quando os mesmos são modificados.
**Internet** | O tráfego com um próximo salto da Internet sairá da rede virtual e será automaticamente convertido em um endereço IP público de um pool dinâmico disponível na região do Azure associada ou usando um endereço IP público configurado para esse recurso. Se o endereço de destino for para um dos serviços do Azure, o tráfego será roteado diretamente para o serviço pela rede de backbone do Azure, em vez de rotear o tráfego para a Internet. O tráfego entre os serviços do Azure não atravessa a Internet, independentemente da região do Azure na qual a rede virtual existe ou da região em que uma instância do serviço Azure está implementada.
**Nenhum** | O tráfego com um próximo salto de nenhum é Descartado. O Azure cria rotas padrão do sistema para prefixos de endereço reservado sem nenhum como o tipo do próximo salto. As rotas com um próximo salto de nenhum também podem ser adicionadas usando tabelas de rotas para impedir que o tráfego seja roteado para redes específicas.
|

### <a name="security-controls"></a>Controles de segurança

Controlar | Descrição
----- | -----
**NSGs (grupos de segurança de rede)** | NSGs controle o tráfego dentro e fora dos recursos de rede virtual no Azure. NSGs aplicar regras para os fluxos de tráfego que são permitidos ou negados, o que inclui o tráfego no Azure e entre o Azure e redes externas, como no local ou na Internet. NSGs são aplicados a sub-redes em uma rede virtual ou a interfaces de rede individuais.
**Firewall do Azure** | O Firewall do Azure é um serviço de segurança de rede gerenciado baseado em nuvem que protege os recursos da rede virtual do Azure. É uma firewall com total monitoração de estado como um serviço com elevada disponibilidade incorporada e escalabilidade da cloud sem restrições. O Firewall do Azure pode ser configurado com regras de filtragem de rede tradicionais baseadas em endereços IP, protocolos e portas, mas também dá suporte à filtragem com base em nomes de domínio totalmente qualificados (FQDN), marcas de serviço e inteligência de ameaças embutidas.
**Solução de virtualização de rede (NVA)** | Soluções de virtualização de rede são mídia de máquina virtual que pode fornecer rede, segurança e outras funções para o Azure. O NVAs dá suporte à funcionalidade e aos serviços de rede na forma de VMs em redes virtuais e implantações. O NVAs pode ser usado para atender a requisitos específicos, integrar com ferramentas operacionais e de gerenciamento, ou para fornecer consistência com os produtos existentes. O Azure suporta uma ampla lista de aplicações virtuais de rede externas, incluindo firewalls de aplicações Web (WAF), firewalls, gateways/routers, controladores de entrega de aplicações (ADC) e otimizadores WAN.
**Políticas de ponto de extremidade de serviço (versão prévia)** | As políticas de ponto de extremidade de serviço de rede virtual permitem filtrar o tráfego de rede virtual para os serviços do Azure, permitindo apenas recursos de serviço do Azure específicos, em pontos de extremidade de serviço. As políticas de ponto final proporcionam um controlo de acesso granular sobre o tráfego da rede virtual para os serviços do Azure.
**Azure Policy** | Azure Policy é um serviço no Azure para criar, atribuir e gerenciar políticas. Essas políticas usam regras para controlar os tipos de recursos que podem ser implantados e a configuração desses recursos. As políticas podem ser usadas para impor a conformidade, impedindo que os recursos sejam implantados se eles não atenderem aos requisitos ou puderem ser usados para o monitoramento para relatar o status de conformidade.
|

### <a name="paas-egress"></a>Egresso de PaaS

A maioria dos recursos de PaaS não gera tráfego de saída, mas responde a solicitações de entrada (como um gateway de aplicativo, armazenamento, banco de dados SQL, etc.) ou dados de retransmissão de outros recursos (como o barramento de serviço e a retransmissão do Azure). Os fluxos de comunicação de rede entre os recursos de PaaS, como os serviços de aplicativos para armazenamento ou bancos de dados SQL, são controlados e contidos pelo Azure e protegidos por meio de identidade e outros controles de configuração de recurso em vez de segmentação de rede ou diferenciação.

Os recursos de PaaS implantados em uma rede virtual recebem endereços IP dedicados e estão sujeitos a qualquer controle de roteamento e NSGs da mesma maneira que outros recursos na rede virtual. Os recursos de PaaS que não existem em uma rede virtual Utilise um pool de endereços IP que são compartilhados entre todas as instâncias do recurso, que são publicadas por meio da documentação da Microsoft ou podem ser determinadas por meio de Azure Resource Manager.

## <a name="general-guidance"></a>Documentação de orientação geral

Para projetar e criar soluções seguras no Azure, é essencial entender e controlar o tráfego de rede para que somente a comunicação autorizada e assegurada possa ocorrer. A intenção deste guia e das diretrizes de componente específicas nas seções posteriores é descrever as ferramentas e os serviços que podem ser utiliseddos para aplicar os princípios descritos na [acsc proteção: Implementando a segmentação de](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) rede e a separação entre cargas de trabalho do Azure. Isso inclui detalhar como criar uma arquitetura virtual para proteger recursos quando não é possível aplicar os mesmos controles físicos e de rede tradicionais que são possíveis em um ambiente local.

### <a name="guidance"></a>Orientação

* Limitar o número de pontos de saída para redes virtuais
* Substituir a rota padrão do sistema para todas as sub-redes que não precisam de comunicação de saída direta com a Internet
* Projete e implemente uma arquitetura de rede completa para identificar e controlar todos os pontos de entrada e saída para os recursos do Azure
* Considere utilising um design de rede hub e spoke para redes virtuais, conforme discutido na documentação do Microsoft Virtual Data Centre (VDC)
* Produtos Utilise com recursos de segurança embutidos para conexões de saída para a Internet (por exemplo, firewall do Azure, dispositivos virtuais de rede ou proxies da Web)
* Use controles de identidade como acesso baseado em função, acesso condicional e autenticação multifator (MFA) para limitar os privilégios de configuração de rede
* Implementar bloqueios para impedir a modificação ou exclusão de elementos principais da configuração de rede
* Implantar PaaS em uma configuração integrada de VNet para aumentar a diferenciação e o controle
* Implementar o ExpressRoute para conectividade com redes locais
* Implementar VPNs para integração com redes externas
* Utilise Azure Policy restringir as regiões e os recursos somente aos que são necessários para a funcionalidade do sistema
* Utilise Azure Policy impor a configuração de segurança de linha de base para recursos
* Aproveitar o observador de rede e Azure Monitor para registro em log, auditoria e visibilidade do tráfego de rede no Azure

### <a name="resources"></a>Recursos

Item | Associar
-----------| ---------
_Documentos normativos da Austrália e conformidade da política, incluindo diretrizes do consumidor_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Data Center Virtual do Azure_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)
_Segmentação de rede ACSC_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_Segurança de nuvem ACSC para locatários_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_Manual de segurança de informações do ACSC_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>Diretrizes de componente

Esta seção fornece diretrizes adicionais sobre os componentes individuais que são relevantes para o tráfego de saída para sistemas implantados no Azure. Cada seção descreve a intenção do componente específico com links para documentação e guias de configuração que podem ser usados para auxiliar no design e na criação de atividades.

### <a name="systems-security"></a>Segurança de sistemas

Toda a comunicação com os recursos no Azure passa pela infraestrutura de rede mantida pela Microsoft, que fornece conectividade e funcionalidade de segurança. Uma variedade de proteções são automaticamente colocadas em vigor pela Microsoft para proteger a infraestrutura de rede e a plataforma do Azure e recursos adicionais estão disponíveis como serviços no Azure para controlar o tráfego de rede e estabelecer a segmentação de rede e separação.

### <a name="virtual-network-vnet"></a>Rede Virtual (VNet)

As redes virtuais são um dos blocos de construção fundamentais para a rede no Azure. As redes virtuais definem um espaço de endereço IP e o limite de roteamento a serem usados em uma variedade de sistemas. As redes virtuais são divididas em sub-redes e todas as sub-redes em uma rede virtual têm uma rota de rede direta entre si. Usando gateways de rede virtual (ExpressRoute ou VPN), os sistemas em uma rede virtual podem ser integrados a ambientes locais e externos e por meio da NAT (conversão de endereços de rede) fornecida pelo Azure e a alocação de endereço IP público, os sistemas podem Conecte-se à Internet ou a outras regiões e serviços do Azure. Compreender as redes virtuais e os parâmetros de configuração associados e o roteamento é crucial na compreensão e no controle do tráfego de rede de saída.

Como as redes virtuais formam o espaço de endereço base e o limite de roteamento no Azure, ele pode ser usado como um bloco de construção principal de segmentação e separação de rede.

| Resource | Associar |
| --- | --- |
| *Visão geral das redes virtuais* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) |
| *Guia de instruções do plano de redes virtuais*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) |
| *Criar um início rápido de rede virtual* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
|

### <a name="subnet"></a>Subnet

As sub-redes são um componente crucial para segmentação de rede e diferenciação no Azure. As sub-redes podem ser usadas para fornecer separação entre sistemas. Uma sub-rede é o recurso em uma rede virtual em que NSGs, tabelas de rotas e pontos de extremidade de serviço são aplicados. As sub-redes podem ser usadas como endereços de origem e de destino para regras de firewall e listas de controle de acesso.

As sub-redes em uma rede virtual devem ser planejadas para atender aos requisitos de cargas de trabalho e sistemas. Os indivíduos envolvidos no design ou na implementação de sub-redes devem consultar as diretrizes de ACSC para segmentação de rede para determinar como os sistemas devem ser agrupados em uma sub-rede.

|Resource|Associar|
|---|---|
|*Adicionar, alterar ou excluir uma sub-rede de rede virtual* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
|

### <a name="network-interface"></a>Interface de rede

As interfaces de rede são a origem de todo o tráfego de saída de uma máquina virtual. As interfaces de rede permitem a configuração do endereçamento IP e podem ser usadas para aplicar NSGs ou roteamento de tráfego por meio de um NVA. As interfaces de rede para máquinas virtuais devem ser planejadas e configuradas adequadamente para alinhar com os objetivos gerais de segmentação de rede e de diferenciação.

|Resource|Associar|
|---|---|
|*Criar, alterar ou excluir uma interface de rede* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface) |
|*Endereçamento IP da interface de rede*               | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
|

### <a name="vnet-integrated-paas"></a>PaaS integrado de VNet

A PaaS pode fornecer funcionalidade e disponibilidade aprimoradas e reduzir a sobrecarga de gerenciamento, mas deve ser protegida adequadamente. Para aumentar o controle, impor a segmentação de rede ou fornecer um ponto de saída seguro para aplicativos e serviços, muitos recursos de PaaS podem ser integrados a uma rede virtual.

Usando o PaaS como uma parte integrada da arquitetura do sistema ou do aplicativo, a Microsoft fornece vários mecanismos para implantar a PaaS em uma rede virtual. A metodologia de implantação pode ajudar a restringir o acesso enquanto fornece conectividade e integração com sistemas e aplicativos internos. Os exemplos incluem ambientes de serviço de aplicativo, instâncias gerenciadas do SQL e muito mais.

Ao implantar a PaaS em uma rede virtual em que os controles de roteamento e NSG foram implementados, é crucial entender os requisitos de comunicação específicos do recurso, incluindo o acesso de gerenciamento dos serviços da Microsoft e o caminho que o tráfego de comunicações será levado ao responder a solicitações de entrada desses serviços.

| Resource  | Associar  |
| --- | --- |
| *Integração de rede virtual para serviços do Azure* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services) |
| *Integre seu aplicativo com um guia de instruções da rede virtual do Azure* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
|

### <a name="public-ip"></a>IP Público

Os endereços IP públicos são usados ao se comunicar fora de uma rede virtual. Isso inclui recursos de PaaS e quaisquer rotas com um próximo salto da Internet. As entidades da Comunidade devem planejar a alocação de endereços IP públicos com cuidado e atribuí-los a recursos em que há um requisito original. Como uma prática de design geral, os endereços IP públicos devem ser alocados a pontos de saída controlados para a rede virtual, como o Firewall do Azure, o gateway de VPN ou dispositivos de virtualização de rede.

|Resource|Associar|
|---|---|
|*Visão geral de Endereços IP Públicos*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) |
|*Criar, alterar ou excluir um endereço IP público* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
|

## <a name="effective-routes"></a>Rotas efetivas

As rotas efetivas são o conjunto resultante de rotas determinado pela combinação de rotas do sistema, pontos de extremidade de serviço, tabelas de rotas e BGP e o aplicativo da lógica de roteamento do Azure. Quando o tráfego de saída é enviado a partir de uma sub-rede, o Azure seleciona uma rota com base no endereço IP de destino, através da utilização do algoritmo de correspondência de prefixo mais longo. Se várias rotas tiverem o mesmo prefixo de endereço, o Azure seleciona o tipo de rota com base na prioridade seguinte:

1. Rota definida pelo utilizador
2. Rota BGP
3. Rota de sistema

É importante observar que as rotas do sistema para o tráfego relacionado à rede virtual, aos emparelhamentos de rede virtual ou aos pontos de extremidade de serviço de rede virtual são rotas preferenciais, mesmo que as rotas BGP sejam mais específicas.

Os indivíduos envolvidos no design ou na implementação de topologias de roteamento no Azure devem entender como o Azure roteia o tráfego e desenvolver uma arquitetura que equilibre a funcionalidade necessária dos sistemas com a segurança e a visibilidade necessárias. Deve-se tomar cuidado para planejar o ambiente adequadamente para evitar intervenções e exceções excessivas para rotear comportamentos, pois isso aumentará a complexidade e poderá tornar a solução de problemas e a detecção de falhas mais difíceis e demoradas.

| Resource | Associar  |
| --- | --- |
| *Exibir rotas efetivas* | <https://docs.microsoft.com/azure/virtual-network/manage-route-table#view-effective-routes> |
|

### <a name="system-routes"></a>Rotas de sistema

Para [rotas do sistema](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#system-routes), os indivíduos envolvidos no design ou na implementação de redes virtuais devem entender as rotas padrão do sistema e as opções disponíveis para complementar ou substituir essas rotas.

### <a name="service-endpoints"></a>Pontos finais de serviço

Habilitar [pontos de extremidade de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) em uma sub-rede fornece um caminho de comunicação direta para o recurso de PaaS associado. Isso pode fornecer maior desempenho e segurança, restringindo o caminho de comunicação disponível apenas para esse serviço. O uso de pontos de extremidade de serviço introduz um caminho de vazamento de dados potencial, pois a configuração padrão permite o acesso a todas as instâncias do serviço PaaS em vez das instâncias específicas necessárias para um aplicativo ou sistema.

As entidades da Comunidade devem avaliar o risco associado ao fornecimento de acesso direto ao recurso PaaS, incluindo a probabilidade e a conseqüência do caminho que está sendo usado indiretamente.

Para reduzir os riscos potenciais associados aos pontos de extremidade de serviço, implemente as políticas de ponto final de serviço sempre que possível ou considere habilitar pontos de extremidades de serviço em um firewall do Azure ou sub-rede NVA e rotear o tráfego de sub-redes específicas por meio de ti, onde adicional a filtragem, o monitoramento ou a inspeção podem ser aplicados.

|Resource|Associar|
|---|---|
|*Tutorial: Restringir o acesso à rede para recursos de PaaS com pontos de extremidade de serviço de rede virtual usando o portal do Azure* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

### <a name="route-tables"></a>Tabelas de rotas

As tabelas de rotas fornecem um mecanismo configurado pelo administrador para controlar o tráfego de rede no Azure. As tabelas de rotas podem ser utilised para encaminhar o tráfego para um firewall do Azure ou NVA, conectar-se diretamente a recursos externos ou substituir rotas do sistema do Azure. As tabelas de rotas também podem ser usadas para impedir que as redes aprendidas por meio de um gateway de rede virtual sejam disponibilizadas para recursos em uma sub-rede desabilitando a propagação de rota do gateway de rede virtual.

|Resource|Associar|
|---|---|
|*Conceitos de roteamento-rotas personalizadas* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)|
|*Tutorial: Rotear tráfego de rede* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)|
|

### <a name="border-gateway-protocol-bgp"></a>Border Gateway Protocol (BGP)

O BGP pode ser utiliseddo por gateways de rede virtual para trocar dinamicamente as informações de roteamento com outras redes externas ou locais. O BGP aplica-se a uma rede virtual quando configurado por meio de um gateway de rede virtual do ExpressRoute sobre o emparelhamento privado do ExpressRoute e quando habilitado em um gateway de VPN do Azure.

Os indivíduos envolvidos no design ou na implementação de redes virtuais e gateways de rede virtual no Azure devem levar algum tempo para entender o comportamento e as opções de configuração disponíveis para BGP no Azure.

|Resource|Associar|
|---|---|
|*Conceitos de roteamento: BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools)|
|*Requisitos de roteamento do ExpressRoute* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](https://docs.microsoft.com/azure/expressroute/expressroute-routing)|
|*Sobre o BGP com o gateway de VPN do Azure* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)|
|*Tutorial: Configurar uma VPN site a site em um emparelhamento da Microsoft de ExpressRoute* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering)|
|

## <a name="next-hop-types"></a>Tipos do próximo salto

### <a name="virtual-network"></a>Rede virtual

As rotas com um próximo salto da rede virtual são adicionadas automaticamente como rotas do sistema, mas também podem ser adicionadas a rotas definidas pelo usuário para direcionar o tráfego de volta para a rede virtual em instâncias em que a rota do sistema foi substituída.

### <a name="vnet-peering"></a>VNet peering

O emparelhamento VNet permite a comunicação entre duas redes virtuais diferentes. A configuração do emparelhamento VNet deve ser habilitada em cada rede virtual, mas as redes virtuais não precisam estar na mesma região, assinatura ou associadas ao mesmo locatário do Azure Active Directory (AD do Azure).

Ao configurar o emparelhamento VNet, é essencial que os indivíduos envolvidos no design ou na implementação do emparelhamento VNet compreendam os quatro parâmetros de configuração associados e como eles se aplicam a cada lado do par:

1. **Permitir acesso à rede virtual:** Selecione **habilitado** (padrão) para habilitar a comunicação entre as duas redes virtuais. Habilitar a comunicação entre redes virtuais permite que os recursos conectados a uma rede virtual se comuniquem entre si com a mesma largura de banda e latência como se estivessem conectados à mesma rede virtual.
2. **Permitir tráfego encaminhado:** Marque esta caixa para permitir o tráfego encaminhado por um tráfego de rede que não originou da rede virtual – para fluir para essa rede virtual por meio de um emparelhamento. Essa configuração é fundamental para implementar uma topologia de rede hub e spoke.
3. **Permitir trânsito de gateway:** Marque esta caixa para permitir que a rede virtual emparelhada Utilise o gateway de rede virtual conectado a essa rede virtual. *Permitir trânsito de gateway* está habilitado na rede virtual com o recurso de gateway de rede virtual, mas só se aplica se *usar gateways remotos* estiver habilitado na outra rede virtual.
4. **Usar gateways remotos:** Marque esta caixa para permitir que o tráfego dessa rede virtual flua por meio de um gateway de rede virtual conectado à rede virtual que está emparelhada com o. O *uso* de gateways remotos é habilitado na rede virtual sem um gateway de rede virtual e só se aplica se a opção *Permitir trânsito de gateway* estiver habilitada na outra rede virtual.

|Resource|Associar|
|---|---|
| Conceitos: Peering de rede virtual                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) |
| Criar, alterar ou excluir um emparelhamento de rede virtual | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="virtual-network-gateway"></a>Gateway de rede virtual

Os gateways de rede virtual fornecem um mecanismo para a integração de redes virtuais com redes externas, como ambientes locais, ambientes de parceiros e outras implantações em nuvem. Os dois tipos de gateway de rede virtual são ExpressRoute e VPN.

#### <a name="expressroute-gateway"></a>Gateway de ExpressRoute

Os gateways de ExpressRoute fornecem um ponto de saída da rede virtual para um ambiente local e devem ser implantados para atender aos requisitos de segurança, disponibilidade, finanças e desempenho. Gateways de ExpressRoute fornecem uma largura de banda de rede definida e incorrem em custos de uso após a implantação. As redes virtuais podem ter apenas um gateway de ExpressRoute, mas isso pode ser conectado a vários circuitos de ExpressRoute e pode ser aproveitado por várias redes virtuais por meio de emparelhamento de VNet, permitindo que a largura de banda e a conectividade sejam compartilhadas. Deve-se ter cuidado para configurar o roteamento entre ambientes locais e redes virtuais usando gateways de ExpressRoute para garantir a conectividade de ponta a ponta usando pontos de saída de rede controlados conhecidos. As entidades da Comunidade que usam o gateway do ExpressRoute sobre o emparelhamento privado do ExpressRoute também devem implantar NVA (soluções de virtualização de rede) para estabelecer conectividade VPN com o ambiente local para conformidade com as diretrizes de consumidor do ACSC.

É importante observar que os gateways de ExpressRoute têm restrições sobre os intervalos de endereços, as comunidades e outros itens de configuração específicos trocados por meio de BGP.

| Resource  | Associar  |
|---|---|
| Visão geral do gateway de ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) |
| Configurar um gateway de rede virtual para o ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)
|

#### <a name="vpn-gateway"></a>Gateway de VPN

O gateway de VPN do Azure fornece um ponto de rede de saída da rede virtual para uma rede externa para conectividade segura de site a site. Gateways de VPN fornecem uma largura de banda de rede definida e incorrem em custos de uso após a implantação. As entidades da Comunidade utilising gateway de VPN devem garantir que ela esteja configurada de acordo com as diretrizes de consumidor do ACSC. As redes virtuais podem ter apenas um gateway de VPN, mas isso pode ser configurado com vários túneis e pode ser aproveitado por várias redes virtuais por meio de emparelhamento de VNet, permitindo que várias redes virtuais compartilhem largura de banda e conectividade. Os gateways de VPN podem ser estabelecidos pela Internet ou via ExpressRoute por meio do emparelhamento da Microsoft.

| Resource  | Associar |
| --- | --- |
| Visão geral do gateway de VPN| [https://docs.microsoft.com/azure/vpn-gateway](https://docs.microsoft.com/azure/vpn-gateway)|
| Planear e conceber para o Gateway de VPN | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
| Gateway de VPN do Azure na Austrália do Azure | [Gateway de VPN do Azure na Austrália do Azure](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>Próximo salto da solução de virtualização

O próximo salto da solução de virtualização fornece a capacidade de processar o tráfego de rede fora da rede do Azure e da topologia de roteamento aplicada às redes virtuais. Os dispositivos virtuais podem aplicar regras de segurança, converter endereços, estabelecer VPNs, tráfego de proxy ou uma variedade de outros recursos. O próximo salto da solução de virtualização é aplicado por meio de UDRs em uma tabela de rotas e pode ser usado para direcionar o tráfego para um firewall do Azure, NVA individual ou Azure Load Balancer fornecer disponibilidade em vários NVAs. Para usar um dispositivo virtual para roteamento, as interfaces de rede associadas devem ser habilitadas para o encaminhamento de IP.

| Resource  | Associar |
| --- | ---|
| Conceitos de roteamento: Rotas personalizadas | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) |
| Habilitar ou desabilitar o encaminhamento de IP | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>Próximo salto de VirtualNetworkServiceEndpoint

As rotas com um tipo de próximo salto de VirtualNetworkServiceEndpoint são adicionadas somente quando um ponto de extremidade de serviço é configurado em uma sub-rede e não podem ser manualmente configuradas por meio de tabelas de rota.

### <a name="next-hop-of-internet"></a>Próximo salto da Internet

A Internet do próximo salto é usada para acessar os recursos que usam um endereço IP público, que inclui a Internet, bem como os serviços PaaS e Azure nas regiões do Azure. O próximo salto da Internet não requer uma rota padrão (0.0.0.0/0) cobrindo todas as redes, mas pode ser usado para habilitar caminhos de roteamento para serviços públicos específicos. O próximo salto da Internet deve ser usado para adicionar rotas a serviços autorizados e funcionalidades necessárias para a funcionalidade do sistema, como endereços de gerenciamento da Microsoft.

Exemplos de serviços que podem ser adicionados usando o próximo salto da Internet são:

1. Serviços de gerenciamento de chaves para ativação do Windows
2. Gerenciamento de Ambiente do Serviço de Aplicativo

|Resource|Associar|
|---|---|
| Conexões de saída no Azure | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) |
| Usar rotas personalizadas do Azure para habilitar a ativação do KMS | [https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) |
| Bloqueando um Ambiente do Serviço de Aplicativo  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](https://docs.microsoft.com/azure/app-service/environment/firewall-integration) |
|

### <a name="next-hop-of-none"></a>Próximo salto de nenhum

O próximo salto de nenhum pode ser usado para impedir a comunicação com uma rede específica. Em contraste com um NSG, que controla se o tráfego é permitido ou negado de atravessar um caminho de rede disponível, usar um próximo salto de nenhum remove completamente o caminho de rede. Deve-se tomar cuidado ao criar rotas com um próximo salto de nenhum, especialmente ao aplicá-la a uma rota padrão de 0.0.0.0/0, pois isso pode ter consequências indesejadas e pode dificultar a solução de problemas do sistema.

## <a name="security"></a>Segurança

A implementação de segmentação de rede e controles de diferenciação em recursos de IaaS e PaaS é obtida por meio da proteção dos próprios recursos e da implementação de caminhos de comunicação controlados a partir dos sistemas que se comunicarão com o recursos.

Projetar e criar soluções no Azure é um processo de criação de uma arquitetura lógica para entender, controlar e monitorar recursos de rede em toda a presença do Azure. Essa arquitetura lógica é um software definido na plataforma do Azure e assume o lugar de uma topologia de rede física implementada em ambientes de rede tradicionais.

A arquitetura lógica que é criada deve fornecer a funcionalidade necessária para a usabilidade, mas também deve fornecer a visibilidade e o controle necessários para segurança e integridade.

Atingir esse resultado se baseia na implementação das ferramentas de segmentação de rede e segregação necessárias, mas também na proteção e na imposição da topologia de rede e da implementação dessas ferramentas.

### <a name="network-security-groups-nsgs"></a>NSGs (grupos de segurança de rede)

NSGs são usados para especificar o tráfego de entrada e de saída permitido para uma sub-rede ou uma interface de rede específica. Ao configurar o NSGs, as entidades da Comunidade devem usar uma abordagem de lista de permissões em que as regras são configuradas para permitir o tráfego necessário com uma regra padrão configurada para negar todo o tráfego que não corresponde a uma instrução de permissão específica. Ao planejar e configurar o NSGs, é necessário ter cuidado para garantir que todo o tráfego de entrada e saída necessário seja capturado adequadamente. Isso inclui a identificação e a compreensão de todos os intervalos de endereços IP privados utilised em redes virtuais e no ambiente local, além de serviços específicos da Microsoft, como requisitos de gerenciamento de Azure Load Balancer e PaaS. Indivíduos envolvidos no design e na implementação do NSGs também devem entender o uso de marcas de serviço e grupos de segurança de aplicativo para criar regras de segurança refinadas, de serviço e específicas do aplicativo.

É importante observar que a configuração padrão de um NSG permite o tráfego de saída para todos os endereços na rede virtual e todos os endereços IP públicos.

|Resource|Associar|
|---|---|
|Visão geral de segurança de rede | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Criar, alterar ou excluir um grupo de segurança de rede | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

### <a name="azure-firewall"></a>Azure Firewall

O Firewall do Azure pode ser utilised para criar uma topologia de rede de Hub e spoke e impor controles de segurança de rede centralizados. O Firewall do Azure pode ser usado para atender aos requisitos necessários do ISM para o tráfego de saída, implementando uma abordagem de listagem de permissão em que somente os endereços IP, protocolos, portas e FQDNs necessários para a funcionalidade do sistema são autorizados. As entidades da Comunidade devem usar uma abordagem baseada em risco para determinar se os recursos de segurança fornecidos pelo firewall do Azure são suficientes para seus requisitos. Para cenários em que são necessários recursos de segurança adicionais além daqueles fornecidos pelo firewall do Azure, as entidades da Comunidade devem considerar a implementação de NVAs.

|Resource|Associar|
|---|---|
|*Documentação do firewall do Azure* | [https://docs.microsoft.com/azure/firewall](https://docs.microsoft.com/azure/firewall)|
|*Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando Azure PowerShell* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps)|
|

### <a name="network-virtual-appliances-nvas"></a>NVAs (soluções de virtualização de rede)

O NVAs pode ser usado para criar uma topologia de rede de Hub e spoke, fornecer recursos de rede avançados ou complementares ou pode ser usado como uma alternativa para mecanismos de rede do Azure para familiaridade e suporte e gerenciamento consistentes com serviços de rede locais. O NVAs pode ser implantado para atender a requisitos de segurança específicos, como; cenários em que há um requisito de reconhecimento de identidade associado ao tráfego de rede, descriptografia de HTTPS, inspeção de conteúdo, filtragem ou outros recursos de segurança. O NVAs deve ser implantado em uma configuração altamente disponível e as pessoas envolvidas no design ou na implementação do NVAs devem consultar a documentação apropriada do fornecedor para obter diretrizes sobre a implantação no Azure.

|Resource|Associar|
|---|---|
|*Implantar soluções de virtualização de rede altamente disponíveis* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>Políticas de ponto de extremidade de serviço (versão prévia)

Configure as políticas de ponto de extremidade de serviço com base na disponibilidade do serviço e uma avaliação de risco de segurança da probabilidade e do impacto do vazamento de dados. As políticas de ponto de extremidade de serviço devem ser consideradas para o armazenamento do Azure e gerenciadas caso a caso para outros serviços com base no perfil de risco associado.

| Resource | Associar  |
| --- | --- |
| *Visão geral das políticas de ponto de extremidade* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview) |
| *Criar, alterar ou excluir a política de ponto de extremidade de serviço usando o portal do Azure* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal)
|

### <a name="azure-policy"></a>Política do Azure

Azure Policy é um componente fundamental para impor e manter a integridade da arquitetura lógica do ambiente do Azure. Há uma variedade de serviços e caminhos de tráfego de rede de saída disponíveis por meio dos serviços do Azure. É crucial que as entidades da Comunidade estejam cientes dos recursos que existem em seu ambiente e dos pontos de saída de rede disponíveis. Para garantir que os pontos de saída de rede não autorizados não sejam criados no ambiente do Azure, as entidades da Comunidade devem usar Azure Policy para controlar os tipos de recursos que podem ser implantados e a configuração desses recursos. Exemplos práticos incluem a restrição de recursos somente para os autorizados e aprovados para uso e a exigência de NSGs para serem adicionados às sub-redes.

|Resource | Associar|
|---|---|
|*Visão geral de Azure Policy* | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|*Política de exemplo dos tipos de recursos permitidos* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)|
|*Forçar NSG em uma política de exemplo de sub-rede*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="paas-egress-capabilities"></a>Recursos de egresso de PaaS

Os recursos de PaaS fornecem oportunidades de maior funcionalidade e gerenciamento simplificado, mas introduzem complexidades no endereçamento de requisitos para segmentação e diferenciação de rede. Os recursos de PaaS normalmente são configurados com endereços IP públicos e podem ser acessados pela Internet.  Se você estiver usando recursos de PaaS em seus sistemas e soluções, deve-se tomar cuidado para identificar os fluxos de comunicação entre os componentes e criar regras de segurança de rede para permitir apenas essa comunicação. Como parte de uma abordagem de defesa aprofundada à segurança, os recursos de PaaS devem ser configurados com criptografia, autenticação e permissões e controles de acesso apropriados.  

### <a name="public-ip-for-paas"></a>IP público para PaaS

Os endereços IP públicos para recursos de PaaS são alocados com base na região em que o serviço é hospedado ou implantado. Uma compreensão das regiões e alocação de endereço IP público será necessária se você pretende criar regras de segurança de rede e a topologia de roteamento apropriadas para segmentação de rede e diferenciação que abrangem redes virtuais do Azure, PaaS e ExpressRoute e Conectividade com a Internet. O Azure aloca endereços IP de um pool alocado para cada região do Azure. A Microsoft torna os endereços usados em cada região disponíveis para download, que são atualizados de maneira regular e controlada. Os serviços que estão disponíveis em cada região também mudam frequentemente à medida que novos serviços são liberados ou os serviços são implantados mais amplamente. As entidades da Comunidade devem revisar esses materiais regularmente e podem usar a automação para manter os sistemas conforme necessário. Endereços IP específicos para alguns serviços hospedados em cada região podem ser obtidos entrando em contato com o suporte da Microsoft.

| Resource | Associar |
| --- | --- |
| *Intervalos IP do Datacenter do Microsoft Azure*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *Serviços do Azure por região*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Austrália Central, Austrália-Central-2, Austrália-leste, Austrália-sudeste & Products = All](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Endereços IP de entrada e saída no serviço Azure App* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips)
|

## <a name="next-steps"></a>Passos Seguintes

Compare sua arquitetura e o design gerais com as [plantas protegidas publicadas para aplicativos Web IaaS e PaaS](https://aka.ms/au-protected).
