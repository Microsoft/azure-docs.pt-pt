---
title: Segurança da rede para relé Azure
description: Este artigo descreve como usar as regras de firewall IP e pontos finais privados com o Azure Relay.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 735a0b7dc303ad63cb301151e3e14e4488c856b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217248"
---
# <a name="network-security-for-azure-relay"></a>Segurança da rede para relé Azure 
Este artigo descreve como utilizar as seguintes funcionalidades de segurança com a Azure Relay: 

- Regras de firewall IP
- Pontos finais privados 

> [!NOTE]
> O Azure Relay não suporta pontos finais de serviço de rede. 


## <a name="ip-firewall"></a>Firewall de IP 
Por predefinição, os espaços de nome retransmissores estão acessíveis a partir da Internet desde que o pedido venha com autenticação e autorização válidas. Com a firewall IP, pode restringi-lo ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Encaminhamento).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Esta funcionalidade é útil em cenários em que o Azure Relay só deve ser acessível a partir de determinados sites conhecidos. As regras de firewall permitem-lhe configurar regras para aceitar tráfego originário de endereços IPv4 específicos. Por exemplo, se utilizar o Relay com [a Rota Expresso Azure,](../expressroute/expressroute-faqs.md#supported-services)pode criar uma regra de **firewall** para permitir o tráfego a partir apenas dos endereços IP da infraestrutura no local. 

As regras de firewall IP são aplicadas ao nível do espaço de identificação do Relay. Por isso, as regras aplicam-se a todas as ligações dos clientes utilizando qualquer protocolo suportado. Qualquer tentativa de ligação a partir de um endereço IP que não corresponda a uma regra ip permitida no espaço de nome Relay é rejeitada como não autorizada. A resposta não menciona a regra do IP. As regras do filtro IP são aplicadas por ordem, e a primeira regra que corresponde ao endereço IP determina a ação de aceitação ou rejeição.

Para obter mais informações, consulte [Como configurar firewall IP para um espaço de nome relay](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>Pontos finais privados

O Azure **Private Link Service** permite-lhe aceder aos serviços Azure (por exemplo, Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage e Azure Cosmos DB) e a Azure acolheu serviços de cliente/parceiro sobre um ponto final privado na sua rede virtual. Para mais informações, consulte [o que é a Azure Private Link?](../private-link/private-link-overview.md)

Um **ponto final privado** é uma interface de rede que permite que as suas cargas de trabalho em execução numa rede virtual se conectem de forma privada e segura a um serviço que tenha um recurso de **ligação privada** (por exemplo, um espaço de nome Relay). O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ExpressRoute, ligações VPN ou endereços IP públicos. O tráfego entre a sua rede virtual e o serviço atravessa a rede de espinha dorsal da Microsoft, eliminando a exposição da Internet pública. Pode fornecer um nível de granularidade no controlo de acesso, permitindo ligações a espaços específicos de nomes Azure Relay.

Para mais informações, consulte [Como configurar pontos finais privados](private-link-service.md)


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como configurar firewall IP](ip-firewall-virtual-networks.md)
- [Como configurar pontos finais privados](private-link-service.md)
