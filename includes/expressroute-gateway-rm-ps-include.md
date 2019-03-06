---
title: incluir ficheiro
description: incluir ficheiro
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c50e2b082c3181c37e9d129766d4bf400075d5a8
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410687"
---
Os passos para esta tarefa utilizam uma VNet com base nos valores na lista de referência de configuração seguinte. Definições adicionais e os nomes também são descritos nesta lista. Não usamos essa lista diretamente em qualquer um dos passos, embora podemos adicionar variáveis com base nos valores nesta lista. Pode copiar a lista para utilizar como uma referência, substituindo os valores pelos seus próprios.

* Nome da rede virtual = "TestVNet"
* Espaço de endereços de rede virtual = 192.168.0.0/16
* Resource Group = "TestRG"
* Subnet1 nome = "FrontEnd" 
* Espaço de endereços de Subnet1 = "192.168.1.0/24"
* Nome da sub-rede de gateway: "GatewaySubnet", deve nomeá sempre uma sub-rede de gateway *GatewaySubnet*.
* Espaço de endereços de sub-rede de gateway = "192.168.200.0/26"
* Região = "Este dos E.U.A."
* Nome do gateway = "GW"
* Nome do gateway de IP = "GWIP"
* Configuração de IP do gateway Name = "gwipconf"
* Tipo = "ExpressRoute" deste tipo é necessário para uma configuração de ExpressRoute.
* Nome de IP público do gateway = "gwpip"

## <a name="add-a-gateway"></a>Adicionar um gateway
1. Ligar à sua subscrição do Azure.

  [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Declare as variáveis para este exercício. Certifique-se de que editar o exemplo para refletir as definições que pretende utilizar.

  ```azurepowershell-interactive 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. Store o objeto de rede virtual como uma variável.

  ```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. Adicione uma sub-rede de gateway à sua rede Virtual. A sub-rede do gateway tem de ser o nome "GatewaySubnet". Deve criar uma sub-rede de gateway é/27 ou superior (/ 26, / 25, etc.).

  ```azurepowershell-interactive
  Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. Defina a configuração.

  ```azurepowershell-interactive
  $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
  ```
6. Store a sub-rede do gateway como uma variável.

  ```azurepowershell-interactive
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. Solicitar um endereço IP público. O endereço IP é solicitado antes de criar o gateway. Não é possível especificar o endereço IP que pretende utilizar. é atribuído dinamicamente. Utilizará este endereço IP na secção de configuração seguinte. O allocationmethod como tem de ser dinâmico.

  ```azurepowershell-interactive
  $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. Crie a configuração para o seu gateway. A configuração do gateway define a sub-rede e o endereço IP público a utilizar. Neste passo, está a especificar a configuração que será utilizada ao criar o gateway. Este passo não cria, na verdade, o objeto de gateway. Utilize o exemplo abaixo para criar a configuração do gateway.

  ```azurepowershell-interactive
  $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. Crie o gateway. Neste passo, o **- GatewayType** é especialmente importante. Tem de utilizar o valor **ExpressRoute**. Depois de executar estes cmdlets, o gateway pode demorar 45 minutos ou mais para criar.

  ```azurepowershell-interactive
  New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>Certifique-se de que a criação do gateway
Utilize os seguintes comandos para verificar que foi criado o gateway:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Redimensionar um gateway
Existem diversas [SKUs de Gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Pode utilizar o seguinte comando para alterar o SKU de Gateway em qualquer altura.

> [!IMPORTANT]
> Este comando não funcionar para o gateway de UltraPerformance. Para alterar o gateway para um gateway de UltraPerformance, remova primeiro o gateway do ExpressRoute existente e, em seguida, crie um novo gateway de UltraPerformance. Para mudar o gateway de um gateway de UltraPerformance, para remover primeiro o gateway de UltraPerformance e, em seguida, crie um novo gateway.
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
