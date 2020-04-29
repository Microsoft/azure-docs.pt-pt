---
title: Segurança da rede para ônibus de serviço Azure
description: Este artigo descreve funcionalidades de segurança da rede, tais como etiquetas de serviço, regras de firewall IP, pontos finais de serviço e pontos finais privados.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: aschhab
ms.openlocfilehash: 95f8c2a3b47b59bab7df909be43dacdb1f9c58f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479283"
---
# <a name="network-security-for-azure-service-bus"></a>Segurança da rede para ônibus de serviço Azure 
Este artigo descreve como usar as seguintes funcionalidades de segurança com o Azure Service Bus: 

- Etiquetas de serviço
- Regras ip Firewall
- Pontos finais do serviço de rede
- Pontos finais privados (pré-visualização)


## <a name="service-tags"></a>Etiquetas de serviço
Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. Para mais informações sobre etiquetas de serviço, consulte a visão geral das [etiquetas](../virtual-network/service-tags-overview.md)de serviço .

Pode utilizar etiquetas de serviço para definir controlos de acesso à rede em [grupos](../virtual-network/security-overview.md#security-rules) de segurança de rede ou [firewall Azure](../firewall/service-tags.md). Utilize etiquetas de serviço no lugar de endereços IP específicos quando criar regras de segurança. Especificando o nome da etiqueta de serviço (por exemplo, **ServiceBus)** no campo de *origem* ou *destino* apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente.

| Etiqueta de serviço | Objetivo | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com firewall Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Tráfego de ônibus de serviço Azure que utiliza o nível de serviço Premium. | Saída | Sim | Sim |


## <a name="ip-firewall"></a>Firewall de IP 
Por predefinição, os espaços de nome service Bus são acessíveis a partir da internet desde que o pedido venha com autenticação e autorização válidas. Com firewall IP, pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que o Azure Service Bus só deve estar acessível a partir de certos sites bem conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar o tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar o Service Bus com [Rota Expressa Azure][rota expressa], pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos seus endereços IP de infraestrutura no local ou endereços de um gateway NAT corporativo. 

As regras de firewall IP são aplicadas ao nível do espaço de nome do Ônibus de serviço. Por isso, as regras aplicam-se a todas as ligações dos clientes que utilizam qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra ip permitida no espaço de nome do Ônibus de serviço é rejeitada como não autorizada. A resposta não menciona a regra do PI. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a aceitação ou rejeita a ação.

Para mais informações, consulte [Como configurar firewall IP para um espaço de nome de ônibus de serviço](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Pontos finais do serviço de rede
A integração do Service Bus com [pontos finais de serviço da Rede Virtual (VNet)](service-bus-service-endpoints.md) permite o acesso seguro a capacidades de mensagens a partir de cargas de trabalho como máquinas virtuais que estão ligadas a redes virtuais, com a trajetória de tráfego da rede a ser protegida em ambas as extremidades.

Uma vez configurado para ser ligado a pelo menos um ponto final de serviço de rede virtual, o respetivo espaço de nome do Ônibus de Serviço deixará de aceitar o tráfego de qualquer lugar, mas sim rede virtual autorizada. Do ponto de vista da rede virtual, vincular um espaço de nome service Bus a um ponto final de serviço configura um túnel de rede isolado da subnet da rede virtual para o serviço de mensagens.

O resultado é uma relação privada e isolada entre as cargas de trabalho ligadas à subnet e o respetivo espaço de nome saque de serviço, apesar do endereço observável da rede do ponto final do serviço de mensagens estar numa gama de IP pública.

> [!IMPORTANT]
> As Redes Virtuais são suportadas apenas em espaços de nome de ônibus de [serviço de nível Premium.](service-bus-premium-messaging.md)
> 
> Ao utilizar pontos finais de serviço VNet com o Service Bus, não deve ativar estes pontos finais em aplicações que misturam espaços de nome sinuosos de ônibus de serviço standard e premium. Porque o nível Standard não suporta VNets. O ponto final é restrito apenas a espaços de nome de nível Premium.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários de segurança avançados possibilitados pela integração vnet 

Soluções que requerem segurança apertada e compartimentada, e onde as redes virtuais fornecem a segmentação entre os serviços compartimentados, geralmente ainda precisam de caminhos de comunicação entre os serviços residentes nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo os que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades da camada de rede para cima. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, onde as mensagens são até escritas em disco à medida que transitam entre as partes. As cargas de trabalho em duas redes virtuais distintas que estão ambas ligadas à mesma instância de Ônibus de serviço podem comunicar de forma eficiente e fiável através de mensagens, enquanto a respetiva integridade de fronteira de isolamento da rede é preservada.
 
Isto significa que as suas soluções de nuvem sensíveis à segurança não só têm acesso a capacidades de mensagens assíncronas fiáveis e escaláveis, como podem agora utilizar mensagens para criar caminhos de comunicação entre compartimentos de solução segura que são inerentemente mais seguros do que o que é possível com qualquer modo de comunicação entre pares, incluindo HTTPS e outros protocolos de tomada seletivos protegidos por TLS.

### <a name="bind-service-bus-to-virtual-networks"></a>Ônibus de serviço de ligação para redes virtuais

*As regras* de rede virtuais são a funcionalidade de segurança da firewall que controla se o seu servidor Deônibus de Serviço Azure aceita ligações a partir de uma determinada subnet de rede virtual.

Vincular um espaço de nome de ônibus de serviço a uma rede virtual é um processo em duas etapas. Primeiro é necessário criar um **ponto final** de serviço de Rede Virtual numa subnet de Rede Virtual e capacitá-lo para a **Microsoft.ServiceBus,** conforme explicado na visão geral do [ponto final](service-bus-service-endpoints.md)do serviço . Depois de ter adicionado o ponto final do serviço, ligue-lhe o espaço de nome do Ônibus de serviço com uma regra de **rede virtual**.

A regra da rede virtual é uma associação do espaço de nome do Service Bus com uma subnet de rede virtual. Enquanto a regra existe, todas as cargas de trabalho ligadas à subnet têm acesso ao espaço de nome do Ônibus de serviço. O próprio Service Bus nunca estabelece ligações de saída, não precisa de ter acesso, pelo que nunca é concedido acesso à sua subnet, permitindo esta regra.

Para mais informações, consulte [Como configurar pontos finais](service-bus-service-endpoints.md) de serviço de rede virtual para um espaço de nome de ônibus de serviço

## <a name="private-endpoints"></a>Pontos finais privados

O Azure Private Link Service permite-lhe aceder aos serviços Azure (por exemplo, Azure Service Bus, Azure Storage e Azure Cosmos DB) e o Azure acolheu serviços de clientes/parceiros sobre um **ponto final privado** na sua rede virtual.

Um ponto final privado é uma interface de rede que o conecta de forma privada e segura a um serviço alimentado por Azure Private Link. O ponto final privado utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a uma instância de um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acesso.

Para mais informações, consulte [o que é azure private link?](../private-link/private-link-overview.md)

> [!NOTE]
> Esta funcionalidade é suportada com o nível **premium** do Azure Service Bus. Para mais informações sobre o nível premium, consulte o artigo service [bus premium e standard de mensagens tiers.](service-bus-premium-messaging.md)
>
> Esta funcionalidade encontra-se atualmente em **pré-visualização**. 


Para mais informações, consulte [Como configurar pontos finais privados para um espaço de nome de ônibus de serviço](private-link-service.md)


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como configurar a firewall IP para um espaço de nome de ônibus de serviço](service-bus-ip-filtering.md)
- [Como configurar pontos finais de serviço de rede virtual para um espaço de nome de ônibus de serviço](service-bus-service-endpoints.md)
- [Como configurar pontos finais privados para um espaço de nome de ônibus de serviço](private-link-service.md)
