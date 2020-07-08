---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922ac7eb6cb9676af65700a6a2fe7fbae35a0dc5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184792"
---
Os passos para esta tarefa utilizam um VNet com base nos valores da seguinte lista de referência de configuração. Definições e nomes adicionais também estão descritos nesta lista. Não usamos esta lista diretamente em nenhum dos passos, embora adicionemos variáveis com base nos valores desta lista. Pode copiar a lista para usar como referência, substituindo os valores pelos seus.

* Nome da rede virtual = "TestvNet"
* Espaço de endereço de rede virtual = 192.168.0.0/16
* Grupo de Recursos = "TestRG"
* Nome subnet1 = "FrontEnd" 
* Espaço de endereço subnet1 = "192.168.1.0/24"
* Nome da sub-rede Gateway: "GatewaySubnet" Deve sempre nomear uma sub-rede *gateway GatewaySubnet*.
* Espaço de endereço da Sub-rede Gateway = "192.168.200.0/26"
* Região = "Leste dos EUA"
* Nome gateway = "GW"
* Gateway IP Name = "GWIP"
* Gateway IP configuração Nome = "gwipconf"
* Tipo = "ExpressRoute" Este tipo é necessário para uma configuração ExpressRoute.
* Gateway Public IP Name = "gwpip"

## <a name="add-a-gateway"></a>Adicionar um gateway
1. Ligue-se à sua Assinatura Azure.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Declare as suas variáveis para este exercício. Certifique-se de editar a amostra para refletir as definições que pretende utilizar.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Guarde o objeto de rede virtual como uma variável.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Adicione uma sub-rede de gateway à sua Rede Virtual. A sub-rede gateway deve ser denominada "GatewaySubnet". Deve criar uma sub-rede de gateway que seja /27 ou maior (/26, /25, etc.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Defina a configuração.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Guarde a sub-rede gateway como variável.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Solicitar um endereço IP público. O endereço IP é solicitado antes de criar o gateway. Não é possível especificar o endereço IP que pretende utilizar; é dinamicamente atribuído. Utilizará este endereço IP na secção de configuração seguinte. O "A Atribuição de Café" deve ser dinâmico.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Crie a configuração para o seu gateway. A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Neste passo, está a especificar a configuração que será utilizada quando criar o gateway. Este passo não cria realmente o objeto de gateway. Utilize o exemplo abaixo para criar a configuração do gateway.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Criar o portal. Neste passo, o **-GatewayType** é especialmente importante. Deve utilizar o valor **ExpressRoute**. Depois de executar estes cmdlets, o gateway pode demorar 45 minutos ou mais a criar.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Verifique se o portal foi criado
Utilize os seguintes comandos para verificar se o gateway foi criado:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Resize a gateway (Redimensionar um gateway)
Há uma série de [SKUs gateway.](../articles/expressroute/expressroute-about-virtual-network-gateways.md) Pode utilizar o seguinte comando para alterar o Gateway SKU a qualquer momento.

> [!IMPORTANT]
> Este comando não funciona para o Portal UltraPerformance. Para alterar a sua porta de entrada para um gateway UltraPerformance, primeiro remova o gateway ExpressRoute existente e, em seguida, crie um novo gateway UltraPerformance. Para desclassificar o seu portal a partir de um gateway UltraPerformance, primeiro remova o gateway UltraPerformance e, em seguida, crie um novo portal.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Remover um gateway
Utilize o seguinte comando para remover um gateway:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
