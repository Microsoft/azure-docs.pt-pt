---
title: 'Tutorial: Implementar e configurar o Azure Firewall numa rede híbrida com o Azure PowerShell'
description: Neste tutorial, saiba como implementar e configurar a Firewall do Azure com o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 1/30/2019
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 3a1edde2f51abbe60370eefee1b0c141f772c547
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405467"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Tutorial: Implementar e configurar o Azure Firewall numa rede híbrida com o Azure PowerShell

Quando se liga a sua rede no local a uma rede virtual do Azure para criar uma rede híbrida, a capacidade para controlar o acesso aos seus recursos de rede do Azure é uma parte importante de um plano de segurança geral.

Pode utilizar a Firewall do Azure para controlar o acesso de rede numa rede híbrida através de regras que definem permitidos e não o tráfego de rede.

Para este tutorial, crie três redes virtuais:

- **VNet Hub** -a firewall está nesta rede virtual.
- **VNet-and-Spoke** -a rede virtual do spoke representa a carga de trabalho localizada no Azure.
- **VNet Onprem** -a rede virtual no local representa uma rede no local. Numa implementação real, ele pode ser conectado por conexão de uma VPN ou ExpressRoute. Para simplificar, este tutorial utiliza uma ligação de gateway VPN e uma rede virtual do Azure-localizado é utilizada para representar uma rede no local.

![Firewall numa rede híbrida](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Declarar as variáveis
> * Criar a rede virtual do concentrador de firewall
> * Criar a rede virtual do spoke
> * Criar a rede virtual no local
> * Configurar e implementar a firewall
> * Criar e ligar os gateways de VPN
> * Configurar o peering entre o hub- and -spoke redes virtuais
> * Criar as rotas
> * Criar as máquinas virtuais
> * Testar a firewall

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer que execute o PowerShell localmente. Tem de ter versão 6.12.0 do módulo Azure PowerShell ou posterior instalado. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Depois de verificar a versão do PowerShell, execute `Login-AzureRmAccount` para criar uma ligação ao Azure.

Existem três requisitos chave para este cenário funcionar corretamente:

- Um utilizador rota definida (URD) na sub-rede spoke que aponta para o endereço IP do Firewall do Azure como o gateway predefinido. A propagação de rotas BGP tem de ser **Desativada** nesta tabela de rotas.
- Um UDR na sub-rede de gateway do hub tem de apontar para o endereço IP de firewall como salto seguinte para as redes de spoke.
- Não é necessária nenhuma UDR a sub-rede de Firewall do Azure, à medida que aprende as rotas do BGP.
- Certifique-se de que define **AllowGatewayTransit** no peering de VNet-Hub para VNet-Spoke e que utiliza **UseRemoteGateways** no peering de VNet-Spoke para VNet-Hub.

Consulte a secção de criar rotas neste tutorial para ver como estas rotas são criadas.

>[!NOTE]
>Firewall do Azure tem de ter conectividade à internet direta. Se tiver ativado o túnel forçado para o local através do ExpressRoute ou o Gateway de aplicação, terá de configurar 0.0.0.0/0 UDR com o **NextHopType** valor definido como **Internet**e, em seguida, atribua-o para  **AzureFirewallSubnet**.

>[!NOTE]
>Tráfego entre VNets diretamente em modo de peering é encaminhado diretamente, mesmo se um UDR aponta para o Firewall do Azure como o gateway predefinido. Para enviar tráfego de sub-rede para sub-rede para o firewall neste cenário, um UDR tem de conter o prefixo de rede de sub-rede de destino explicitamente em ambas as sub-redes.

Para rever a documentação de referência relacionada do Azure PowerShell, consulte [referência do PowerShell do Azure](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="declare-the-variables"></a>Declarar as variáveis

O exemplo seguinte declara as variáveis com os valores deste tutorial. Em alguns casos, poderá ter de substituir alguns valores pelos seus próprios trabalhar na sua subscrição. Modifique as variáveis, se necessário, e, em seguida, copie e cole-as na consola do PowerShell.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>Criar a rede virtual do concentrador de firewall

Primeiro, crie o grupo de recursos que contém os recursos para este tutorial:

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

Defina as sub-redes a serem incluídos na rede virtual:

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Agora, crie a rede virtual do concentrador de firewall:

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Pedir um endereço IP público a ser alocada para o gateway de VPN, que irá criar rede virtual. Tenha em atenção que o *AllocationMethod* é **Dinâmico**. Não pode especificar o endereço IP que pretende utilizar. É atribuído dinamicamente ao seu gateway de VPN. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual do spoke

Defina as sub-redes a serem incluídos na rede virtual spoke:

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Crie a rede virtual do spoke:

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual no local

Defina as sub-redes a serem incluídos na rede virtual:

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Agora, crie a rede virtual no local:

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Pedir um endereço IP público a ser alocada para o gateway que irá criar para a rede virtual. Tenha em atenção que o *AllocationMethod* é **Dinâmico**. Não pode especificar o endereço IP que pretende utilizar. É atribuído dinamicamente ao seu gateway. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>Configurar e implementar a firewall

Implemente agora a firewall na rede virtual do concentrador.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Configurar regras de rede

<!--- $Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>Criar e ligar os gateways de VPN

As redes virtuais hub e no local ligadas através de gateways de VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Criar um gateway VPN para rede virtual do concentrador

Crie a configuração do gateway de VPN. A configuração do gateway de VPN define a sub-rede e o endereço IP público a utilizar.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Agora, crie o gateway VPN para rede virtual do concentrador. Configurações de rede de rede requerem um RouteBased VpnType. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Criar um gateway VPN para a rede virtual no local

Crie a configuração do gateway de VPN. A configuração do gateway de VPN define a sub-rede e o endereço IP público a utilizar.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Agora, crie o gateway de VPN para a rede virtual no local. Configurações de rede de rede requerem um RouteBased VpnType. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>Criar as ligações VPN

Agora, pode criar as ligações de VPN entre os gateways de hub e no local

#### <a name="get-the-vpn-gateways"></a>Obter os gateways de VPN

```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Criar as ligações

Neste passo, vai criar a ligação de rede virtual do concentrador à rede virtual no local. Nos exemplos, verá uma chave partilhada referenciada. Pode utilizar os seus próprios valores para a chave partilhada. Importante: a chave partilhada tem de corresponder a ambas as ligações. A criação de uma ligação pode demorar algum tempo.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Crie o local para a ligação do hub de rede virtual. Este passo é semelhante ao anterior, exceto a criar a ligação a partir do local de VNet para VNet hub. Verifique se as chaves partilhadas correspondem. Após alguns minutos, estará ligado.

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>Verificar a ligação

Pode verificar se a ligação teve êxito com o cmdlet *Get-AzureRmVirtualNetworkGatewayConnection*, com ou sem *-Debug*. Utilize o seguinte exemplo de cmdlet, configurando os valores para corresponder aos seus. Se lhe for pedido, selecione **A** para executar **Todos**. No exemplo,  *-Name* refere-se ao nome da ligação que pretende testar.

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Quando o cmdlet terminar, veja os valores. No exemplo seguinte, o estado da ligação é apresentado como *Ligado* e pode ver os bytes de entrada e de saída.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Configurar o peering entre o hub- and -spoke redes virtuais

Agora configurar o peering entre o hub e spoke redes virtuais.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Criar as rotas

Em seguida, crie duas rotas:

- Uma rota da sub-rede de gateway do hub para a sub-rede spoke através do endereço IP da firewall
- Uma rota predefinida da sub-rede spoke através do endereço IP da firewall

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie o spoke máquinas de virtuais de carga de trabalho e no local e colocá-los nas sub-redes apropriadas.

### <a name="create-the-workload-virtual-machine"></a>Criar a máquina virtual de carga de trabalho

Criar uma máquina virtual na rede virtual spoke, executando o IIS, com nenhum endereço IP público e permite que os pings no.
Quando lhe for pedido, escreva um nome de utilizador e palavra-passe para a máquina virtual.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>Criar a máquina de virtual no local

Esta é uma máquina virtual simples que utiliza para ligar através do ambiente de trabalho remoto para o endereço IP público. A partir daí, em seguida, ligar para o servidor no local através da firewall. Quando lhe for pedido, escreva um nome de utilizador e palavra-passe para a máquina virtual.

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Testar a firewall

Em primeiro lugar, obtenha e, em seguida, tenha em atenção o endereço IP privado para **VM-spoke-01** máquina virtual.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

No portal do Azure, ligue à máquina virtual **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Abra um browser no **VM Onprem**e navegue para http://\<IP privado da VM-spoke-01\>.

Deverá ver a página predefinida dos Serviços de Informação Internet.

Na **VM Onprem**, abra um ambiente de trabalho remoto para a **VM-spoke-01** no endereço IP privado.

A ligação deve ser bem-sucedida e deverá conseguir iniciar sessão com o nome de utilizador e a palavra-passe escolhidos.

Verificou que as regras de firewall estão a funcionar:

<!---- You can ping the server on the spoke VNet.--->
- Pode procurar o servidor web na rede virtual spoke.
- Pode ligar ao servidor na rede virtual spoke através de RDP.

Em seguida, altere a ação das coleções de regras de rede da firewall para **Negar**, para verificar se as regras de firewall funcionam conforme esperado. Execute o script seguinte para alterar a ação da coleção de regras para **Negar**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```

Agora execute os testes novamente. Desta vez, devem falhar todos. Feche quaisquer ambientes de trabalho remotos existentes antes de testar as regras alteradas.

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos, elimine o grupo de recursos **FW-Hybrid-Test** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Passos Seguintes

Em seguida, pode monitorizar os registos do Azure Firewall.

> [!div class="nextstepaction"]
> [Tutorial: Monitorize registos de Firewall do Azure](./tutorial-diagnostics.md)
