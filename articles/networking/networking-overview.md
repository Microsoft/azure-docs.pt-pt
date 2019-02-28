---
title: Redes do Azure | Documentos da Microsoft
description: Saiba mais sobre as capacidades e serviços de rede do Azure.
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: 56e203a212a4e0e81922336f72fd58ef3cb7dc64
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983511"
---
# <a name="azure-networking"></a>Redes do Azure

O Azure fornece uma variedade de capacidades de rede que podem ser utilizados em conjunto ou separadamente. Clique em qualquer uma das principais capacidades seguintes para obter mais informações sobre os mesmos:
- [Conectividade entre os recursos do Azure](#connectivity): Ligar recursos do Azure em conjunto numa rede virtual segura e privada na cloud.
- [Conectividade com a Internet](#internet-connectivity): Comunica e para recursos do Azure através da Internet.
- [Conectividade no local](#on-premises-connectivity): Ligar uma rede no local para recursos do Azure através de uma rede privada virtual (VPN) através da Internet ou por meio de uma ligação dedicada para o Azure.
- [Direção do tráfego e de balanceamento de carga](#load-balancing): Balancear carga de tráfego para servidores na mesma localização e direcionar o tráfego para servidores em diferentes localizações.
- [Segurança](#security): Filtre o tráfego de rede entre sub-redes da rede ou máquinas virtuais individuais (VM).
- [Encaminhamento](#routing): Utilize o encaminhamento predefinido ou controlar totalmente o encaminhamento entre os recursos do Azure e no local.
- [Capacidade de gerenciamento](#manageability): Monitorizar e gerir os recursos de rede do Azure.
- [Ferramentas de implantação e configuração](#tools): Utilize um portal baseado na web ou ferramentas de linha de comandos para várias plataformas para implementar e configurar recursos de rede.

## <a name="Connectivity"></a>Conectividade entre os recursos do Azure

Recursos do Azure como máquinas virtuais, serviços Cloud, os conjuntos de dimensionamento de máquinas virtuais e ambientes de serviço de aplicações do Azure podem comunicar em privado entre si através de uma rede Virtual do Azure (VNet). Uma VNet é um isolamento lógico da cloud do Azure dedicada a sua [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Pode implementar várias VNets em cada subscrição do Azure e o Azure [região](https://azure.microsoft.com/regions). Cada VNet é isolada de outras VNets. Para cada VNet, pode:

- Especificar um espaço de endereços IP privado personalizado, utilizando endereços públicos e privados (RFC 1918). O Azure atribui recursos ligados à VNet um endereço IP privado do espaço de endereço que atribuir.
- Segmentar a VNet num ou mais sub-redes e atribuir uma parte do espaço de endereços da VNet, a cada sub-rede.
- Utilizar a resolução de nomes fornecida pelo Azure ou especificar o seu próprio servidor DNS para utilização por recursos ligados a uma VNet.

Para saber mais sobre o serviço de rede Virtual do Azure, leia os [descrição geral de rede Virtual](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo. Pode ligar VNets entre si, permitindo que os recursos ligados a das Vnets para comunicar entre si em VNets. Pode utilizar uma ou ambas das seguintes opções para ligar VNets entre si:

- **Peering:** Permite que os recursos ligados a VNets do Azure diferente na mesma região do Azure para comunicar entre si. A largura de banda e a latência entre as VNets é o mesmo como se os recursos foram ligados à mesma VNet. Para saber mais sobre o peering, veja a [descrição geral o peering de rede Virtual](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Gateway de VPN:** Permite que os recursos ligados a VNets do Azure diferentes em diferentes regiões do Azure para comunicar entre si. O tráfego entre VNets flui através de um Gateway de VPN do Azure. Largura de banda entre VNets está limitada à largura de banda do gateway. Para saber mais sobre como ligar VNets com um Gateway de VPN, veja a [configurar uma ligação de VNet a VNet entre regiões](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

## <a name="internet-connectivity"></a>Conectividade com a Internet

Por predefinição, todos os recursos do Azure ligados a uma VNet tem conectividade de saída à Internet. O endereço IP privado do recurso é a rede endereço de origem traduzido (SNAT) a um endereço IP público pela infraestrutura do Azure. Para saber mais sobre a conectividade de Internet de saída, veja a [compreender as ligações de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

Para comunicar internamente com recursos do Azure da Internet ou comunicações de saída à Internet sem o SNAT, um recurso tem de atribuir um endereço IP público. Para saber mais sobre endereços IP públicos, leia os [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

## <a name="on-premises-connectivity"></a>Conectividade no local

Pode aceder a recursos na sua VNet segura através de uma ligação VPN ou uma ligação privada direta. Para enviar tráfego de rede entre a rede virtual do Azure e a sua rede no local, tem de criar um gateway de rede virtual. Configurar definições para o gateway para criar o tipo de ligação que pretende, VPN ou ExpressRoute.

Pode ligar a sua rede no local a uma VNet com qualquer combinação das seguintes opções:

**Ponto a site (VPN sobre SSTP)**

A imagem seguinte mostra o ponto de separado para ligações de site entre vários computadores e uma VNet:

![Ponto a site](./media/networking-overview/point-to-site.png)

Esta ligação é estabelecida entre uma VNet e a um único computador. Este tipo de ligação é excelente se estiver a começar a utilizar o Azure ou para os programadores, uma vez que necessita de pouca ou nenhumas alterações à sua rede existente. Também é conveniente quando estiver a ligar a partir de uma localização remota, como uma conferência ou doméstica. As ligações ponto a site, muitas vezes, estão combinadas a uma ligação site a site através do mesmo gateway de rede virtual. A ligação utiliza o protocolo SSTP para fornecer comunicações encriptadas através da Internet entre o computador e a VNet. A latência de uma VPN ponto a site é imprevisível, uma vez que o tráfego atravessa a Internet.

**Site a site (túnel VPN IPsec/IKE)**

![Site a Site](./media/networking-overview/site-to-site.png)

Esta ligação é estabelecida entre o dispositivo VPN no local e um Gateway de VPN do Azure. Este tipo de ligação permite que qualquer recurso no local que está a autorizar a aceda à VNet. A ligação é uma VPN IPSec/IKE que fornece comunicações encriptadas através da Internet entre o dispositivo no local e o gateway de VPN do Azure. Pode ligar vários sites no local para o mesmo gateway VPN. O dispositivo VPN no local em cada site tem de ter um com acesso exterior endereço IP público que não estiver protegido por um NAT. A latência de uma ligação site a site é imprevisível, uma vez que o tráfego atravessa a Internet.

**ExpressRoute (ligação privada dedicada)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Este tipo de ligação é estabelecido entre a rede e o Azure, através de um parceiro do ExpressRoute. Esta ligação é privada. Tráfego atravessa a Internet. A latência de uma ligação do ExpressRoute é previsível, uma vez que o tráfego não atravessa a Internet. ExpressRoute pode ser combinado com uma ligação site a site.

Para saber mais sobre todas as opções de ligação anterior, leia os [diagramas de topologia de ligação](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

## <a name="load-balancing"></a>Direção do tráfego e de balanceamento de carga

O Microsoft Azure fornece vários serviços para gerir a forma como o tráfego de rede é distribuído e com balanceamento de carga. Pode usar qualquer uma das seguintes capacidades separadamente ou em conjunto:

**Balanceamento de carga de DNS**

O serviço de Gestor de tráfego do Azure fornece balanceamento de carga de global DNS. O Gestor de tráfego responde aos clientes com o endereço IP de um ponto final em bom estado, com base em um dos seguintes métodos de encaminhamento:
- **Geográfica:** Os clientes são direcionados para os pontos finais (Azure, externo ou aninhado) com base nas localizações geográficas em suas consultas DNS são originados. Este método permite cenários em que são importante saber a região geográfica de um cliente e o encaminhamento-los com base no mesmo. Os exemplos incluem o cumprimento mandatos de soberania de dados, a localização da experiência de utilizador e de conteúdo e medir o tráfego a partir de regiões diferentes.
- **Desempenho:** O endereço IP devolvido para o cliente é o "mais perto" para o cliente. O ponto final "mais próximo" não é necessariamente mais próximo, medido pela distância geográfica. Em vez disso, esse método determina o ponto final mais próximo ao medir a latência de rede. O Gestor de tráfego mantém uma tabela de latência de Internet para controlar o tempo de ida e volta entre intervalos de endereços IP e cada datacenter do Azure.
- **Prioridade:** O tráfego é direcionado para o ponto final (prioridade mais alta) primário. Se o ponto final primário não estiver disponível, o Gestor de tráfego encaminha o tráfego para o segundo ponto de extremidade. Se os pontos de extremidade primários e secundários não estiverem disponíveis, o tráfego passa para o terceiro e assim por diante. Disponibilidade do ponto de extremidade baseia-se sobre o estado configurado (ativada ou desativada) e a monitorização do ponto final em curso.
- **Round robin ponderado:** Para cada solicitação, o Gestor de tráfego escolhe aleatoriamente um ponto de extremidade disponível. A probabilidade de escolher um ponto de extremidade baseia-se os pesos atribuídos a todos os pontos de extremidade disponíveis. Em todos os pontos de extremidade resulta numa distribuição de tráfego até mesmo a utilizar a mesma importância. Usar os pesos superiores ou inferiores em pontos de extremidade específicos, faz com que esses pontos de extremidade seja retornado mais ou menos frequência nas respostas DNS.

A imagem seguinte mostra um pedido para um aplicativo web direcionado para um ponto de final de aplicação Web. Pontos finais também podem ser outros serviços do Azure, como VMs e serviços em nuvem.

![Gestor de Tráfego](./media/networking-overview/traffic-manager.png)

O cliente liga-se diretamente ao ponto de extremidade. O Gestor de tráfego do Azure Deteta quando um ponto final está danificado e, em seguida, redireciona os clientes para um ponto de extremidade diferente, bom estado de funcionamento. Para saber mais sobre o Gestor de tráfego, leia os [descrição geral do Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

**Balanceamento de carga de aplicação**

O serviço de Gateway de aplicação do Azure fornece o controlador de entrega de aplicações (ADC) como um serviço. Gateway de aplicação oferece vários grupos de recursos de balanceamento de carga de camada 7 (HTTP/HTTPS) para as suas aplicações, incluindo uma firewall de aplicações para proteger as suas aplicações web contra vulnerabilidades e exploits da web. Gateway de aplicação também lhe permite otimizar a produtividade do web farm ao descarregar a terminação de SSL intensiva da CPU para o gateway de aplicação. 

Outras capacidades de encaminhamento de camada 7 incluem a distribuição round robin de tráfego de entrada, afinidade por sessões com base no cookie, encaminhamento baseado no caminho URL e a capacidade de alojar vários Web sites atrás de um gateway de aplicação único. Gateway de aplicação pode ser configurado como um gateway de acesso à Internet, um gateway só interno ou uma combinação de ambos. Gateway de aplicação é totalmente do Azure gerida, dimensionável e de elevada disponibilidade. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão. Para saber mais sobre o Gateway de aplicação, veja a [descrição geral do Gateway de aplicação](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

A imagem seguinte mostra o URL de encaminhamento com o Gateway de aplicação com base no caminho:

![Gateway de Aplicação](./media/networking-overview/application-gateway.png)

**Balanceamento de carga de rede**

O Balanceador de carga do Azure fornece elevado desempenho e de baixa latência camada 4-balanceamento de carga para todos os protocolos UDP e TCP. Gere ligações de entrada e saídas. Pode configurar pontos finais com balanceamento de carga internos e públicos. Pode definir regras para mapear as conexões de entrada para destinos do conjunto de back-end com opções de pesquisa de estado de funcionamento TCP e HTTP para gerir a disponibilidade do serviço. Para saber mais sobre o Balanceador de carga, veja a [descrição geral do Balanceador de carga](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

A imagem seguinte mostra um aplicativo de várias camado de acesso à Internet que utiliza ambos os balanceadores de carga internos e externos:

![Load balancer](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Segurança

Pode filtrar o tráfego de e para recursos do Azure com as seguintes opções:

- **Rede:** Pode implementar grupos de segurança de rede do Azure (NSGs) para filtrar o tráfego de entrada e saído para recursos do Azure. Cada NSG contém uma ou mais regras de entrada e saídas. Cada regra especifica os endereços IP de origem, endereços IP de destino, porta e protocolo que o tráfego é filtrado com. Os NSGs podem ser aplicados para sub-redes individuais e VMs individuais. Para saber mais sobre NSGs, leia os [descrição geral dos grupos de segurança de rede](../virtual-network/security-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Aplicação:** Ao utilizar um Gateway de aplicação com firewall de aplicações web pode proteger os seus aplicativos web vulnerabilidades e exploits. Exemplos comuns são a ataques de injeção SQL, scripting entre sites e cabeçalhos mal formados. Gateway de aplicação filtra este tráfego e impede de acessar seus servidores web. É possível configurar as regras que desejar ativado. A capacidade de configurar políticas de negociação SSL é fornecida para permitir que determinadas políticas ser desativado. Para saber mais sobre o firewall de aplicações web, veja a [firewall de aplicações Web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

Se precisar de capacidade de rede Azure não fornecer ou pretende utilizar aplicações de rede utilizam no local, pode implementar os produtos em VMs e conectá-los a sua VNet. O [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) contém vários diferentes VMs pré-configuradas com aplicativos de rede podem utilizar atualmente. Estas VMs pré-configuradas, normalmente, são referidas como aplicações virtuais de rede (NVA). NVAs estão disponíveis com aplicativos como firewall e a otimização de WAN.

## <a name="routing"></a>Encaminhamento

O Azure cria padrão tabelas de rotas que permitem recursos ligados a qualquer sub-rede na VNet em qualquer comunicam entre si. Pode implementar um ou ambos dos seguintes tipos de rotas para substituir as rotas predefinidas que o Azure cria:
- **Definido pelo utilizador:** Pode criar tabelas de rotas personalizadas com rotas que controlam onde o tráfego é encaminhado para cada sub-rede. Para saber mais sobre as rotas definidas pelo utilizador, veja o artigo [Rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Protocolo BGP (BGP):** Se ligar a VNet à sua rede no local através de um Gateway de VPN do Azure ou a ligação do ExpressRoute, pode propagar rotas BGP para as suas VNets. O BGP é o protocolo de encaminhamento padrão utilizado normalmente na Internet para trocar informações de encaminhamento e acessibilidade entre duas ou mais redes. Quando utilizado no contexto de redes virtuais do Azure, o BGP permite que os Gateways de VPN do Azure e os dispositivos VPN no local, chamados elementos BGP ou vizinhos, troquem "rotas" que o informam de ambos os gateways da disponibilidade e acessibilidade para esses prefixos ir por meio dos gateways ou routers envolvidos. BGP também pode ativar o encaminhamento de tráfego entre várias redes ao propagar rotas que um gateway BGP aprende de um elemento de rede para todos os outros elementos BGP. Para saber mais sobre o BGP, consulte a [BGP com visão geral de Gateways de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

## <a name="manageability"></a>Capacidade de gestão

O Azure fornece as seguintes ferramentas para monitorizar e gerir redes:
- **Registos de atividades:** Todos os recursos do Azure têm registos de atividades que fornecem informações sobre operações sido efetuada, o estado das operações e quem iniciou a operação. Para saber mais sobre os registos de atividade, veja a [descrição geral de registos de atividades](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Registos de diagnóstico:** Eventos periódicos e espontânea são criados pelos recursos de rede e com sessão iniciados em contas de armazenamento do Azure, enviados para um Hub de eventos do Azure ou enviados para os registos do Azure Monitor. Os registos de diagnóstico fornecem informações aprofundadas sobre o estado de funcionamento de um recurso. Os registos de diagnóstico são fornecidos para o Balanceador de carga (para a Internet), grupos de segurança de rede, as rotas e o Gateway de aplicação. Para saber mais sobre os registos de diagnóstico, leia os [descrição geral de registos de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Métricas:** As métricas são medidas de desempenho e contadores coletados durante um período de tempo em recursos. Métricas podem ser utilizadas para acionar alertas com base nos limiares. Atualmente as métricas estão disponíveis no Gateway de aplicação. Para saber mais sobre métricas, veja a [descrição geral das métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Resolução de problemas:** Informações de resolução de problemas é acessível diretamente no portal do Azure. As informações de ajuda a diagnosticar problemas comuns com o ExpressRoute, Gateway de VPN, o Gateway de aplicação, registos de segurança de rede, rotas, DNS, Balanceador de carga e o Gestor de tráfego.
- **Controlo de acesso baseado em funções (RBAC):** Controlar quem pode criar e gerir recursos de rede com controlo de acesso baseado em funções (RBAC). Saiba mais sobre o RBAC com a leitura a [começar a utilizar o RBAC](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo. 
- **Captura de pacotes:** O serviço de observador de rede do Azure fornece a capacidade de executar uma captura de pacotes numa VM através de uma extensão, dentro da VM. Esta capacidade está disponível para Linux e VMs do Windows. Para saber mais sobre a captura de pacotes, leia os [descrição geral de captura de pacotes](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Verificar fluxos de IP:** Observador de rede permite-lhe verificar fluxos de IP entre uma VM do Azure e um recurso remoto para determinar se os pacotes são permitidos ou negados. Esta capacidade fornece aos administradores a capacidade de diagnosticar rapidamente problemas de conectividade. Para saber mais sobre como verificar fluxos de IP, leia os [descrição geral de verificação do fluxo de IP](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Resolver problemas de conectividade VPN:** A capacidade de resolução de problemas VPN do observador de rede fornece a capacidade de consultar uma ligação ou o gateway e certifique-se o estado de funcionamento dos recursos. Para saber mais sobre a resolução de problemas de ligações VPN, veja a [descrição geral de resolução de problemas de conectividade VPN](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Ver a topologia de rede:** Ver uma representação gráfica dos recursos de rede numa VNet com o observador de rede. Para saber mais sobre a topologia de rede de visualização, leia os [descrição geral da topologia](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.

## <a name="tools"></a>Ferramentas de implantação e configuração

Pode implementar e configurar recursos de rede do Azure com qualquer uma das seguintes ferramentas:

- **Portal do Azure:** Uma interface gráfica do usuário que é executado num navegador. Abra o [Portal do Azure](http://portal.azure.com).
- **Azure PowerShell:** Ferramentas de linha de comando para gerenciar o Azure a partir de computadores Windows. Saiba mais sobre o Azure PowerShell o lendo o [descrição geral do Azure PowerShell](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Interface de linha de comandos do Azure (CLI):** Ferramentas de linha de comando para gerenciar o Azure a partir de computadores Linux, macOS ou Windows. Saiba mais sobre a CLI do Azure com a leitura a [descrição geral da CLI do Azure](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- **Modelos do Azure Resource Manager:** Um ficheiro (no formato JSON) que define a infraestrutura e a configuração de uma solução do Azure. Ao utilizar um modelo, pode implementar repetidamente a solução durante o ciclo de vida da mesma e ter a confiança de que os recursos são implementados num estado consistente. Para saber mais sobre a criação de modelos, veja a [melhores práticas para criar modelos](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo. Modelos podem ser implementados com o portal do Azure, CLI ou PowerShell. Para começar a utilizar com os modelos imediatamente, implemente um dos muitos modelos previamente configurados no [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=network) biblioteca. 

## <a name="pricing"></a>Preços

Alguns dos serviços de redes do Azure têm um custo, enquanto outros são gratuitos. Ver os [rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network), [Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway), [Gateway de aplicação](https://azure.microsoft.com/pricing/details/application-gateway/), [Balanceador de carga](https://azure.microsoft.com/pricing/details/load-balancer), [oobservadorderede](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Gestor de tráfego](https://azure.microsoft.com/pricing/details/traffic-manager) e [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) páginas para obter mais informações de preços.

## <a name="next-steps"></a>Passos Seguintes

- Criar a sua primeira VNet e ligá-la, algumas VMs, concluindo os passos a [criar a primeira rede virtual](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- Ligar o computador a uma VNet, concluindo os passos a [configurar uma ligação ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
- Tráfego de Internet para servidores públicos de balanceamento de carga, concluindo os passos a [criar um balanceador de carga com acesso à Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) artigo.
