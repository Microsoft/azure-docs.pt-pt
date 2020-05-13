---
title: Segurança da rede para O Relé Azure
description: Este artigo descreve como configurar o acesso a partir de pontos finais privados
services: service-bus-relay
author: spelluru
ms.service: service-bus-relay
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: d0a0d03680877ae78f5af939f8d47e4e426abadb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211017"
---
# <a name="network-security-for-azure-relay"></a>Segurança da rede para O Relé Azure 
Este artigo descreve como usar as seguintes funcionalidades de segurança com o Azure Relay: 

- Regras de firewall IP (pré-visualização)
- Pontos finais privados (pré-visualização)

> [!NOTE]
> A Azure Relay não suporta pontos finais do serviço de rede. 


## <a name="ip-firewall"></a>Firewall de IP 
Por predefinição, os espaços de nome retransmissão são acessíveis a partir da internet desde que o pedido venha com autenticação e autorização válidas. Com firewall IP, pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que o Azure Relay só deve ser acessível a partir de certos sites bem conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar o tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar o Relay com a [Rota Expresso Azure,](/azure/expressroute/expressroute-faqs#supported-services)pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos seus endereços IP de infraestrutura no local. 

As regras de firewall IP são aplicadas ao nível do espaço de nome retransmissor. Por isso, as regras aplicam-se a todas as ligações dos clientes que utilizam qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra ip permitida no espaço de nome retransmissor é rejeitada como não autorizada. A resposta não menciona a regra do PI. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a aceitação ou rejeita a ação.

Para mais informações, consulte Como configurar a firewall IP para um espaço de nome de [retransmissão](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>Pontos finais privados

O **Azure Private Link Service** permite-lhe aceder aos serviços Azure (por exemplo, Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage e Azure Cosmos DB) e o Azure acolheu serviços de clientes/parceiros sobre um ponto final privado na sua rede virtual. Para mais informações, consulte [O que é azure private link (Pré-visualização)?](../private-link/private-link-overview.md)

Um **ponto final privado** é uma interface de rede que permite que as suas cargas de trabalho em funcionamento numa rede virtual se conectem de forma privada e segura a um serviço que tem um recurso de **ligação privada** (por exemplo, um espaço de nome retransmissor). O ponto final privado utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ExpressRoute, ligações VPN ou endereços IP públicos. O tráfego entre a sua rede virtual e o serviço atravessa a rede de espinha dorsal da Microsoft, eliminando a exposição da Internet pública. Pode fornecer um nível de granularidade no controlo de acesso, permitindo ligações a espaços específicos de identificação do Relé Azure.

> [!NOTE]
> Esta funcionalidade encontra-se atualmente em **pré-visualização**. 

Para mais informações, consulte [Como configurar pontos finais privados](private-link-service.md)


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como configurar firewall IP](ip-firewall-virtual-networks.md)
- [Como configurar pontos finais privados](private-link-service.md)