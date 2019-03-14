---
title: 'Elimine um gateway de rede virtual: PowerShell: O Azure Resource Manager | Documentos da Microsoft'
description: Elimine um gateway de rede virtual com o PowerShell no modelo de implementação do Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: bf1cc3b95e8335b6e24dd405d82e5c51d2a8d11b
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792689"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Eliminar um gateway de rede virtual com o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Existem duas abordagens diferentes, que pode tomar para eliminar um gateway de rede virtual para uma configuração de gateway VPN.

- Se pretender eliminar tudo e começar de novo, como no caso de um ambiente de teste, pode eliminar o grupo de recursos. Quando elimina um grupo de recursos, elimina todos os recursos dentro do grupo. Este é o método só é recomendada se não pretende manter qualquer um dos recursos no grupo de recursos. Não é possível eliminar seletivamente apenas a alguns recursos, o uso dessa abordagem.

- Se quiser manter alguns dos recursos no grupo de recursos, a eliminar um gateway de rede virtual fica um pouco mais complicada. Antes de poder eliminar o gateway de rede virtual, tem primeiro de eliminar todos os recursos que são dependentes no gateway. Os passos que segue dependem do tipo de ligações que criou e os recursos dependentes para cada ligação.

## <a name="before-beginning"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Baixe os mais recentes cmdlets do PowerShell do Azure Resource Manager.

Baixe e instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para obter mais informações sobre como transferir e instalar os cmdlets do PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Ligar à sua conta do Azure.

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

## <a name="S2S"></a>Eliminar um gateway de VPN de Site a Site

Para eliminar um gateway de rede virtual para uma configuração de S2S, tem primeiro de eliminar cada recurso que diz respeito ao gateway de rede virtual. Recursos tem de ser eliminados numa determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do grupo de recursos: RG1<br>
Nome do gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação do Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obter o gateway de rede virtual que pretende eliminar.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o gateway de rede virtual tem quaisquer ligações.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Elimine todas as ligações.

Poderá ser-lhe pedido para confirmar a eliminação de cada uma das ligações.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Elimine o gateway de rede virtual.

Poderá ser-lhe pedido para confirmar a eliminação do gateway. Se tiver uma configuração P2S para esta VNet, além de sua configuração de S2S, a eliminação do gateway de rede virtual desligará automaticamente todos os clientes de P2S sem aviso.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste momento, o gateway de rede virtual foi eliminado. Pode utilizar os passos seguintes para eliminar todos os recursos que já não estão a ser utilizados.

### <a name="5-delete-the-local-network-gateways"></a>5 eliminar os gateways de rede local.

Obter a lista de gateways da rede local correspondente.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Elimine os gateways de rede local. Poderá ser-lhe pedido para confirmar a eliminação de cada gateway de rede local.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Elimine os recursos de endereço IP público.

Obter as configurações de IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obter a lista de recursos de endereço IP público utilizado para este gateway de rede virtual. Se o gateway de rede virtual foi ativo-ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Elimine os recursos de IP público.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Eliminar a sub-rede de gateway e definir a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>Eliminar um gateway de VPN de VNet a VNet

Para eliminar um gateway de rede virtual para uma configuração de V2V, tem primeiro de eliminar cada recurso que diz respeito ao gateway de rede virtual. Recursos tem de ser eliminados numa determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do grupo de recursos: RG1<br>
Nome do gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação do Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obter o gateway de rede virtual que pretende eliminar.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o gateway de rede virtual tem quaisquer ligações.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Podem existir outras ligações para o gateway de rede virtual que fazem parte de um grupo de recursos diferente. Verificar a existência de ligações adicionais em cada grupo de recursos adicionais. Neste exemplo, estamos a verificar a para ligações a partir de RG2. Executá-lo para cada grupo de recursos que tenha que pode ter uma ligação para o gateway de rede virtual.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Obter a lista de ligações em ambas as direções.

Como se trata de uma configuração de VNet a VNet, tem da lista de ligações em ambas as direções.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Neste exemplo, estamos a verificar a para ligações a partir de RG2. Executá-lo para cada grupo de recursos que tenha que pode ter uma ligação para o gateway de rede virtual.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Elimine todas as ligações.

Poderá ser-lhe pedido para confirmar a eliminação de cada uma das ligações.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Elimine o gateway de rede virtual.

Poderá ser-lhe pedido para confirmar a eliminação do gateway de rede virtual. Se tiver configurações P2S para as suas VNets, além de sua configuração de V2V, a eliminar os gateways de rede virtual desligará automaticamente todos os clientes de P2S sem aviso.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste momento, o gateway de rede virtual foi eliminado. Pode utilizar os passos seguintes para eliminar todos os recursos que já não estão a ser utilizados.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Eliminar os recursos de endereço IP público

Obter as configurações de IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obter a lista de recursos de endereço IP público utilizado para este gateway de rede virtual. Se o gateway de rede virtual foi ativo-ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Elimine os recursos de IP público. Poderá ser-lhe pedido para confirmar a eliminação de IP público.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Eliminar a sub-rede de gateway e definir a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Eliminar um gateway de VPN ponto a Site

Para eliminar um gateway de rede virtual para uma configuração P2S, tem primeiro de eliminar cada recurso que diz respeito ao gateway de rede virtual. Recursos tem de ser eliminados numa determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do grupo de recursos: RG1<br>
Nome do gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação do Resource Manager.


>[!NOTE]
> Ao eliminar o gateway de VPN, todos os clientes ligados serão desligados da VNet sem aviso.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obter o gateway de rede virtual que pretende eliminar.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Elimine o gateway de rede virtual.

Poderá ser-lhe pedido para confirmar a eliminação do gateway de rede virtual.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste momento, o gateway de rede virtual foi eliminado. Pode utilizar os passos seguintes para eliminar todos os recursos que já não estão a ser utilizados.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Eliminar os recursos de endereço IP público

Obter as configurações de IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obter a lista de endereços IP públicos utilizados para este gateway de rede virtual. Se o gateway de rede virtual foi ativo-ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Elimine os IPs públicos. Poderá ser-lhe pedido para confirmar a eliminação de IP público.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Eliminar a sub-rede de gateway e definir a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Eliminar um gateway de VPN, eliminando o grupo de recursos

Se não estiver preocupado manter qualquer um dos seus recursos no grupo de recursos e pretender começar de novo, pode eliminar um grupo de recursos inteiro. Esta é uma forma rápida de remover tudo. Os seguintes passos aplicam-se apenas para o modelo de implementação do Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Obter uma lista de todos os grupos de recursos na sua subscrição.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Localize o grupo de recursos que pretende eliminar.

Localize o grupo de recursos que pretende eliminar e ver a lista de recursos nesse grupo de recursos. No exemplo, o nome do grupo de recursos é RG1. Modifique o exemplo para obter uma lista de todos os recursos.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Verifique se os recursos na lista.

Quando a lista é retornada, analisá-lo-certifique-se de que pretende eliminar todos os recursos no grupo de recursos, bem como o grupo de recursos. Se quiser manter alguns dos recursos no grupo de recursos, utilize os passos nas secções anteriores deste artigo para eliminar o seu gateway.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Elimine o grupo de recursos e recursos.

Para eliminar o grupo de recursos e todos os recursos contidos no grupo de recursos, modifique o exemplo e execute.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Verificar o estado.

Demora algum tempo para o Azure para eliminar todos os recursos. Pode verificar o estado do grupo de recursos utilizando este cmdlet.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

O resultado devolvido mostra "Com êxito".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
