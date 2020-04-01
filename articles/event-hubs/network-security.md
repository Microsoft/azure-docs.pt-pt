---
title: Segurança de rede para Hubs de Eventos Azure
description: Este artigo descreve como configurar o acesso a partir de pontos finais privados
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 46e6a9ecc2ed09aed1076f12c1f61a966485bdad
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422774"
---
# <a name="network-security-for-azure-event-hubs"></a>Segurança de rede para Hubs de Eventos Azure 
Este artigo descreve como usar as seguintes funcionalidades de segurança com hubs de eventos Azure: 

- Etiquetas de serviço
- Regras ip Firewall
- Pontos finais do serviço de rede
- Pontos finais privados (pré-visualização)


## <a name="service-tags"></a>Etiquetas de serviço
Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. Para mais informações sobre etiquetas de serviço, consulte a visão geral das [etiquetas](../virtual-network/service-tags-overview.md)de serviço .

Pode utilizar etiquetas de serviço para definir controlos de acesso à rede em [grupos](../virtual-network/security-overview.md#security-rules) de segurança de rede ou [firewall Azure](../firewall/service-tags.md). Utilize etiquetas de serviço no lugar de endereços IP específicos quando criar regras de segurança. Especificando o nome da etiqueta de serviço (por exemplo, **EventHub)** no campo de *origem* ou *destino* apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente.

| Etiqueta de serviço | Objetivo | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com firewall Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Hubs de eventos Azure. | Saída | Sim | Sim |


## <a name="ip-firewall"></a>Firewall de IP 
Por predefinição, os espaços de nome do Event Hubs são acessíveis a partir da internet desde que o pedido venha com autenticação e autorização válidas. Com firewall IP, pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que os Hubs de Eventos Azure devem ser acessíveis apenas a partir de certos sites bem conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar o tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar os Hubs de Eventos com [a Rota Expresso Azure,](/azure/expressroute/expressroute-faqs#supported-services)pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos seus endereços IP de infraestrutura no local. 

As regras de firewall IP são aplicadas ao nível do espaço de nome do Event Hubs. Por isso, as regras aplicam-se a todas as ligações dos clientes que utilizam qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra ip permitida no espaço de nome do Event Hubs é rejeitada como não autorizada. A resposta não menciona a regra do PI. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a aceitação ou rejeita a ação.

Para mais informações, consulte [Como configurar firewall IP para um hub](event-hubs-ip-filtering.md) de eventos

## <a name="network-service-endpoints"></a>Pontos finais do serviço de rede
A integração de Hubs de Eventos com Pontos finais de Serviço de [Rede Virtual (VNet)](../virtual-network/virtual-network-service-endpoints-overview.md) permite o acesso seguro a capacidades de mensagens a partir de cargas de trabalho como máquinas virtuais que estão ligadas a redes virtuais, com a trajetória de tráfego da rede a ser protegida em ambas as extremidades.

Uma vez configurado para vincular-se a pelo menos um ponto final de serviço de rede virtual, o respetivo espaço de nome sem nome do Event Hubs já não aceita tráfego de qualquer lugar, mas autorizado subredes em redes virtuais. Do ponto de vista da rede virtual, vincular um espaço de nome do Event Hubs a um ponto final de serviço configura um túnel de rede isolado da subnet da rede virtual para o serviço de mensagens. 

O resultado é uma relação privada e isolada entre as cargas de trabalho ligadas à subnet e o respetivo espaço de nome sem nome do Event Hubs, apesar do endereço de rede observável do ponto final do serviço de mensagens estar numa gama de IP pública. Há uma exceção a este comportamento. Ativar um ponto final de serviço, `denyall` por defeito, permite a regra na [firewall IP](event-hubs-ip-filtering.md) associada à rede virtual. Pode adicionar endereços IP específicos na firewall IP para permitir o acesso ao ponto final do Event Hub. 

> [!IMPORTANT]
> As redes virtuais são suportadas em níveis **standard** e **dedicados** de Centros de Eventos. Não é suportado no nível **básico.**

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários de segurança avançados possibilitados pela integração vnet 

As soluções que requerem segurança apertada e compartimentada, e onde as redes virtuais fornecem a segmentação entre os serviços compartimentados, ainda precisam de caminhos de comunicação entre os serviços residentes nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo os que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades da camada de rede para cima. Os serviços de mensagens fornecem caminhos de comunicação isolados, onde as mensagens são até escritas em disco à medida que transitam entre as partes. As cargas de trabalho em duas redes virtuais distintas que estão ambas ligadas ao mesmo caso de Hubs de Eventos podem comunicar de forma eficiente e fiável através de mensagens, enquanto a respetiva integridade de fronteira de isolamento da rede é preservada.
 
Isto significa que as suas soluções de nuvem sensíveis à segurança não só têm acesso a capacidades de mensagens assíncronas fiáveis e escaláveis, como podem agora utilizar mensagens para criar caminhos de comunicação entre compartimentos de solução segura que são inerentemente mais seguros do que o que é possível com qualquer modo de comunicação entre pares, incluindo HTTPS e outros protocolos de tomada seletivos protegidos por TLS.

### <a name="bind-event-hubs-to-virtual-networks"></a>Ligar centros de eventos a redes virtuais

**As regras** de rede virtuais são a funcionalidade de segurança da firewall que controla se o seu espaço de nome sem nome do Azure Event Hubs aceita ligações a partir de uma determinada subnet de rede virtual.

Vincular um espaço de nome de Event Hubs a uma rede virtual é um processo em duas etapas. Primeiro é necessário criar um **ponto final** de serviço de rede virtual na subnet de uma rede virtual e capacitá-lo para o **Microsoft.EventHub,** conforme explicado no artigo de visão geral do ponto final do [serviço.](../virtual-network/virtual-network-service-endpoints-overview.md) Uma vez adicionado o ponto final do serviço, ligue o espaço de nome do Event Hubs a ele com uma regra de **rede virtual**.

A regra da rede virtual é uma associação do espaço de nome do Event Hubs com uma subnet de rede virtual. Enquanto a regra existe, todas as cargas de trabalho ligadas à subnet têm acesso ao espaço de nome do Event Hubs. O Próprio Event Hubs nunca estabelece ligações de saída, não precisa de ter acesso, pelo que nunca é concedido acesso à sua subnet, permitindo esta regra.

Para mais informações, consulte [Como configurar pontos finais](event-hubs-service-endpoints.md) do serviço de rede virtual para um hub de eventos

## <a name="private-endpoints"></a>Pontos finais privados

O [serviço Azure Private Link](../private-link/private-link-overview.md) permite-lhe aceder aos Serviços Azure (por exemplo, Azure Event Hubs, Azure Storage e Azure Cosmos DB) e o Azure acolheu serviços de clientes/parceiros sobre um ponto final **privado** na sua rede virtual.

Um ponto final privado é uma interface de rede que o conecta de forma privada e segura a um serviço alimentado por Azure Private Link. O ponto final privado utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a uma instância de um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acesso.

> [!NOTE]
> Esta funcionalidade é suportada apenas com o nível **dedicado.** Para mais informações sobre o nível dedicado, consulte [a visão geral dos Centros de Eventos Dedicados](event-hubs-dedicated-overview.md). 
>
> Esta funcionalidade encontra-se atualmente em **pré-visualização**. 


Para mais informações, consulte [Como configurar pontos finais privados para um hub](private-link-service.md) de eventos


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como configurar firewall IP para um hub de eventos](event-hubs-ip-filtering.md)
- [Como configurar pontos finais de serviço de rede virtual para um hub de eventos](event-hubs-service-endpoints.md)
- [Como configurar pontos finais privados para um hub de eventos](private-link-service.md)