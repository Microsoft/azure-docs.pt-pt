---
title: 'Quickstart: Criar um equilibrador de carga interno - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Este quickstart mostra como criar um equilibrador de carga interno usando Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 5844b321fd3050bab9288657189ddcd87bba14db
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562340"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Quickstart: Criar um equilibrador de carga interno para carregar VMs de equilíbrio usando Azure PowerShell

Começa com o Azure Load Balancer utilizando o Azure PowerShell para criar um equilibrador de carga interno e duas máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell instalado localmente ou Azure Cloud Shell

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreateIntLBQS-rg' -Location 'eastus'
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

Nesta secção, cria-se um equilibrador de carga que equilibra as máquinas virtuais. 

Quando cria um equilibrador de carga interno, uma rede virtual é configurada como a rede para o equilibrador de carga. 

O diagrama a seguir mostra os recursos criados neste arranque rápido:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Recursos padrão do balanceador de carga criados para o arranque rápido." border="false":::

## <a name="configure-virtual-network---standard"></a>Configure rede virtual - Standard

Antes de implementar VMs e testar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

Crie uma rede virtual para as máquinas virtuais backend.

Crie um grupo de segurança de rede para definir ligações de entrada à sua rede virtual.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Criar rede virtual, grupo de segurança de rede e hospedeiro de bastião

* Criar uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

* Criar uma regra de grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

* Crie um anfitrião Azure Bastion com [New-AzBastion](/powershell/module/az.network/new-azbastion).

* Criar um grupo de segurança de rede com [o New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```
## <a name="create-standard-load-balancer"></a>Criar balanceador de carga padrão

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:

* Crie um IP frontal com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) para o pool IP frontend. Este IP recebe o tráfego de entrada no equilibrador de carga

* Crie um pool de endereços back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) para o tráfego enviado a partir do frontend do equilibrador de carga. Esta piscina é onde as máquinas virtuais de backend são implantadas.

* Crie uma sonda de saúde com [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) que determine a saúde dos casos de VM backend.

* Crie uma regra do balançador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) que define como o tráfego é distribuído para os VMs.

* Criar um equilibrador de carga pública com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-virtual-machines---standard"></a>Criar máquinas virtuais - Standard

Nesta secção, irá criar as três máquinas virtuais para o pool de backend do equilibrador de carga.

* Criar três interfaces de rede com [o New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Desconfime de utilizador e palavra-passe para os VMs com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

* Crie as máquinas virtuais com:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Sistema operativo Set-AzVM](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreateIntLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreateIntLBQS-rg'

## For loop with variable to create virtual machines for load balancer backend pool. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
    Zone = "$i"
}
New-AzVM @vm -AsJob
}

```

As colocações das máquinas virtuais e do hospedeiro de bastião são submetidas como trabalhos powerShell. Para visualizar o estado dos trabalhos, use [o Get-Job](/powershell/module/microsoft.powershell.core/get-job):

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

Nesta secção, cria-se um equilibrador de carga que equilibra as máquinas virtuais. 

Quando cria um equilibrador de carga interno, uma rede virtual é configurada como a rede para o equilibrador de carga. 

O diagrama a seguir mostra os recursos criados neste arranque rápido:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Recursos básicos do balanceador de carga criados em arranque rápido." border="false":::

## <a name="configure-virtual-network---basic"></a>Configure rede virtual - Básico

Antes de implementar VMs e testar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

Crie uma rede virtual para as máquinas virtuais backend.

Crie um grupo de segurança de rede para definir ligações de entrada à sua rede virtual.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Criar rede virtual, grupo de segurança de rede e hospedeiro de bastião

* Criar uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

* Criar uma regra de grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

* Crie um anfitrião Azure Bastion com [New-AzBastion](/powershell/module/az.network/new-azbastion).

* Criar um grupo de segurança de rede com [o New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```
## <a name="create-basic-load-balancer"></a>Criar balanceador de carga básico

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:

* Crie um IP frontal com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) para o pool IP frontend. Este IP recebe o tráfego de entrada no equilibrador de carga

* Crie um pool de endereços back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) para o tráfego enviado a partir do frontend do equilibrador de carga. Esta piscina é onde as máquinas virtuais de backend são implantadas.

* Crie uma sonda de saúde com [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) que determine a saúde dos casos de VM backend.

* Crie uma regra do balançador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) que define como o tráfego é distribuído para os VMs.

* Criar um equilibrador de carga pública com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Basic'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-virtual-machines---basic"></a>Criar máquinas virtuais - Básico

Nesta secção, irá criar as máquinas virtuais para o pool de backend do equilibrador de carga.

* Criar três interfaces de rede com [o New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Desconfime de utilizador e palavra-passe para os VMs com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

* Utilize [o New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) para criar um conjunto de disponibilidade para as máquinas virtuais.

* Crie as máquinas virtuais com:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Sistema operativo Set-AzVM](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreateIntLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreateIntLBQS-rg'

## Create availability set for the virtual machines. ##
$set = @{
    Name = 'myAvailabilitySet'
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Sku = 'Aligned'
    PlatformFaultDomainCount = '2'
    PlatformUpdateDomainCount =  '2'
}
$avs = New-AzAvailabilitySet @set

## For loop with variable to create virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'
    AvailabilitySetId = $avs.Id   
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
}
New-AzVM @vm -AsJob
}

```

As colocações das máquinas virtuais e do hospedeiro de bastião são submetidas como trabalhos powerShell. Para visualizar o estado dos trabalhos, use [o Get-Job](/powershell/module/microsoft.powershell.core/get-job):

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

---

## <a name="install-iis"></a>Instalar o IIS

Utilize [a Configuração AzVMExtension](/powershell/module/az.compute/set-azvmextension) para instalar a extensão de script personalizada. 

A extensão executa o `PowerShell Add-WindowsFeature Web-Server` para instalar o servidor Web IIS e, em seguida, atualiza a página Default.htm para mostrar o nome do anfitrião da VM:

> [!IMPORTANT]
> Certifique-se de que as instalações da máquina virtual foram concluídas a partir dos passos anteriores antes de prosseguir.  Utilize `Get-Job` para verificar o estado dos trabalhos de implantação de máquinas virtuais.

```azurepowershell-interactive
## For loop with variable to install custom script extension on virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
$ext = @{
    Publisher = 'Microsoft.Compute'
    ExtensionType = 'CustomScriptExtension'
    ExtensionName = 'IIS'
    ResourceGroupName = 'CreateIntLBQS-rg'
    VMName = "myVM$i"
    Location = 'eastus'
    TypeHandlerVersion = '1.8'
    SettingString = '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
}
Set-AzVMExtension @ext -AsJob
}
```

As extensões são implementadas como trabalhos powerShell. Para visualizar o estado dos trabalhos de instalação, utilize [o Get-Job](/powershell/module/microsoft.powershell.core/get-job):


```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
8      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
9      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
10     Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
```


## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

### <a name="create-virtual-machine"></a>Criar a máquina virtual

Crie a máquina virtual com:

* [Novo AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)
* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Sistema operativo Set-AzVM](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VM. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreateIntLBQS-rg'

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreateIntLBQS-rg'

## Command to create network interface for VM ##
$nic = @{
    Name = "myNicTestVM"
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myTestVM"
    VMSize = 'Standard_DS1_v2' 
}
$vmos = @{
    ComputerName = "myTestVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreateIntLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
}
New-AzVM @vm

```

### <a name="test"></a>Teste

1. [Inicie sessão](https://portal.azure.com) no portal do Azure.

1. Encontre o endereço IP privado para o balançador de carga no ecrã **de visão geral.** Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione **myLoadBalancer**.

2. Tome nota ou copie o endereço ao lado **do Endereço IP Privado** na **visão geral** do **myLoadBalancer**.

3. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos**, e, em seguida, da lista de recursos, selecione **myTestVM** que está localizado no grupo de recursos **CreateIntLBQS-rg.**

4. Na página **'Vista Geral',** selecione **Connect** e, em seguida, **Bastion**.

6. Introduza o nome de utilizador e a palavra-passe introduzidos durante a criação de VM.

7. Abrir **internet Explorer** no **myTestVM**.

8. Introduza o endereço IP a partir do passo anterior na barra de endereços do navegador. A página predefinida do servidor Web do IIS é apresentada no browser.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Criar um equilibrador de carga interno padrão" border="true":::
   
Para ver o balanceador de carga distribuir tráfego através dos três VMs, pode personalizar a página padrão de cada servidor Web IIS de cada VM e, em seguida, refrescar o seu navegador web a partir da máquina do cliente.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o equilibrador de carga e os recursos restantes.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateIntLBQS-rg'
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido:

* Criou um balanceador de carga interno padrão ou básico
* Máquinas virtuais anexadas. 
* Configurado a regra de tráfego do balanceador de carga e a sonda de saúde.
* Testei o equilibrador de carga.

Para saber mais sobre o Azure Load Balancer, continue a:
> [!div class="nextstepaction"]
> [What is Azure Load Balancer?](load-balancer-overview.md) (O que é o Balanceador de Carga do Azure?)