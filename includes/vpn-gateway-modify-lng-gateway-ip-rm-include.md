---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/28/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3e4f5c07602d5bc1b7760793664415f092301c20
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444302"
---
### <a name="gwipnoconnection"></a> Para modificar o "GatewayIpAddress" do gateway de rede local - sem ligação de gateway

Se o dispositivo VPN que pretende ligar foi alterou o respetivo endereço IP público, tem de modificar o gateway de rede local para que essa alteração seja refletida. Utilize o exemplo para modificar um gateway de rede local que não tem uma ligação de gateway.

Ao modificar este valor, também pode modificar os prefixos de endereços em simultâneo. Certifique-se de que utiliza o nome existente do seu gateway de rede local, de modo a substituir as definições atuais. Se utilizar um nome diferente, irá criar um novo gateway de rede local, em vez de substituir o existente.

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a> Para modificar o "GatewayIpAddress" do gateway de rede local - ligação de gateway existente

Se o dispositivo VPN que pretende ligar foi alterou o respetivo endereço IP público, tem de modificar o gateway de rede local para que essa alteração seja refletida. Se já existir uma ligação de gateway, tem primeiro de remover a ligação. Depois de a ligação ser removida, pode modificar o endereço IP do gateway e recriar uma nova ligação. Também pode modificar os prefixos de endereços em simultâneo. Este procedimento resulta num período de indisponibilidade da ligação VPN. Ao modificar os endereços IP de gateway, não precisa de eliminar o gateway de VPN. Só tem de remover a ligação.
 

1. Remova a ligação. Pode encontrar o nome da ligação com o cmdlet "Get-AzureRmVirtualNetworkGatewayConnection".

   ```azurepowershell-interactive
   Remove-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1
   ```
2. Modifique o valor "GatewayIpAddress". Também pode modificar os prefixos de endereços em simultâneo. Certifique-se de que utiliza o nome existente do seu gateway de rede local, para substituir as definições atuais. Se não o fizer, irá criar um novo gateway de rede local, em vez de substituir o existente.

   ```azurepowershell-interactive
   New-AzureRmLocalNetworkGateway -Name Site1 `
   -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
   -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
   ```
3. Crie a ligação. Neste exemplo, configuramos uma ligação do tipo IPsec. Ao recriar a ligação, utilize o tipo de ligação especificado para a sua configuração. Para tipos de ligação adicionais, veja a página [cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).  Para obter o nome do VirtualNetworkGateway, pode executar o cmdlet "Get-AzureRmVirtualNetworkGateway".
   
    Defina as variáveis.

   ```azurepowershell-interactive
   $local = Get-AzureRMLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
   $vnetgw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   ```
   
    Crie a ligação.

   ```azurepowershell-interactive 
   New-AzureRmVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
   -Location "East US" `
   -VirtualNetworkGateway1 $vnetgw `
   -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```