---
title: 'Tutorial: Proteja o seu hub virtual usando a Azure PowerShell'
description: Este artigo descreve como criar um Azure Virtual WAN numa região com Azure Firewall ativado no centro.
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: 093b962bb9f42b660fb8cc5c0584ec67ebc87e48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789166"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>Tutorial: Proteja o seu hub virtual usando a Azure PowerShell

Neste tutorial, você cria um caso WAN virtual com um Hub Virtual numa região, e você implementa um Azure Firewall no Centro Virtual para garantir a conectividade. Neste exemplo, demonstra conectividade segura entre redes virtuais. O tráfego entre redes virtuais e os balcões site-to-site, ponto-a-local ou ExpressRoute também são suportados pelo Virtual Secure Hub.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Implementar o WAN virtual
> * Implementar firewall Azure e configurar o encaminhamento personalizado
> * Testar conectividade

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- PowerShell 7

   Este tutorial requer que você executar Azure PowerShell localmente no PowerShell 7. Para instalar o PowerShell 7, consulte [migração do Windows PowerShell 5.1 para PowerShell 7](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7).
- Az.Network versão 3.2.0

    Se tiver a versão 3.4.0 ou posterior da Az.Network, terá de reduzir para utilizar alguns dos comandos deste tutorial. Pode verificar a versão do seu módulo Az.Network com o comando `Get-InstalledModule -Name Az.Network` . Para desinstalar o módulo Az.Network, corra `Uninstall-Module -name az.network` . Para instalar o módulo Az.Network 3.2.0, corra `Install-Module az.network -RequiredVersion 3.2.0 -force` .

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>Implantação inicial de WAN virtual

Como primeiro passo, você precisa definir algumas variáveis e criar o grupo de recursos, a instância WAN virtual, e o hub virtual:

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

Crie duas redes virtuais e conecte-as ao centro como raios:

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

Neste momento, você tem um WAN virtual totalmente funcional, fornecendo qualquer conectividade para qualquer. Para melhorá-lo com segurança, você precisa colocar um Azure Firewall em cada Hub Virtual. As políticas de firewall podem ser usadas para gerir eficientemente a instância virtual wan Azure Firewall. Assim, uma política de firewall também é criada neste exemplo:

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

Permitir o registo do Azure Firewall para o Azure Monitor é opcional, mas neste exemplo utiliza os registos firewall para provar que o tráfego está a atravessar a firewall:

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Implementar firewall Azure e configurar o encaminhamento personalizado

Agora tem uma Firewall Azure no centro, mas ainda precisa de modificar o encaminhamento para que o VIRTUAL WAN envie o tráfego das redes virtuais e dos ramos através da firewall. Faça isto em dois passos:

1. Configure todas as ligações de rede virtuais (e ligações de ramificação se houver) para propagar para a `None` Tabela de Rota. O efeito desta configuração é que outras redes e ramos virtuais não aprendem os seus prefixos, pelo que não tem encaminhamento para os alcançar.
1. Agora pode inserir rotas estáticas na Tabela de `Default` Rotas (onde todas as redes e ramos virtuais estão associados por padrão), de modo a que todo o tráfego seja enviado para a Firewall Azure.

> [!NOTE]
> Esta é a configuração implementada ao garantir a conectividade do Portal Azure com o Azure Firewall Manager

Comece com o primeiro passo, para configurar as suas ligações de rede virtuais para propagar para a `None` Tabela de Rota:

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

Agora pode continuar com o segundo passo, para adicionar as rotas estáticas à `Default` tabela de rotas. Neste exemplo, aplica-se a configuração padrão que o Azure Firewall Manager geraria ao assegurar a conectividade num WAN Virtual, mas pode alterar a lista de prefixos na rota estática para se adaptar aos seus requisitos específicos:

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>Testar conectividade

Agora tem um centro totalmente operacional. Para testar a conectividade, precisa de uma máquina virtual em cada rede virtual falada ligada ao hub:

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

A configuração padrão na política de firewall é deixar cair tudo. Por isso, tens de configurar algumas regras. Comece com as regras do DNAT, para que as máquinas virtuais de teste estejam acessíveis sobre o endereço IP público da Firewall:

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

Agora pode configurar algumas regras de exemplo. Defina uma regra de rede que permite o tráfego de SSH, além de uma regra de aplicação que permite o acesso à Internet ao Nome de Domínio Totalmente `ifconfig.co` Qualificado. Este URL devolve o endereço IP de origem que vê no pedido HTTP:

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

Antes de enviar qualquer tráfego, pode inspecionar as rotas eficazes das máquinas virtuais. Devem conter os prefixos aprendidos com o WAN Virtual `0.0.0.0/0` (mais RFC1918), mas não o prefixo do outro falado:

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

Agora gere tráfego de uma Máquina Virtual para a outra, e verifique se caiu na Firewall do Azure. Nos seguintes comandos SSH é necessário aceitar as impressões digitais das máquinas virtuais e fornecer a palavra-passe que definiu quando criou as máquinas virtuais. Neste exemplo, vai enviar cinco pacotes de pedido de eco ICMP da máquina virtual em spoke1 para spoke2, além de uma tentativa de ligação TCP na porta 22 usando o utilitário Linux `nc` (com as `-vz` bandeiras apenas envia um pedido de ligação e mostra o resultado). Você deve ver o ping falhando, e a tentativa de ligação TCP na porta 22 conseguindo, uma vez que é permitido pela regra de rede que configuraste anteriormente:

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

Também pode verificar o tráfego da Internet. Os pedidos HTTP através da utilidade `curl` para a FQDN que permitiu na política de firewall `ifconfig.co` () devem funcionar, mas os pedidos HTTP para qualquer outro destino devem falhar (neste exemplo, você testa com `bing.com` ):

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

A maneira mais fácil de verificar se os pacotes são deixados pela firewall é verificar os registos. Uma vez que configura o Azure Firewall para enviar registos para o Azure Monitor, pode utilizar a Linguagem de Consulta de Kusto para recuperar os registos relevantes do Azure Monitor:

> [!NOTE]
> Pode levar cerca de 1 minuto para que os registos pareçam ser enviados para o Azure Monitor

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

No comando anterior deverá ver diferentes entradas:

* A sua ligação SSH sendo DNAT'ed
* Deixou cair os pacotes ICMP entre os VMs nos porta-vozes (10.1.1.4 e 10.1.2.4)
* Conexões SSH permitidas entre os VMs nos porta-vozes

Aqui uma amostra de saída produzida pelo comando acima:

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

Se pretender ver os registos das regras de aplicação (descrevendo ligações HTTP permitidas e negadas) ou alterar a forma como os registos são apresentados, pode tentar com outras consultas de KQL. Pode encontrar alguns exemplos nos [registos do Azure Monitor para Azure Firewall](../firewall/firewall-workbook.md).


## <a name="clean-up-resources"></a>Limpar os recursos

Para eliminar o ambiente de teste, pode remover o grupo de recursos com todos os objetos contidos:

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça os parceiros de segurança de confiança](trusted-security-partners.md)