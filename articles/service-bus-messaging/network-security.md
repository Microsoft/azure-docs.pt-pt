---
title: Segurança de rede para ônibus de serviço Azure
description: Este artigo descreve funcionalidades de segurança de rede tais como tags de serviço, regras de firewall IP, pontos finais de serviço e pontos finais privados.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: db0dd89d1f902699c27b724609505ba681757454
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92310469"
---
# <a name="network-security-for-azure-service-bus"></a>Segurança de rede para ônibus de serviço Azure 
Este artigo descreve como utilizar as seguintes funcionalidades de segurança com o Azure Service Bus: 

- Etiquetas de serviço
- Regras ip firewall
- Pontos finais de serviço de rede
- Pontos finais privados


## <a name="service-tags"></a>Etiquetas de serviço
Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. Para obter mais informações sobre etiquetas de serviço, consulte [a visão geral das etiquetas de serviço.](../virtual-network/service-tags-overview.md)

Pode utilizar tags de serviço para definir controlos de acesso à rede em [grupos de segurança](../virtual-network/network-security-groups-overview.md#security-rules) de rede ou [Azure Firewall](../firewall/service-tags.md). Utilize etiquetas de serviço no lugar de endereços IP específicos quando criar regras de segurança. Ao especificar o nome da etiqueta de serviço (por exemplo, **ServiceBus)** no campo de *origem* ou *destino* adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente.

| Etiqueta de serviço | Objetivo | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Tráfego de autocarros Azure Service que utiliza o nível de serviço Premium. | Saída | Yes | Yes |


> [!NOTE]
> Você pode usar tags de serviço apenas para espaços de nome **premium.** Se estiver a utilizar um espaço de nome **padrão,** utilize o endereço IP que vê quando executar o seguinte comando: `nslookup <host name for the namespace>` . Por exemplo: `nslookup contosons.servicebus.windows.net`. 

## <a name="ip-firewall"></a>Firewall de IP 
Por predefinição, os espaços de nome do Service Bus estão acessíveis a partir da Internet desde que o pedido venha com autenticação e autorização válidas. Com a firewall IP, pode restringi-lo ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Encaminhamento).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que o Azure Service Bus só deve ser acessível a partir de determinados sites conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar o Service Bus com [Rota Expresso Azure][rota expressa], pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos endereços IP da sua infraestrutura no local ou endereços de um gateway NAT corporativo. 

As regras de firewall IP são aplicadas ao nível do espaço de nomes do Service Bus. Por isso, as regras aplicam-se a todas as ligações dos clientes utilizando qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra IP permitida no espaço de nomes do Service Bus é rejeitada como não autorizada. A resposta não menciona a regra do IP. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a ação de aceitação ou rejeição.

Para obter mais informações, consulte [Como configurar firewall IP para um espaço de nomes de ônibus de serviço](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Pontos finais de serviço de rede
A integração do Service Bus com [os pontos finais de serviço da Rede Virtual (VNet)](service-bus-service-endpoints.md) permite o acesso seguro às capacidades de mensagens a partir de cargas de trabalho, como máquinas virtuais, que estão ligadas a redes virtuais, com o caminho de tráfego da rede a ser assegurado em ambas as extremidades.

Uma vez configurado para ser ligado a pelo menos um ponto final de serviço de sub-rede de rede virtual, o respetivo espaço de nome do Service Bus deixará de aceitar o tráfego de qualquer lugar, mas sim de redes virtuais autorizadas. Do ponto de vista da rede virtual, a ligação de um espaço de nomes de Service Bus a um ponto final de serviço configura um túnel de rede isolado da sub-rede de rede virtual para o serviço de mensagens.

O resultado é uma relação privada e isolada entre as cargas de trabalho ligadas à sub-rede e o respetivo espaço de nomes do Service Bus, apesar do endereço de rede observável do ponto final do serviço de mensagens estar numa gama pública de IP.

> [!IMPORTANT]
> As Redes Virtuais são suportadas apenas em espaços de nomes de serviços de serviço de [nível Premium.](service-bus-premium-messaging.md)
> 
> Ao utilizar pontos finais de serviço VNet com Service Bus, não deve ativar estes pontos finais em aplicações que misturam espaços de nomes standard e premium service bus. Porque o nível Standard não suporta VNets. O ponto final é restrito apenas aos espaços de nome de nível Premium.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração do VNet 

As soluções que requerem uma segurança apertada e compartimentada, e onde as sub-redes de rede virtuais fornecem a segmentação entre os serviços compartimentados, geralmente ainda precisam de vias de comunicação entre serviços residentes nesses compartimentos.

Qualquer via IP imediata entre os compartimentos, incluindo os que transportam HTTPS sobre TCP/IP, comporta o risco de exploração de vulnerabilidades a partir da camada de rede em cima. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, onde as mensagens são mesmo escritas para o disco à medida que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão ambas ligadas à mesma instância do Service Bus podem comunicar de forma eficiente e fiável através de mensagens, enquanto a respetiva integridade da fronteira de isolamento da rede é preservada.
 
Isto significa que as suas soluções de nuvem sensíveis à segurança não só têm acesso a capacidades de mensagens assíncronas fiáveis e escaláveis da indústria Azure, como podem agora utilizar mensagens para criar caminhos de comunicação entre compartimentos de solução seguros que são inerentemente mais seguros do que o que é possível com qualquer modo de comunicação peer-to-peer, incluindo HTTPS e outros protocolos de tomadas seguros.

### <a name="bind-service-bus-to-virtual-networks"></a>Bind Service Bus para redes virtuais

*As regras de rede virtual* são a funcionalidade de segurança de firewall que controla se o seu servidor Azure Service Bus aceita ligações a partir de uma determinada sub-rede de rede virtual.

Ligar um espaço de nome de Service Bus a uma rede virtual é um processo em duas etapas. Primeiro, tem de criar um **ponto final de serviço de Rede Virtual** numa sub-rede de Rede Virtual e capacitá-lo para o **Microsoft.ServiceBus,** conforme explicado na [visão geral](service-bus-service-endpoints.md)do ponto final do serviço . Depois de ter adicionado o ponto final do serviço, liga-lhe o espaço de nomes do Service Bus com uma **regra de rede virtual**.

A regra da rede virtual é uma associação do espaço de nomes do Service Bus com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho ligadas à sub-rede têm acesso ao espaço de nomes do Service Bus. A Service Bus nunca estabelece ligações de saída, não precisa de ter acesso e, portanto, nunca tem acesso à sua sub-rede, permitindo esta regra.

Para obter mais informações, consulte [Como configurar pontos finais de serviço de rede virtual para um espaço de nomes de Service Bus](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Pontos finais privados

O Azure Private Link Service permite-lhe aceder aos serviços Azure (por exemplo, Azure Service Bus, Azure Storage e Azure Cosmos DB) e a Azure acolheu serviços de cliente/parceiro sobre um **ponto final privado** na sua rede virtual.

Um ponto final privado é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acessos.

Para mais informações, consulte [o que é a Azure Private Link?](../private-link/private-link-overview.md)

> [!NOTE]
> Esta funcionalidade é suportada com o nível **premium** da Azure Service Bus. Para obter mais informações sobre o nível premium, consulte o artigo [de níveis de mensagens Service Bus Premium e Standard.](service-bus-premium-messaging.md)


Para obter mais informações, consulte [Como configurar pontos finais privados para um espaço de nomes de autocarros de serviço](private-link-service.md)


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como configurar firewall IP para um espaço de nomes de ônibus de serviço](service-bus-ip-filtering.md)
- [Como configurar pontos finais de serviço de rede virtual para um espaço de nomes de ônibus de serviço](service-bus-service-endpoints.md)
- [Como configurar pontos finais privados para um espaço de nomes de ônibus de serviço](private-link-service.md)