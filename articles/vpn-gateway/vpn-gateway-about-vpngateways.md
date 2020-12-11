---
title: Sobre Azure VPN Gateway
description: Saiba o que é um Gateway VPN e como usar um Gateway VPN para ligar às redes virtuais IPsec IKE Site-to-Site, VNet-to-VNet e Point-to-Site VPN.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom: contperf-fy21q1
ms.openlocfilehash: c19e1156f897873defaba4cebaabb904d034ead3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032750"
---
# <a name="what-is-vpn-gateway"></a>O que é um Gateway de VPN?

Um gateway de VPN é um tipo específico de gateway de rede virtual utilizado para enviar tráfego encriptado entre uma rede virtual do Azure e uma localização no local através da Internet pública. Também pode utilizar um gateway de VPN para enviar tráfego encriptado entre as redes virtuais do Azure através da rede da Microsoft. Cada rede virtual pode ter apenas um gateway de VPN. No entanto, pode criar várias ligações para o mesmo gateway de VPN. Quando cria várias ligações para o mesmo gateway de VPN, todos os túneis de VPN partilham a largura de banda do gateway disponível.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>O que é um gateway de rede virtual?

Um gateway de rede virtual é composto por dois ou mais VMs que são implantados numa sub-rede específica que cria chamada *sub-rede gateway*. Os VMs de gateway de rede virtual contêm tabelas de encaminhamento e executam serviços de gateway específicos. Estes VMs são criados quando cria o gateway de rede virtual. Não é possível configurar diretamente os VMs que fazem parte do portal de rede virtual.

Ao configurar um gateway de rede virtual, configura uma definição que especifica o tipo de gateway. O tipo de gateway determina como o gateway de rede virtual será usado e as ações que o gateway toma. O tipo de gateway 'Vpn' especifica que o tipo de gateway de rede virtual criado é um 'gateway VPN'. Isto distingue-o de um gateway ExpressRoute, que usa um tipo de gateway diferente. Uma rede virtual pode ter dois gateways de rede virtuais; uma porta de entrada VPN e uma porta de entrada ExpressRoute. Para obter mais informações, consulte [os tipos gateway](vpn-gateway-about-vpn-gateway-settings.md#gwtype).

A criação de um gateway de rede virtual pode demorar até 45 minutos a concluir. Quando cria um gateway de rede virtual, as VMs do gateway são implementadas na sub-rede do gateway e configuradas com as definições que especificar. Depois de criar um gateway de VPN, pode criar uma ligação de túnel de VPN IPsec/IKE entre esse gateway de VPN e outro gateway de VPN (VNet a VNet) ou criar uma ligação de túnel de VPN IPsec/IKE entre vários locais entre o gateway de VPN e um dispositivo VPN no local (Site a Site). Também pode criar uma ligação VPN ponto-a-local (VPN sobre OpenVPN, IKEv2 ou SSTP), que permite ligar-se à sua rede virtual a partir de um local remoto, como a partir de uma conferência ou de casa.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>Configurar um Gateway de VPN

Uma ligação de gateway de VPN depende de vários recursos que estão configurados com definições específicas. A maior parte dos recursos pode ser configurada em separado, embora alguns recursos tenham de ser configurados numa determinada ordem.

### <a name="design"></a><a name="diagrams"></a>Design

É importante saber que existem configurações diferentes disponíveis para as ligações de gateway VPN. Deve determinar qual das configurações se adequa melhor às suas necessidades. Por exemplo, as ligações Ponto-a-Local, Site-a-Site e conexões ExpressRoute/Site-to-Site têm diferentes requisitos de instruções e configuração. Para obter informações sobre design e para visualizar os diagramas de topologia de ligação, consulte [Design](design.md).

### <a name="planning-table"></a><a name="planningtable"></a>Tabela de planeamento

A tabela seguinte pode ajudá-lo a decidir a melhor opção de conectividade para a sua solução.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>Definições

As definições que escolheu para cada recurso são essenciais para a criação de uma ligação com êxito. Para obter informações sobre recursos individuais e sobre definições para o Gateway de VPN, consulte [definições sobre o Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md). O artigo contém informações para ajudar a compreender os tipos de gateway, os SKUs de gateway, os tipos de VPN, os tipos de ligação, as sub-redes de gateway, os gateways de rede local e várias outras definições de recursos que poderá querer considerar.

### <a name="deployment-tools"></a><a name="tools"></a>Ferramentas de implementação

Pode começar por criar e configurar recursos utilizando uma ferramenta de configuração, como o portal do Azure. Mais tarde, pode decidir mudar para outra ferramenta, como o PowerShell, para configurar recursos adicionais ou modificar os recursos existentes, quando aplicável. Atualmente, não pode configurar todos os recursos e definições de recursos no portal do Azure. As instruções nos artigos para cada topologia de ligação especificam quando uma ferramenta de configuração especifica é necessária.

## <a name="gateway-skus"></a><a name="gwsku"></a>SKUs de Gateway

Quando cria um gateway de rede virtual, tem de especificar o SKU de gateway que pretende utilizar. Selecione o SKU que atende as suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, funcionalidades e SLA.

* Para obter mais informações sobre os SKUs de gateway, incluindo funcionalidades suportadas, produção e teste dev, e etapas de configuração, consulte as [Definições de Gateway VPN - Gateway SKUs.](vpn-gateway-about-vpn-gateway-settings.md#gwsku)
* Para obter informações sobre o Legacy SKU, consulte [Working with Legacy SKUs](vpn-gateway-about-skus-legacy.md).

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>SKUs de Gateway por túnel, ligação e débito

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>Zonas de Disponibilidade

Os gateways VPN podem ser implantados em Zonas de Disponibilidade Azure. Isto traz resiliência, escalabilidade e uma maior disponibilidade para os gateways de redes virtuais. Implementar gateways nas Zonas de Disponibilidade do Azure separa física e logicamente os gateways numa região, enquanto protege a sua conectividade de rede no local para o Azure contra falhas ao nível das zonas. ver [Sobre as portas de rede virtuais redundantes de zona em Zonas de Disponibilidade Azure](about-zone-redundant-vnet-gateways.md).

## <a name="pricing"></a><a name="pricing"></a>Preços

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Para obter mais informações sobre os SKUs de gateway para o Gateway de VPN, veja [SKUs de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a><a name="faq"></a>FAQ

Para perguntas mais frequentes sobre o Gateway de Aplicação, veja as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md).

## <a name="whats-new"></a><a name="new"></a>Novidades

Subscreva o feed RSS e veja as últimas atualizações da funcionalidade VPN Gateway na página [Azure Updates.](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway)

## <a name="next-steps"></a>Passos seguintes

- Veja as [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md) para obter mais informações.
- Veja [Subscription and service limits (Subscrições e limites do serviço)](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
- Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
