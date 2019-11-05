---
title: Seletores de tráfego personalizados para conexões de gateway de VPN do Azure | Microsoft Docs
description: Saiba mais sobre como usar seletores de tráfego personalizados em conexões de gateway de VPN
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: radwiv
ms.openlocfilehash: cf9401b21d4aa736a6edeae5146d1355f2d49d1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512161"
---
# <a name="custom-traffic-selectors-for-vpn-gateway-connections"></a>Seletores de tráfego personalizados para conexões de gateway de VPN

Você pode definir seletores de tráfego personalizados em suas conexões de gateway de VPN. A capacidade de definir seletores de tráfego permite restringir espaços de endereço de ambos os lados dos túneis VPN nos quais você deseja enviar tráfego. Os seletores de tráfego personalizados são particularmente úteis quando você tem um grande espaço de endereço de VNet, mas deseja usar uma de suas sub-redes para negociação de IPsec/IKE.

Os seletores de tráfego personalizados podem ser adicionados durante a criação de uma nova conexão ou podem ser atualizados para uma conexão existente. O parâmetro `TrafficSelectorPolicies` consiste em uma matriz de seletores de tráfego. Cada seletor de tráfego contém uma coleção de intervalos de endereços locais e remotos no formato CIDR.

## <a name="add-custom-traffic-selectors"></a>Adicionar seletores de tráfego personalizados

Os exemplos a seguir mostram como os seletores de tráfego personalizados podem ser criados para uma conexão de gateway de rede virtual usando comandos do PowerShell. Para obter ajuda sobre como criar uma conexão de gateway de rede virtual, consulte [Configurar uma conexão site a site](vpn-gateway-create-site-to-site-rm-powershell.md).

1. Defina valores para o parâmetro *trafficselectorpolicies* em $trafficselectorpolicy. Ao definir isso, observe que o valor *New-AzTrafficSelectorPolicy* usa intervalos de endereços locais e remotos em uma matriz.

   ```azurepowershell-interactive
   $trafficSelectorPolicy = New-AzTrafficSelectorPolicy `
   -LocalAddressRanges ("10.10.10.0/24", "20.20.20.0/24") `
   -RemoteAddressRanges ("30.30.30.0/24", "40.40.40.0/24")
   ```
2. Crie uma nova conexão de gateway de rede virtual. Ajuste os parâmetros de valor para seus requisitos.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection `
   -ResourceGroupName $rgname `
   -name $vnetConnectionName `
   -location $location `
   -VirtualNetworkGateway1 $vnetGateway `
   -LocalNetworkGateway2 $localnetGateway `
   -ConnectionType IPsec `
   -RoutingWeight 3 `
   -SharedKey $sharedKey `
   -UsePolicyBasedTrafficSelectors $true `
   -TrafficSelectorPolicies ($trafficSelectorPolicy)
   ```

Você também pode atualizar seletores de tráfego personalizados para uma conexão de gateway de rede virtual existente usando ' Set-AzVirtualNetworkGatewayConnection '. Para obter valores do PowerShell, consulte [conexão de gateway de rede virtual](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre gateways de VPN, consulte [sobre o gateway de VPN](vpn-gateway-about-vpngateways.md).