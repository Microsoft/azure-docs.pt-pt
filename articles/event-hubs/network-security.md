---
title: Segurança de rede para hubs de eventos Azure
description: Este artigo descreve como configurar o acesso a partir de pontos finais privados
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 02a3a3436c354f7a9c817298d0ce887e33d8016a
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318488"
---
# <a name="network-security-for-azure-event-hubs"></a>Segurança de rede para hubs de eventos Azure 
Este artigo descreve como usar as seguintes funcionalidades de segurança com os Azure Event Hubs: 

- Etiquetas de serviço
- Regras ip firewall
- Pontos finais de serviço de rede
- Pontos finais privados


## <a name="service-tags"></a>Etiquetas de serviço
Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. Para obter mais informações sobre etiquetas de serviço, consulte [a visão geral das etiquetas de serviço.](../virtual-network/service-tags-overview.md)

Pode utilizar tags de serviço para definir controlos de acesso à rede em [grupos de segurança](../virtual-network/network-security-groups-overview.md#security-rules)de rede   ou [Azure Firewall](../firewall/service-tags.md). Utilize etiquetas de serviço no lugar de endereços IP específicos quando criar regras de segurança. Ao especificar o nome da etiqueta de serviço (por exemplo, **EventHub)** no campo de *origem*ou destino adequado   de uma *destination*   regra, pode permitir ou negar o tráfego para o serviço correspondente.

| Etiqueta de serviço | Objetivo | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure Event Hubs. | Saída | Sim | Sim |


## <a name="ip-firewall"></a>Firewall de IP 
Por predefinição, os espaços de nomes do Event Hubs estão acessíveis a partir da Internet desde que o pedido venha com autenticação e autorização válidas. Com a firewall IP, pode restringi-lo ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Encaminhamento).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que os Azure Event Hubs deverão ser acessíveis apenas a partir de determinados sites conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar Os Centros de Eventos com [Rota Azure Express,](../expressroute/expressroute-faqs.md#supported-services)pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos endereços IP da infraestrutura no local. 

As regras de firewall IP são aplicadas ao nível do espaço de nomes do Event Hubs. Por isso, as regras aplicam-se a todas as ligações dos clientes utilizando qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra IP permitida no espaço de nomes Do Event Hubs é rejeitada como não autorizada. A resposta não menciona a regra do IP. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a ação de aceitação ou rejeição.

Para mais informações, consulte [Como configurar firewall IP para um centro de eventos](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Pontos finais de serviço de rede
A integração de Centros de Eventos com [Rede Virtual (VNet) Os pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço permitem o acesso seguro às capacidades de mensagens a partir de cargas de trabalho, como máquinas virtuais, que estão ligadas a redes virtuais, com o caminho de tráfego da rede a ser assegurado em ambas as extremidades.

Uma vez configurado para ficar ligado a pelo menos um ponto final de serviço de sub-rede de rede virtual, o respetivo espaço de nomes do Event Hubs já não aceita tráfego de qualquer lugar, mas sub-redes autorizadas em redes virtuais. Do ponto de vista da rede virtual, a ligação de um espaço de nomes de Event Hubs a um ponto final de serviço configura um túnel de rede isolado da sub-rede de rede virtual para o serviço de mensagens. 

O resultado é uma relação privada e isolada entre as cargas de trabalho ligadas à sub-rede e o respetivo espaço de nomes De Event Hubs, apesar do endereço de rede observável do ponto final do serviço de mensagens estar numa gama pública de IP. Há uma exceção a este comportamento. Ativar um ponto final de serviço, por padrão, permite a `denyall` regra na firewall [IP](event-hubs-ip-filtering.md) associada à rede virtual. Pode adicionar endereços IP específicos na firewall IP para permitir o acesso ao ponto final público do Event Hub. 

> [!IMPORTANT]
> As redes virtuais são suportadas em níveis **padrão** e **dedicados** de Centros de Eventos. Não é suportado no nível **básico.**

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração do VNet 

As soluções que requerem uma segurança apertada e compartimentada, e onde as sub-redes de rede virtuais fornecem a segmentação entre os serviços compartimentados, ainda precisam de vias de comunicação entre serviços residentes nesses compartimentos.

Qualquer via IP imediata entre os compartimentos, incluindo os que transportam HTTPS sobre TCP/IP, comporta o risco de exploração de vulnerabilidades a partir da camada de rede em cima. Os serviços de mensagens fornecem caminhos de comunicação isolados, onde as mensagens são mesmo escritas para o disco à medida que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão ambas ligadas à mesma instância Event Hubs podem comunicar de forma eficiente e fiável através de mensagens, enquanto a respetiva integridade da fronteira de isolamento da rede é preservada.
 
Isto significa que as suas soluções de nuvem sensíveis à segurança não só têm acesso a capacidades de mensagens assíncronas fiáveis e escaláveis da indústria Azure, como podem agora utilizar mensagens para criar caminhos de comunicação entre compartimentos de solução seguros que são inerentemente mais seguros do que o que é possível com qualquer modo de comunicação peer-to-peer, incluindo HTTPS e outros protocolos de tomadas seguros.

### <a name="bind-event-hubs-to-virtual-networks"></a>Ligar centros de eventos a redes virtuais

**As regras de rede virtual** são a funcionalidade de segurança de firewall que controla se o seu espaço de nomeS Azure Event Hubs aceita ligações a partir de uma determinada sub-rede de rede virtual.

Ligar um espaço de nomes de Event Hubs a uma rede virtual é um processo em duas etapas. Primeiro, precisa de criar um **ponto final de serviço de rede virtual** na sub-rede de uma rede virtual e de o ativar para o **Microsoft.EventHub,** como explicado no artigo de [visão geral](../virtual-network/virtual-network-service-endpoints-overview.md) do ponto de vista do serviço. Depois de ter adicionado o ponto final de serviço, liga-lhe o espaço de nomes Do Event Hubs com uma **regra de rede virtual**.

A regra da rede virtual é uma associação do espaço de nomes do Event Hubs com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho ligadas à sub-rede têm acesso ao espaço de nomes do Event Hubs. O Próprio Event Hubs nunca estabelece ligações de saída, não precisa de ter acesso e, portanto, nunca tem acesso à sua sub-rede, permitindo esta regra.

Para obter mais informações, consulte [Como configurar pontos finais de serviço de rede virtual para um centro de eventos](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Pontos finais privados

[O serviço Azure Private Link](../private-link/private-link-overview.md) permite-lhe aceder aos Serviços Azure (por exemplo, Azure Event Hubs, Azure Storage e Azure Cosmos DB) e a Azure acolheu serviços de cliente/parceiro sobre um **ponto final privado** na sua rede virtual.

Um ponto final privado é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acessos.

> [!NOTE]
> Esta funcionalidade é suportada apenas com o nível **dedicado.** Para obter mais informações sobre o nível dedicado, consulte [a visão geral dos Centros de Eventos Dedicados.](event-hubs-dedicated-overview.md) 

Para mais informações, consulte [Como configurar pontos finais privados para um centro de eventos](private-link-service.md)


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como configurar firewall IP para um centro de eventos](event-hubs-ip-filtering.md)
- [Como configurar pontos finais de serviço de rede virtual para um centro de eventos](event-hubs-service-endpoints.md)
- [Como configurar pontos finais privados para um centro de eventos](private-link-service.md)