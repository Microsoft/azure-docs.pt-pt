---
title: 'Gateway Azure VPN: Excluir uma porta de entrada: PowerShell'
description: Elimine um gateway de rede virtual utilizando o PowerShell no modelo de implementação do Gestor de Recursos.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 09/03/2020
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 48e0998719ea19c0b360b50d8c9171d2b448ad19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440767"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Excluir um gateway de rede virtual usando o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (clássico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Existem algumas abordagens diferentes que pode tomar quando pretende eliminar um gateway de rede virtual para uma configuração de gateway VPN.

- Se pretender apagar tudo e começar de novo, como no caso de um ambiente de teste, pode eliminar o grupo de recursos. Quando elimina um grupo de recursos, elimina todos os recursos dentro do grupo. Este é o método só é recomendado se não quiser manter nenhum dos recursos no grupo de recursos. Não é possível eliminar seletivamente apenas alguns recursos usando esta abordagem.

- Se quiser manter alguns dos recursos no seu grupo de recursos, eliminar um gateway de rede virtual torna-se um pouco mais complicado. Antes de poder apagar o gateway de rede virtual, tem primeiro de eliminar quaisquer recursos que dependam do gateway. Os passos que segue dependem do tipo de ligações que criou e dos recursos dependentes para cada ligação.

## <a name="before-beginning"></a>Antes de começar



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Descarregue os mais recentes cmdlets powerShell do Gestor de Recursos Azure.

Descarregue e instale a versão mais recente do Azure Resource Manager PowerShell cmdlets. Para obter mais informações sobre o download e instalação de cmdlets PowerShell, consulte [Como instalar e configurar a Azure PowerShell](/powershell/azure/).

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

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Excluir uma porta VPN site-to-site

Para eliminar um gateway de rede virtual para uma configuração S2S, tem primeiro de eliminar cada recurso que se refere ao portal de rede virtual. Os recursos devem ser suprimidos numa determinada ordem devido às dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome VNet: VNet1<br>
Nome do Grupo de Recursos: RG1<br>
Nome de gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação do Gestor de Recursos.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o portal de rede virtual que pretende eliminar.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o gateway de rede virtual tem alguma ligação.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Eliminar todas as ligações.

Pode ser solicitado que confirme a eliminação de cada uma das ligações.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Apagar o portal de rede virtual.

Pode ser solicitado para confirmar a eliminação do portal. Se tiver uma configuração P2S para este VNet para além da sua configuração S2S, a eliminação do gateway de rede virtual desligará automaticamente todos os clientes P2S sem aviso prévio.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste momento, o seu portal de rede virtual foi apagado. Pode utilizar os próximos passos para eliminar quaisquer recursos que já não estejam a ser utilizados.

### <a name="5-delete-the-local-network-gateways"></a>5 Apague as portas de rede locais.

Obtenha a lista dos respetivos gateways de rede locais.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Apague os gateways de rede locais. Pode ser solicitado que confirme a eliminação de cada uma das portas de rede locais.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Eliminar os recursos de endereço IP público.

Obtenha as configurações IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obtenha a lista de recursos de endereços IP públicos usados para este gateway de rede virtual. Se o gateway de rede virtual estiver ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Apagar os recursos ip públicos.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Elimine a sub-rede do gateway e desloe a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Apagar uma porta de VPN VNet-to-VNet

Para eliminar um gateway de rede virtual para uma configuração V2V, tem primeiro de eliminar cada recurso que se refere ao portal de rede virtual. Os recursos devem ser suprimidos numa determinada ordem devido às dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome VNet: VNet1<br>
Nome do Grupo de Recursos: RG1<br>
Nome de gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação do Gestor de Recursos.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o portal de rede virtual que pretende eliminar.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o gateway de rede virtual tem alguma ligação.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Pode haver outras ligações ao gateway de rede virtual que fazem parte de um grupo de recursos diferente. Verifique se há ligações adicionais em cada grupo de recursos adicionais. Neste exemplo, estamos a verificar as ligações do RG2. Execute isto para cada grupo de recursos que tenha, que pode ter uma ligação com o gateway de rede virtual.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Obtenha a lista de ligações em ambas as direções.

Como esta é uma configuração VNet-vNet, precisa da lista de ligações em ambas as direções.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Neste exemplo, estamos a verificar as ligações do RG2. Execute isto para cada grupo de recursos que tenha, que pode ter uma ligação com o gateway de rede virtual.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Eliminar todas as ligações.

Pode ser solicitado que confirme a eliminação de cada uma das ligações.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Apagar o portal de rede virtual.

Pode ser solicitado que confirme a eliminação do gateway de rede virtual. Se tiver configurações P2S para os seus VNets para além da sua configuração V2V, a eliminação dos gateways de rede virtuais desligará automaticamente todos os clientes P2S sem aviso prévio.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste momento, o seu portal de rede virtual foi apagado. Pode utilizar os próximos passos para eliminar quaisquer recursos que já não estejam a ser utilizados.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Eliminar os recursos de endereços IP públicos

Obtenha as configurações IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obtenha a lista de recursos de endereços IP públicos usados para este gateway de rede virtual. Se o gateway de rede virtual estiver ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Apagar os recursos ip públicos. Pode ser solicitado que confirme a supressão do IP Público.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Elimine a sub-rede do gateway e desloe a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Apagar uma porta VPN ponto-a-local

Para eliminar um gateway de rede virtual para uma configuração P2S, tem primeiro de eliminar cada recurso que se refere ao portal de rede virtual. Os recursos devem ser suprimidos numa determinada ordem devido às dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser especificados, enquanto outros valores são um resultado de saída. Utilizamos os seguintes valores específicos nos exemplos para fins de demonstração:

Nome VNet: VNet1<br>
Nome do Grupo de Recursos: RG1<br>
Nome de gateway de rede virtual: GW1<br>

Os seguintes passos aplicam-se ao modelo de implementação do Gestor de Recursos.


>[!NOTE]
> Quando eliminar o gateway VPN, todos os clientes ligados serão desligados do VNet sem aviso prévio.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o portal de rede virtual que pretende eliminar.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Eliminar o portal de rede virtual.

Pode ser solicitado que confirme a eliminação do gateway de rede virtual.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Neste momento, o seu portal de rede virtual foi apagado. Pode utilizar os próximos passos para eliminar quaisquer recursos que já não estejam a ser utilizados.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Eliminar os recursos de endereços IP públicos

Obtenha as configurações IP do gateway de rede virtual.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Obtenha a lista de endereços IP públicos usados para este gateway de rede virtual. Se o gateway de rede virtual estiver ativo, verá dois endereços IP públicos.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Apague os IPs públicos. Pode ser solicitado que confirme a supressão do IP Público.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Elimine a sub-rede do gateway e desloe a configuração.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>Eliminar uma porta de entrada VPN eliminando o grupo de recursos

Se não está preocupado em manter nenhum dos seus recursos no grupo de recursos e apenas quiser recomeçar, pode eliminar um grupo inteiro de recursos. Esta é uma maneira rápida de remover tudo. Os seguintes passos aplicam-se apenas ao modelo de implementação do Gestor de Recursos.

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

Quando a lista for devolvida, reveja-a para verificar se pretende eliminar todos os recursos do grupo de recursos, bem como o próprio grupo de recursos. Se pretender manter alguns dos recursos no grupo de recursos, utilize os passos nas secções anteriores deste artigo para apagar o seu gateway.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Eliminar o grupo de recursos e os recursos.

Para eliminar o grupo de recursos e todo o recurso contido no grupo de recursos, modifique o exemplo e corra.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Verifique o estado.

Leva algum tempo para o Azure apagar todos os recursos. Pode verificar o estado do seu grupo de recursos utilizando este cmdlet.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

O resultado que é devolvido mostra 'Sucesso'.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
