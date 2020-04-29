---
title: 'Gateway Azure VPN: Eliminar uma porta de entrada: PowerShell'
description: Elimine um portal de rede virtual utilizando o PowerShell no modelo de implementação do Gestor de Recursos.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: f351f14796ec736bd5525f139a518c9a0dd3d19f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162111"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Eliminar um portal de rede virtual usando o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Existem algumas abordagens diferentes que pode tomar quando pretende eliminar uma porta de entrada de rede virtual para uma configuração de gateway VPN.

- Se quiser apagar tudo e recomeçar, como no caso de um ambiente de teste, pode eliminar o grupo de recursos. Ao eliminar um grupo de recursos, elimina todos os recursos dentro do grupo. Este método só é recomendado se não quiser manter nenhum dos recursos no grupo de recursos. Não é possível eliminar seletivamente apenas alguns recursos usando esta abordagem.

- Se quiser manter alguns dos recursos no seu grupo de recursos, apagar um portal de rede virtual torna-se um pouco mais complicado. Antes de poder eliminar o portal de rede virtual, tem primeiro de eliminar quaisquer recursos que dependam do portal. Os passos que segue dependem do tipo de ligações que criou e dos recursos dependentes para cada ligação.

## <a name="before-beginning"></a>Antes de começar



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Descarregue os mais recentes cmdlets do Gestor de Recursos Azure PowerShell.

Descarregue e instale a versão mais recente dos cmdlets PowerShell do Gestor de Recursos Do Azure. Para mais informações sobre o download e instalação de cmdlets PowerShell, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Ligue-se à sua conta Azure.

Abra a consola do PowerShell e ligue-se à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

```powershell
Connect-AzAccount
```

Verifique as subscrições da conta.

```powershell
Get-AzSubscription
```

Se tiver mais de uma subscrição, especifique a subscrição que pretende utilizar.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Eliminar um gateway VPN site-to-site

Para eliminar um portal de rede virtual para uma configuração S2S, tem primeiro de eliminar cada recurso que se refere ao portal de rede virtual. Os recursos devem ser suprimidos numa determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para efeitos de demonstração:

Nome VNet: VNet1<br>
Nome do Grupo de Recursos: RG1<br>
Nome de gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação do Gestor de Recursos.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o portal de rede virtual que pretende eliminar.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o portal de rede virtual tem alguma ligação.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Elimine todas as ligações.

Pode ser solicitado que confirme a eliminação de cada uma das ligações.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Apagar o portal da rede virtual.

Pode ser solicitado que confirme a supressão do portal. Se tiver uma configuração P2S para este VNet para além da sua configuração S2S, eliminar o gateway de rede virtual desligará automaticamente todos os clientes P2S sem aviso prévio.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste ponto, o seu portal de rede virtual foi eliminado. Pode utilizar os próximos passos para eliminar quaisquer recursos que já não estejam a ser utilizados.

### <a name="5-delete-the-local-network-gateways"></a>5 Elimine os portões da rede local.

Obtenha a lista dos portais de rede locais correspondentes.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Elimine os portões da rede local. Pode ser solicitado que confirme a eliminação de cada um dos portões da rede local.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Eliminar os recursos de endereço IP públicos.

Obtenha as configurações IP do gateway da rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obtenha a lista de recursos de endereçoip público utilizados para este gateway de rede virtual. Se o portal de rede virtual estiver ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Eliminar os recursos ip públicos.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Elimine a sub-rede de gateway e desloque a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Eliminar um gateway VNet-to-VNet VPN

Para eliminar um portal de rede virtual para uma configuração V2V, tem primeiro de eliminar cada recurso que diz respeito ao portal de rede virtual. Os recursos devem ser suprimidos numa determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para efeitos de demonstração:

Nome VNet: VNet1<br>
Nome do Grupo de Recursos: RG1<br>
Nome de gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação do Gestor de Recursos.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o portal de rede virtual que pretende eliminar.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o portal de rede virtual tem alguma ligação.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Pode haver outras ligações com o portal de rede virtual que fazem parte de um grupo de recursos diferente. Verifique se há ligações adicionais em cada grupo de recursos adicionais. Neste exemplo, estamos a verificar se há ligações a partir do RG2. Execute isto para cada grupo de recursos que você tem que pode ter uma ligação com o gateway da rede virtual.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Obtenha a lista de ligações em ambas as direções.

Como esta é uma configuração VNet-to-VNet, você precisa da lista de ligações em ambas as direções.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Neste exemplo, estamos a verificar se há ligações a partir do RG2. Execute isto para cada grupo de recursos que você tem que pode ter uma ligação com o gateway da rede virtual.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Apagar todas as ligações.

Pode ser solicitado que confirme a eliminação de cada uma das ligações.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Apagar o portal da rede virtual.

Pode ser solicitado que confirme a eliminação do portal da rede virtual. Se tiver configurações P2S para os seus VNets para além da sua configuração V2V, eliminar os gateways de rede virtual desligará automaticamente todos os clientes P2S sem aviso prévio.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste ponto, o seu portal de rede virtual foi eliminado. Pode utilizar os próximos passos para eliminar quaisquer recursos que já não estejam a ser utilizados.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Eliminar os recursos de endereço IP público

Obtenha as configurações IP do gateway da rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obtenha a lista de recursos de endereçoip público utilizados para este gateway de rede virtual. Se o portal de rede virtual estiver ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Eliminar os recursos ip públicos. Pode ser solicitado que confirme a eliminação do IP Público.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Elimine a sub-rede de gateway e desloque a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Eliminar um gateway VPN ponto-a-site

Para eliminar um portal de rede virtual para uma configuração P2S, tem primeiro de eliminar cada recurso que se refere ao portal de rede virtual. Os recursos devem ser suprimidos numa determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para efeitos de demonstração:

Nome VNet: VNet1<br>
Nome do Grupo de Recursos: RG1<br>
Nome de gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação do Gestor de Recursos.


>[!NOTE]
> Ao eliminar o gateway VPN, todos os clientes ligados serão desligados do VNet sem aviso prévio.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o portal de rede virtual que pretende eliminar.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Elimine o portal da rede virtual.

Pode ser solicitado que confirme a eliminação do portal da rede virtual.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste ponto, o seu portal de rede virtual foi eliminado. Pode utilizar os próximos passos para eliminar quaisquer recursos que já não estejam a ser utilizados.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Eliminar os recursos de endereço IP público

Obtenha as configurações IP do gateway da rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obtenha a lista de endereços IP públicos utilizados para este gateway de rede virtual. Se o portal de rede virtual estiver ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Eliminar os IPs públicos. Pode ser solicitado que confirme a eliminação do IP Público.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Elimine a sub-rede de gateway e desloque a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>Eliminar um gateway VPN eliminando o grupo de recursos

Se não está preocupado em manter nenhum dos seus recursos no grupo de recursos e apenas quer recomeçar, pode eliminar todo um grupo de recursos. Esta é uma maneira rápida de remover tudo. Os seguintes passos aplicam-se apenas ao modelo de implementação do Gestor de Recursos.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Obtenha uma lista de todos os grupos de recursos da sua subscrição.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Localize o grupo de recursos que pretende eliminar.

Localize o grupo de recursos que pretende eliminar e ver a lista de recursos desse grupo de recursos. No exemplo, o nome do grupo de recursos é RG1. Modifique o exemplo para recuperar uma lista de todos os recursos.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Verifique os recursos da lista.

Quando a lista for devolvida, reveja-a para verificar se pretende eliminar todos os recursos do grupo de recursos, bem como o próprio grupo de recursos. Se quiser manter alguns dos recursos no grupo de recursos, utilize os passos nas secções anteriores deste artigo para apagar o seu portal.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Eliminar o grupo de recursos e os recursos.

Para eliminar o grupo de recursos e todos os recursos contidos no grupo de recursos, modifique o exemplo e execute.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Verifique o estado.

Leva algum tempo para o Azure apagar todos os recursos. Pode verificar o estado do seu grupo de recursos utilizando este cmdlet.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

O resultado que é devolvido mostra "Sucesso".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
