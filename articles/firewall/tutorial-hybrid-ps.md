---
title: Implementar e configurar o Azure Firewall numa rede híbrida com o Azure PowerShell
description: Neste artigo, você aprenderá a implantar e configurar o Firewall do Azure usando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 10/18/2019
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: a5a008a795b88dbcb72ed9fba869e5251fd93567
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163529"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Implementar e configurar o Azure Firewall numa rede híbrida com o Azure PowerShell

Quando você conecta sua rede local a uma rede virtual do Azure para criar uma rede híbrida, a capacidade de controlar o acesso aos recursos de rede do Azure é uma parte importante de um plano de segurança geral.

Você pode usar o Firewall do Azure para controlar o acesso à rede em uma rede híbrida usando regras que definem o tráfego de rede permitido e negado.

Para este artigo, você cria três redes virtuais:

- **VNet-Hub** -o firewall está nessa rede virtual.
- **VNet-spoke** -a rede virtual spoke representa a carga de trabalho localizada no Azure.
- **VNet-** local – a rede virtual local representa uma rede local. Em uma implantação real, ele pode ser conectado por uma conexão VPN ou ExpressRoute. Para simplificar, este artigo usa uma conexão de gateway de VPN e uma rede virtual localizada no Azure é usada para representar uma rede local.

![Firewall numa rede híbrida](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Declarar as variáveis
> * Criar a rede virtual do hub de firewall
> * Criar a rede virtual do spoke
> * Criar a rede virtual local
> * Configurar e implementar a firewall
> * Criar e ligar os gateways de VPN
> * Emparelhar as redes virtuais Hub e spoke
> * Criar as rotas
> * Criar as máquinas virtuais
> * Testar a firewall

Se você quiser usar portal do Azure em vez de concluir este tutorial, consulte [tutorial: implantar e configurar o Firewall do Azure em uma rede híbrida usando o portal do Azure](tutorial-hybrid-portal.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo requer que você execute o PowerShell localmente. Você deve ter o módulo Azure PowerShell instalado. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](https://docs.microsoft.com/powershell/azure/install-Az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma ligação ao Azure.

Existem três requisitos chave para este cenário funcionar corretamente:

- Uma rota definida pelo usuário (UDR) na sub-rede do spoke que aponta para o endereço IP do firewall do Azure como o gateway padrão. A propagação de rotas BGP tem de ser **Desativada** nesta tabela de rotas.
- Um UDR na sub-rede de gateway de Hub deve apontar para o endereço IP do firewall como o próximo salto para as redes spoke.

   Nenhum UDR é necessário na sub-rede do firewall do Azure, pois ele aprende as rotas do BGP.
- Certifique-se de que define **AllowGatewayTransit** no peering de VNet-Hub para VNet-Spoke e que utiliza **UseRemoteGateways** no peering de VNet-Spoke para VNet-Hub.

Consulte a seção [criar rotas](#create-the-routes) neste artigo para ver como essas rotas são criadas.

>[!NOTE]
>O Firewall do Azure deve ter conectividade direta com a Internet. Se o seu AzureFirewallSubnet aprende uma rota padrão para sua rede local via BGP, você deve substituí-lo por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet.
>
>No momento, o Firewall do Azure não dá suporte a túnel forçado. Se sua configuração requer o túnel forçado para uma rede local e você pode determinar os prefixos de IP de destino para seus destinos de Internet, você pode configurar esses intervalos com a rede local como o próximo salto por meio de uma rota definida pelo usuário no AzureFirewallSubnet. Ou, você pode usar o BGP para definir essas rotas.

>[!NOTE]
>O tráfego entre VNets emparelhadas diretamente é roteado diretamente mesmo se um UDR aponta para o Firewall do Azure como o gateway padrão. Para enviar a sub-rede ao tráfego de sub-rede para o firewall nesse cenário, um UDR deve conter o prefixo de rede de sub-rede de destino explicitamente em ambas as sub-redes.

Para examinar a documentação relacionada Azure PowerShell referência, consulte [Azure PowerShell referência](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="declare-the-variables"></a>Declarar as variáveis

O exemplo a seguir declara as variáveis usando os valores deste artigo. Em alguns casos, talvez seja necessário substituir alguns valores pelos seus próprios para trabalhar em sua assinatura. Modifique as variáveis, se necessário, e, em seguida, copie e cole-as na consola do PowerShell.

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


## <a name="create-the-firewall-hub-virtual-network"></a>Criar a rede virtual do hub de firewall

Primeiro, crie o grupo de recursos para conter os recursos deste artigo:

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

Defina as sub-redes a serem incluídas na rede virtual:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Agora, crie a rede virtual do hub de firewall:

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Solicite um endereço IP público a ser alocado para o gateway de VPN que você criará para sua rede virtual. Tenha em atenção que o *AllocationMethod* é **Dinâmico**. Não é possível especificar o endereço IP que pretende utilizar. É atribuído dinamicamente ao seu gateway de VPN.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual do spoke

Defina as sub-redes a serem incluídas na rede virtual do spoke:

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Criar a rede virtual spoke:

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual local

Defina as sub-redes a serem incluídas na rede virtual:

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Agora, crie a rede virtual local:

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Solicite um endereço IP público a ser alocado para o gateway que você criará para a rede virtual. Tenha em atenção que o *AllocationMethod* é **Dinâmico**. Não é possível especificar o endereço IP que pretende utilizar. É atribuído dinamicamente ao seu gateway.

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>Configurar e implementar a firewall

Agora, implante o firewall na rede virtual do Hub.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Configurar regras de rede

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>Criar e ligar os gateways de VPN

As redes virtuais de Hub e locais são conectadas por meio de gateways de VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Criar um gateway de VPN para a rede virtual do Hub

Crie a configuração do gateway de VPN. A configuração do gateway de VPN define a sub-rede e o endereço IP público a utilizar.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Agora, crie o gateway de VPN para a rede virtual do Hub. As configurações de rede para rede exigem um VpnType RouteBased. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Criar um gateway de VPN para a rede virtual local

Crie a configuração do gateway de VPN. A configuração do gateway de VPN define a sub-rede e o endereço IP público a utilizar.

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Agora, crie o gateway de VPN para a rede virtual local. As configurações de rede para rede exigem um VpnType RouteBased. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>Criar as ligações VPN

Agora você pode criar as conexões VPN entre o Hub e os gateways locais

#### <a name="get-the-vpn-gateways"></a>Obter os gateways de VPN

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Criar as ligações

Nesta etapa, você cria a conexão da rede virtual do hub para a rede virtual local. Nos exemplos, verá uma chave partilhada referenciada. Pode utilizar os seus próprios valores para a chave partilhada. Importante: a chave partilhada tem de corresponder a ambas as ligações. A criação de uma ligação pode demorar algum tempo.

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Crie a conexão de rede virtual local para o Hub. Esta etapa é semelhante à anterior, exceto que você cria a conexão de VNet-local para VNet-Hub. Verifique se as chaves partilhadas correspondem. Após alguns minutos, estará ligado.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>Verificar a ligação

Você pode verificar uma conexão bem-sucedida usando o cmdlet *Get-AzVirtualNetworkGatewayConnection* , com ou sem *debug*. Utilize o seguinte exemplo de cmdlet, configurando os valores para corresponder aos seus. Se lhe for pedido, selecione **A** para executar **Todos**. No exemplo,  *-Name* refere-se ao nome da ligação que pretende testar.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Quando o cmdlet terminar, veja os valores. No exemplo seguinte, o estado da ligação é apresentado como *Ligado* e pode ver os bytes de entrada e de saída.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Emparelhar as redes virtuais Hub e spoke

Agora, emparelhar as redes virtuais Hub e spoke.

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Criar as rotas

Em seguida, crie duas rotas:

- Uma rota da sub-rede de gateway do hub para a sub-rede spoke através do endereço IP da firewall
- Uma rota predefinida da sub-rede spoke através do endereço IP da firewall

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora, crie a carga de trabalho do spoke e as máquinas virtuais locais e coloque-as nas sub-redes apropriadas.

### <a name="create-the-workload-virtual-machine"></a>Criar a máquina virtual de carga de trabalho

Crie uma máquina virtual na rede virtual do spoke, executando o IIS, sem endereço IP público e permita pings no.
Quando lhe for pedido, escreva um nome de utilizador e palavra-passe para a máquina virtual.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
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
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>Criar a máquina virtual local

Essa é uma máquina virtual simples que você usa para se conectar usando Área de Trabalho Remota para o endereço IP público. A partir daí, você se conectará ao servidor local por meio do firewall. Quando lhe for pedido, escreva um nome de utilizador e palavra-passe para a máquina virtual.

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Testar a firewall

Primeiro, obtenha e anote o endereço IP privado para a máquina virtual **VM-spoke-01** .

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

No portal do Azure, ligue à máquina virtual **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Abra um navegador da Web em **VM-local**e navegue até http://\<VM-spoke-01 IP privado \>.

Deverá ver a página predefinida dos Serviços de Informação Internet.

Na **VM Onprem**, abra um ambiente de trabalho remoto para a **VM-spoke-01** no endereço IP privado.

A ligação deve ser bem-sucedida e deverá conseguir iniciar sessão com o nome de utilizador e a palavra-passe escolhidos.

Agora você verificou que as regras de firewall estão funcionando:

<!---- You can ping the server on the spoke VNet.--->
- Você pode procurar o servidor Web na rede virtual do spoke.
- Você pode se conectar ao servidor na rede virtual do spoke usando o RDP.

Em seguida, altere a ação das coleções de regras de rede da firewall para **Negar**, para verificar se as regras de firewall funcionam conforme esperado. Execute o script seguinte para alterar a ação da coleção de regras para **Negar**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

Agora execute os testes novamente. Desta vez, devem falhar todos. Feche quaisquer ambientes de trabalho remotos existentes antes de testar as regras alteradas.

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos, elimine o grupo de recursos **FW-Hybrid-Test** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode monitorizar os registos do Azure Firewall.

[Tutorial: monitorizar registos do Azure Firewall](./tutorial-diagnostics.md)
