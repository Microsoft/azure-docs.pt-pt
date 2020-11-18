---
title: Implementar & configurar a Azure Firewall em rede híbrida utilizando o PowerShell
description: Neste artigo, aprende a implantar e configurar a Azure Firewall utilizando a Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 08/28/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: e60c829831bde3b454ab180d1a39ec46cb346963
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658660"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Implementar e configurar o Azure Firewall numa rede híbrida com o Azure PowerShell

Quando liga a sua rede no local a uma rede virtual Azure para criar uma rede híbrida, a capacidade de controlar o acesso aos recursos da rede Azure é uma parte importante de um plano de segurança global.

Pode utilizar o Azure Firewall para controlar o acesso à rede numa rede híbrida ao utilizar regras que definem o tráfego de rede permitido e negado.

Para este artigo, cria três redes virtuais:

- **VNet-Hub** - a firewall está nesta rede virtual.
- **VNet-Spoke** - a rede virtual falada representa a carga de trabalho localizada no Azure.
- **VNet-Onprem** - A rede virtual no local representa uma rede no local. Numa implementação real, pode ser ligada por uma ligação VPN ou ExpressRoute. Para simplificar, este artigo utiliza uma ligação de gateway VPN, e uma rede virtual localizada no Azure é usada para representar uma rede no local.

![Firewall numa rede híbrida](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Neste artigo, vai aprender a:

* Declarar as variáveis
* Crie a rede virtual do hub de firewall
* Criar a rede virtual falada
* Criar a rede virtual no local
* Configurar e implementar a firewall
* Criar e ligar os gateways de VPN
* Peer the hub e falou redes virtuais
* Criar as rotas
* Criar as máquinas virtuais
* Testar a firewall

Se pretender utilizar o portal Azure para completar este tutorial, consulte [Tutorial: Implementar e configurar a Azure Firewall numa rede híbrida utilizando o portal Azure](tutorial-hybrid-portal.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo requer que você execute PowerShell localmente. Deve ter o módulo Azure PowerShell instalado. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma ligação ao Azure.

Existem três requisitos chave para este cenário funcionar corretamente:

- Uma Rota Definida pelo Utilizador (UDR) na sub-rede falada que aponta para o endereço IP Azure Firewall como o gateway predefinido. A propagação da rota de gateway de rede virtual deve ser **desativada** nesta tabela de rotas.
- Um UDR na sub-rede do gateway do hub deve apontar para o endereço IP de firewall como o próximo salto para as redes de raios.

   Não é necessária nenhuma UDR na sub-rede Azure Firewall, uma vez que aprende rotas a partir de BGP.
- Certifique-se de que define **AllowGatewayTransit** no peering de VNet-Hub para VNet-Spoke e que utiliza **UseRemoteGateways** no peering de VNet-Spoke para VNet-Hub.

Consulte a secção [Rotas da Criação](#create-the-routes) neste artigo para ver como estas rotas são criadas.

>[!NOTE]
>O Azure Firewall tem de ter conectividade Internet direta. Se o seu AzureFirewallSubnet aprender uma rota padrão para a sua rede no local via BGP, deve sobrepor-se a isto com um UDR de 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta na Internet.
>
>A Azure Firewall pode ser configurada para suportar túneis forçados. Para mais informações, consulte [o Azure Firewall forjando túneis.](forced-tunneling.md)

>[!NOTE]
>O tráfego entre VNets diretamente espreitados é encaminhado diretamente mesmo que um UDR aponte para Azure Firewall como o gateway padrão. Para enviar o tráfego de sub-rede para a firewall neste cenário, um UDR deve conter explicitamente o prefixo da rede de sub-redes alvo em ambas as sub-redes.

Para rever a documentação de referência Azure PowerShell, consulte [a referência Azure PowerShell](/powershell/module/az.network/new-azfirewall).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="declare-the-variables"></a>Declarar as variáveis

O exemplo a seguir declara as variáveis utilizando os valores para este artigo. Em alguns casos, poderá ter de substituir alguns valores por outros para trabalhar na sua subscrição. Modifique as variáveis, se necessário, e, em seguida, copie e cole-as na consola do PowerShell.

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


## <a name="create-the-firewall-hub-virtual-network"></a>Crie a rede virtual do hub de firewall

Em primeiro lugar, criar o grupo de recursos para conter os recursos para este artigo:

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

Definir as sub-redes a incluir na rede virtual:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Agora, crie a rede virtual do hub de firewall:

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Solicite um endereço IP público para ser alocado ao gateway VPN que irá criar para a sua rede virtual. Note que o *AlocaçãoMethod* é **Dinâmico.** Não é possível especificar o endereço IP que pretende utilizar. É atribuído dinamicamente ao seu gateway de VPN.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual falada

Definir as sub-redes a incluir na rede virtual falada:

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Criar a rede virtual falada:

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual no local

Definir as sub-redes a incluir na rede virtual:

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Agora, crie a rede virtual no local:

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Solicite um endereço IP público para ser alocado ao portal que irá criar para a rede virtual. Note que o *AlocaçãoMethod* é **Dinâmico.** Não é possível especificar o endereço IP que pretende utilizar. É atribuído dinamicamente ao seu gateway.

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>Configurar e implementar a firewall

Agora, insi(implantado a firewall na rede virtual do hub.

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

O hub e as redes virtuais no local estão conectados através de gateways VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Criar uma porta de entrada VPN para a rede virtual do hub

Crie a configuração do gateway de VPN. A configuração do gateway de VPN define a sub-rede e o endereço IP público a utilizar.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Agora crie a porta de entrada VPN para a rede virtual do hub. As configurações rede-rede requerem um VpnType de RotaBased. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Criar uma porta de entrada VPN para a rede virtual no local

Crie a configuração do gateway de VPN. A configuração do gateway de VPN define a sub-rede e o endereço IP público a utilizar.

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Agora crie a porta de entrada VPN para a rede virtual no local. As configurações rede-rede requerem um VpnType de RotaBased. Criar um gateway de VPN, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway de VPN selecionado.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>Criar as ligações VPN

Agora pode criar as ligações VPN entre o hub e os gateways no local

#### <a name="get-the-vpn-gateways"></a>Obter os gateways de VPN

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Criar as ligações

Neste passo, cria-se a ligação da rede virtual do hub à rede virtual no local. Nos exemplos, verá uma chave partilhada referenciada. Pode utilizar os seus próprios valores para a chave partilhada. Importante: a chave partilhada tem de corresponder a ambas as ligações. A criação de uma ligação pode demorar algum tempo.

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Crie as instalações para hub ligação de rede virtual. Este passo é semelhante ao anterior, exceto que cria a ligação de VNet-Onprem ao VNet-hub. Verifique se as chaves partilhadas correspondem. Após alguns minutos, estará ligado.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>Verificar a ligação

Pode verificar uma ligação bem sucedida utilizando o cmdlet *Get-AzVirtualNetworkGatewayConnection,* com ou sem *-Debug*. Utilize o seguinte exemplo de cmdlet, configurando os valores para corresponder aos seus. Se lhe for pedido, selecione **A** para executar **Todos**. No exemplo, *-O nome* refere-se ao nome da ligação que pretende testar.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Quando o cmdlet terminar, veja os valores. No exemplo seguinte, o estado da ligação é apresentado como *Ligado* e pode ver os bytes de entrada e de saída.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Peer the hub e falou redes virtuais

Agora, espreita o centro e fala redes virtuais.

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

#Create a table, with BGP route propagation disabled. The property is now called "Virtual network gateway route propagation," but the API still refers to the parameter as "DisableBgpRoutePropagation."
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
  -Name "ToFirewall" `
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

Agora crie a carga de trabalho falada e as máquinas virtuais no local, e coloque-as nas sub-redes apropriadas.

### <a name="create-the-workload-virtual-machine"></a>Criar a máquina virtual de carga de trabalho

Crie uma máquina virtual na rede virtual falada, executando o IIS, sem endereço IP público, e permite entrar pings.
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

### <a name="create-the-on-premises-virtual-machine"></a>Crie a máquina virtual no local

Esta é uma máquina virtual simples que utiliza para ligar usando o Ambiente de Trabalho Remoto ao endereço IP público. A partir daí, liga-se ao servidor no local através da firewall. Quando lhe for pedido, escreva um nome de utilizador e palavra-passe para a máquina virtual.

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

Primeiro, obtenha e, em seguida, note o endereço IP privado para a máquina virtual **VM-spoke-01.**

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

No portal do Azure, ligue à máquina virtual **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Abra um navegador web em **VM-Onprem,** e navegue para http:// \<VM-spoke-01 private IP\> .

Deverá ver a página predefinida dos Serviços de Informação Internet.

Na **VM Onprem**, abra um ambiente de trabalho remoto para a **VM-spoke-01** no endereço IP privado.

A ligação deve ser bem-sucedida e deverá conseguir iniciar sessão com o nome de utilizador e a palavra-passe escolhidos.

Então agora verificaste que as regras da firewall estão a funcionar:

<!---- You can ping the server on the spoke VNet.--->
- Pode navegar no servidor web na rede virtual falada.
- Pode ligar-se ao servidor na rede virtual falada utilizando RDP.

Em seguida, altere a ação das coleções de regras de rede da firewall para **Negar**, para verificar se as regras de firewall funcionam conforme esperado. Execute o script seguinte para alterar a ação da coleção de regras para **Negar**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

Agora execute os testes novamente. Desta vez, devem falhar todos. Feche quaisquer ambientes de trabalho remotos existentes antes de testar as regras alteradas.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos, elimine o grupo de recursos **FW-Hybrid-Test** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Próximos passos

Em seguida, pode monitorizar os registos do Azure Firewall.

[Tutorial: monitorizar registos do Azure Firewall](./firewall-diagnostics.md)