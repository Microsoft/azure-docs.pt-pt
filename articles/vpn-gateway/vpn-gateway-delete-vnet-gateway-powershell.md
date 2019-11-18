---
title: 'Gateway de VPN do Azure: excluir um gateway: PowerShell'
description: Exclua um gateway de rede virtual usando o PowerShell no modelo de implantação do Gerenciador de recursos.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: 2c02b656f8d7879115d25516bf49f49d9921a290
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146322"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Excluir um gateway de rede virtual usando o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Há algumas abordagens diferentes que você pode tomar quando deseja excluir um gateway de rede virtual para uma configuração de gateway de VPN.

- Se você quiser excluir tudo e começar novamente, como no caso de um ambiente de teste, você poderá excluir o grupo de recursos. Quando você exclui um grupo de recursos, ele exclui todos os recursos dentro do grupo. Esse método é recomendado apenas se você não quiser manter nenhum dos recursos no grupo de recursos. Você não pode excluir seletivamente apenas alguns recursos usando essa abordagem.

- Se você quiser manter alguns dos recursos em seu grupo de recursos, a exclusão de um gateway de rede virtual se tornará um pouco mais complicada. Antes de excluir o gateway de rede virtual, você deve primeiro excluir todos os recursos que dependem do gateway. As etapas a serem seguidas dependem do tipo de conexão que você criou e dos recursos dependentes de cada conexão.

## <a name="before-beginning"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Baixe os cmdlets mais recentes do PowerShell Azure Resource Manager.

Baixe e instale a versão mais recente dos cmdlets Azure Resource Manager PowerShell. Para obter mais informações sobre como baixar e instalar cmdlets do PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Conecte-se à sua conta do Azure.

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

```powershell
Connect-AzAccount
```

Verifique as subscrições da conta.

```powershell
Get-AzSubscription
```

Se você tiver mais de uma assinatura, especifique a assinatura que deseja usar.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Excluir um gateway de VPN site a site

Para excluir um gateway de rede virtual para uma configuração S2S, você deve primeiro excluir cada recurso que pertença ao gateway de rede virtual. Os recursos devem ser excluídos em uma determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Usamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do grupo de recursos: RG1<br>
Nome do gateway de rede virtual: GW1<br>

As etapas a seguir se aplicam ao modelo de implantação do Gerenciador de recursos.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o gateway de rede virtual que você deseja excluir.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o gateway de rede virtual tem conexões.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. exclua todas as conexões.

Talvez seja solicitado que você confirme a exclusão de cada uma das conexões.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. exclua o gateway de rede virtual.

Talvez seja solicitado que você confirme a exclusão do gateway. Se você tiver uma configuração de P2S para essa VNet além de sua configuração S2S, a exclusão do gateway de rede virtual desconectará automaticamente todos os clientes P2S sem aviso.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste ponto, o gateway de rede virtual foi excluído. Você pode usar as próximas etapas para excluir todos os recursos que não estão mais sendo usados.

### <a name="5-delete-the-local-network-gateways"></a>5 exclua os gateways de rede local.

Obtenha a lista de gateways de rede local correspondentes.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Exclua os gateways de rede local. Você pode ser solicitado a confirmar a exclusão de cada um dos gateways de rede local.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. exclua os recursos de endereço IP público.

Obtenha as configurações de IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obtenha a lista de recursos de endereço IP público usados para este gateway de rede virtual. Se o gateway de rede virtual estava ativo-ativo, você verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Exclua os recursos de IP públicos.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. exclua a sub-rede de gateway e defina a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>Excluir um gateway de VPN de VNet para VNet

Para excluir um gateway de rede virtual para uma configuração V2V, você deve primeiro excluir cada recurso que pertença ao gateway de rede virtual. Os recursos devem ser excluídos em uma determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Usamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do grupo de recursos: RG1<br>
Nome do gateway de rede virtual: GW1<br>

As etapas a seguir se aplicam ao modelo de implantação do Gerenciador de recursos.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o gateway de rede virtual que você deseja excluir.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o gateway de rede virtual tem conexões.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Pode haver outras conexões com o gateway de rede virtual que fazem parte de um grupo de recursos diferente. Verifique se há conexões adicionais em cada grupo de recursos adicional. Neste exemplo, estamos verificando conexões de RG2. Execute isso para cada grupo de recursos que você tem que pode ter uma conexão com o gateway de rede virtual.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Obtenha a lista de conexões em ambas as direções.

Como essa é uma configuração de VNet para VNet, você precisa da lista de conexões em ambas as direções.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Neste exemplo, estamos verificando conexões de RG2. Execute isso para cada grupo de recursos que você tem que pode ter uma conexão com o gateway de rede virtual.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. exclua todas as conexões.

Talvez seja solicitado que você confirme a exclusão de cada uma das conexões.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. exclua o gateway de rede virtual.

Talvez seja solicitado que você confirme a exclusão do gateway de rede virtual. Se você tiver configurações de P2S para seu VNets além da configuração do V2V, excluir os gateways de rede virtual desconectará automaticamente todos os clientes P2S sem aviso.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste ponto, o gateway de rede virtual foi excluído. Você pode usar as próximas etapas para excluir todos os recursos que não estão mais sendo usados.

### <a name="6-delete-the-public-ip-address-resources"></a>6. excluir os recursos de endereço IP público

Obtenha as configurações de IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obtenha a lista de recursos de endereço IP público usados para este gateway de rede virtual. Se o gateway de rede virtual estava ativo-ativo, você verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Exclua os recursos de IP públicos. Talvez seja solicitado que você confirme a exclusão do IP público.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. exclua a sub-rede de gateway e defina a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Excluir um gateway de VPN ponto a site

Para excluir um gateway de rede virtual para uma configuração P2S, você deve primeiro excluir cada recurso que pertença ao gateway de rede virtual. Os recursos devem ser excluídos em uma determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Usamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do grupo de recursos: RG1<br>
Nome do gateway de rede virtual: GW1<br>

As etapas a seguir se aplicam ao modelo de implantação do Gerenciador de recursos.


>[!NOTE]
> Quando você excluir o gateway de VPN, todos os clientes conectados serão desconectados da VNet sem aviso.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o gateway de rede virtual que você deseja excluir.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. exclua o gateway de rede virtual.

Talvez seja solicitado que você confirme a exclusão do gateway de rede virtual.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste ponto, o gateway de rede virtual foi excluído. Você pode usar as próximas etapas para excluir todos os recursos que não estão mais sendo usados.

### <a name="3-delete-the-public-ip-address-resources"></a>3. excluir os recursos de endereço IP público

Obtenha as configurações de IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obtenha a lista de endereços IP públicos usados para este gateway de rede virtual. Se o gateway de rede virtual estava ativo-ativo, você verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Exclua os IPs públicos. Talvez seja solicitado que você confirme a exclusão do IP público.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. exclua a sub-rede de gateway e defina a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Excluir um gateway de VPN excluindo o grupo de recursos

Se você não estiver preocupado em manter qualquer um dos seus recursos no grupo de recursos e quiser apenas recomeçar, poderá excluir um grupo de recursos inteiro. Essa é uma maneira rápida de remover tudo. As etapas a seguir se aplicam somente ao modelo de implantação do Gerenciador de recursos.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Obtenha uma lista de todos os grupos de recursos em sua assinatura.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Localize o grupo de recursos que você deseja excluir.

Localize o grupo de recursos que você deseja excluir e exiba a lista de recursos nesse grupo de recursos. No exemplo, o nome do grupo de recursos é RG1. Modifique o exemplo para recuperar uma lista de todos os recursos.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Verifique os recursos na lista.

Quando a lista for retornada, examine-a para verificar se você deseja excluir todos os recursos no grupo de recursos, bem como o próprio grupo de recursos. Se você quiser manter alguns dos recursos no grupo de recursos, use as etapas nas seções anteriores deste artigo para excluir o gateway.

### <a name="4-delete-the-resource-group-and-resources"></a>4. exclua o grupo de recursos e os recursos.

Para excluir o grupo de recursos e todos os recursos contidos no grupo de recursos, modifique o exemplo e execute.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Verifique o status.

Leva algum tempo para que o Azure exclua todos os recursos. Você pode verificar o status do seu grupo de recursos usando este cmdlet.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

O resultado retornado mostra ' êxito '.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
