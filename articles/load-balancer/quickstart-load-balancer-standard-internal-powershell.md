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
ms.openlocfilehash: e1fa97fb8a3dcae3a78ba0bc85cf59db2c167dea
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696783"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Quickstart: Criar um equilibrador de carga interno para carregar VMs de equilíbrio usando Azure PowerShell

Começa com o Azure Load Balancer utilizando o Azure PowerShell para criar um equilibrador de carga interno e duas máquinas virtuais.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [o New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

* Denominado **CreateIntLBQS-rg**.
* No **local leste.**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

## <a name="configure-virtual-network"></a>Configurar uma rede virtual

Antes de implementar VMs e testar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Crie uma rede virtual e anfitrião do Azure Bastion

Criar uma rede virtual com [a New-AzVirtualNetwork:](/powershell/module/az.network/new-azvirtualnetwork)

* Chamado **myVNet.**
* No grupo de recursos **CreateIntLBQS-rg.**
* Subnet chamado **myBackendSubnet**.
* Rede virtual **10.0.0.0/16**.
* Sub-rede **10.0.0.0/24**.
* Sub-rede chamada **AzureBastionSubnet**.
* Sub-rede **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Criar endereço IP público para anfitrião do Azure Bastion

Utilize [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público para o anfitrião do bastião:

* Nomeado **myPublicIPBastion**
* No grupo de recursos **CreateIntLBQS-rg.**
* No **local leste.**
* Método de atribuição **estático**.
* **Sku padrão.**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Criar anfitrião do Azure Bastion

Use [a New-AzBastion](/powershell/module/az.network/new-azbastion) para criar um hospedeiro de bastião:

* Chamado **myBastion.**
* No grupo de recursos **CreateIntLBQS-rg.**
* Na rede virtual **myVNet.**
* Associado a endereço ip público **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Pode levar alguns minutos para o anfitrião do Bastião Azure ser implantado.

### <a name="create-network-security-group"></a>Criar grupo de segurança de rede
Crie um grupo de segurança de rede para definir ligações recebidas para a sua rede virtual.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Criar uma regra de grupo de segurança de rede para a porta 80
Criar uma regra de grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig:](/powershell/module/az.network/new-aznetworksecurityruleconfig)

* Denominado **myNSGRuleHTTP**.
* Descrição do **Allow HTTP**.
* Acesso do **Allow**.
* Protocolo **(*)**.
* Direção **Entrada**.
* Prioridade **2000**.
* Fonte da **Internet.**
* Gama de porta de **origem de (*)**.
* Prefixo do endereço de destino de **(*)**.
* Porto de destino **80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Criar um grupo de segurança de rede com [o New-AzNetworkSecurityGroup:](/powershell/module/az.network/new-aznetworksecuritygroup)

* Chamado **myNSG.**
* No grupo de recursos **CreateIntLBQS-rg.**
* No local **a leste.**
* Com regras de segurança criadas em passos anteriores armazenados numa variável.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-standard-load-balancer"></a>Criar balanceador de carga padrão

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:

  * Um pool IP frontend que recebe o tráfego de rede de entrada no equilibrador de carga.
  * Uma piscina IP de backend onde a piscina frontal envia o tráfego de rede equilibrado de carga.
  * Uma sonda de saúde que determina a saúde dos casos de VM backend.
  * Uma regra do balançador de carga que define como o tráfego é distribuído para os VMs.

### <a name="create-frontend-ip"></a>Criar o IP de front-end

Crie um IP frontal com [New-AzLoadBalancerFrontendIpConfig:](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)

* Chama-se **MyFrontEnd.**
* Endereço ip privado de **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Configure piscina de endereço de fundo

Crie uma piscina de endereços back-end com [New-AzLoadBalancerBackendAddressPoolConfig:](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) 

* Chamado **myBackEndPool**.
* Os VMs ligam-se a esta piscina traseira nos degraus restantes.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Criar a sonda de estado de funcionamento

Uma sonda de saúde verifica todas as instâncias de máquinas virtuais para garantir que podem enviar tráfego de rede. 

Uma máquina virtual com uma verificação de sonda falhada é removida do equilibrador de carga. A máquina virtual é adicionada de volta ao equilibrador de carga quando a falha é resolvida.

Crie uma sonda de saúde com [Add-AzLoadBalancerProbeConfig:](/powershell/module/az.network/add-azloadbalancerprobeconfig)

* Monitoriza a saúde das máquinas virtuais.
* Chama-se **MyHealthProbe.**
* Protocolo **TCP**.
* Porta de monitorização **80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra do balançador de carga define:

* Configuração IP frontend para o tráfego de entrada.
* O backend IP pool para receber o tráfego.
* A fonte necessária e o porto de destino. 

Crie uma regra de balançador de carga com [Add-AzLoadBalanceruleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Nomeado **myHTTPRule**
* Ouvir no **Porto 80** na piscina frontal **myFrontEnd**.
* Envio de tráfego de rede equilibrado de carga para a piscina de endereço de backend **myBackEndPool** usando **a porta 80**. 
* Utilizando a sonda de saúde **myHealthProbe**.
* Protocolo **TCP**.
* Tempo de **15 minutos** inativo.
* Ativar o reset do TCP.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT -IdleTimeoutInMinutes $idl -EnableTcpReset
```
>[!NOTE]
>As máquinas virtuais no pool de backend não terão conectividade de internet de saída com esta configuração. </br> Para obter mais informações sobre o fornecimento de conectividade de saída, consulte: </br> **[Ligações de saída no Azure](load-balancer-outbound-connections.md)**</br> Opções para fornecer conectividade: </br> **[Configuração do balanceador de carga só de saída](egress-only.md)** </br> **[O que é Virtual Network NAT?](../virtual-network/nat-overview.md)**


### <a name="create-load-balancer-resource"></a>Criar recurso de equilibrador de carga

Criar um balanceador de carga interna com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Nomeado **myLoadBalancer**
* No **leste.**
* No grupo de recursos **CreateIntLBQS-rg.**

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Criar interfaces de rede

Criar três interfaces de rede com [o New-AzNetworkInterface:](/powershell/module/az.network/new-aznetworkinterface)

#### <a name="vm-1"></a>VM 1

* Chamado **myNicVM1**.
* No grupo de recursos **CreateIntLBQS-rg.**
* No local **a leste.**
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**
* Anexado ao balanceador de carga **myLoadBalancer** no **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Chamado **myNicVM2**.
* No grupo de recursos **CreateIntLBQS-rg.**
* No local **a leste.**
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**
* Anexado ao balanceador de carga **myLoadBalancer** no **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Defina um nome de utilizador e palavra-passe para as VMs com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Crie as máquinas virtuais com:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Sistema operativo Set-AzVM](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Chamado **myVM1**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Anexado à interface de rede **myNicVM1**.
* Anexado ao balanceador de carga **myLoadBalancer**.
* Na **Zona 1.**
* No **local leste.**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* Chamado **myVM2**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Anexado à interface de rede **myNicVM2**.
* Anexado ao balanceador de carga **myLoadBalancer**.
* Na **Zona 2.**
* No **local leste.**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Recomenda-se o balanceador de carga SKU standard para cargas de trabalho de produção. Para obter mais informações sobre skus, consulte **[skus de balançadores de carga Azure.](skus.md)**

## <a name="configure-virtual-network"></a>Configurar uma rede virtual

Antes de implementar VMs e testar o seu balanceador de carga, crie os recursos de rede virtual de suporte.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Crie uma rede virtual e anfitrião do Azure Bastion

Criar uma rede virtual com [a New-AzVirtualNetwork:](/powershell/module/az.network/new-azvirtualnetwork)

* Chamado **myVNet.**
* No grupo de recursos **CreateIntLBQS-rg.**
* Subnet chamado **myBackendSubnet**.
* Rede virtual **10.0.0.0/16**.
* Sub-rede **10.0.0.0/24**.
* Sub-rede chamada **AzureBastionSubnet**.
* Sub-rede **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Criar endereço IP público para anfitrião do Azure Bastion

Utilize [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público para o anfitrião do bastião:

* Nomeado **myPublicIPBastion**
* No grupo de recursos **CreateIntLBQS-rg.**
* No **local leste.**
* Método de atribuição **estático**.
* **Sku padrão.**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Criar anfitrião do Azure Bastion

Use [a New-AzBastion](/powershell/module/az.network/new-azbastion) para criar um hospedeiro de bastião:

* Chamado **myBastion.**
* No grupo de recursos **CreateIntLBQS-rg.**
* Na rede virtual **myVNet.**
* Associado a endereço ip público **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Pode levar alguns minutos para o anfitrião do Bastião Azure ser implantado.


### <a name="create-network-security-group"></a>Criar grupo de segurança de rede
Crie um grupo de segurança de rede para definir ligações recebidas para a sua rede virtual.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Criar uma regra de grupo de segurança de rede para a porta 80
Criar uma regra de grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig:](/powershell/module/az.network/new-aznetworksecurityruleconfig)

* Denominado **myNSGRuleHTTP**.
* Descrição do **Allow HTTP**.
* Acesso do **Allow**.
* Protocolo **(*)**.
* Direção **Entrada**.
* Prioridade **2000**.
* Fonte da **Internet.**
* Gama de porta de **origem de (*)**.
* Prefixo do endereço de destino de **(*)**.
* Porto de destino **80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Criar um grupo de segurança de rede com [o New-AzNetworkSecurityGroup:](/powershell/module/az.network/new-aznetworksecuritygroup)

* Chamado **myNSG.**
* No grupo de recursos **CreateIntLBQS-rg.**
* No local **a leste.**
* Com regras de segurança criadas em passos anteriores armazenados numa variável.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-basic-load-balancer"></a>Criar balanceador de carga básico

Esta secção descreve como pode criar e configurar os seguintes componentes do balanceador de carga:

  * Um pool IP frontend que recebe o tráfego de rede de entrada no equilibrador de carga.
  * Uma piscina IP de backend onde a piscina frontal envia o tráfego de rede equilibrado de carga.
  * Uma sonda de saúde que determina a saúde dos casos de VM backend.
  * Uma regra do balançador de carga que define como o tráfego é distribuído para os VMs.

### <a name="create-frontend-ip"></a>Criar o IP de front-end

Crie um IP frontal com [New-AzLoadBalancerFrontendIpConfig:](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)

* Chama-se **MyFrontEnd.**
* Endereço ip privado de **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Configure piscina de endereço de fundo

Crie uma piscina de endereços back-end com [New-AzLoadBalancerBackendAddressPoolConfig:](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) 

* Chamado **myBackEndPool**.
* Os VMs ligam-se a esta piscina traseira nos degraus restantes.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Criar a sonda de estado de funcionamento

Uma sonda de saúde verifica todas as instâncias de máquinas virtuais para garantir que podem enviar tráfego de rede. 

Uma máquina virtual com uma verificação de sonda falhada é removida do equilibrador de carga. A máquina virtual é adicionada de volta ao equilibrador de carga quando a falha é resolvida.

Crie uma sonda de saúde com [Add-AzLoadBalancerProbeConfig:](/powershell/module/az.network/add-azloadbalancerprobeconfig)

* Monitoriza a saúde das máquinas virtuais.
* Chama-se **MyHealthProbe.**
* Protocolo **TCP**.
* Porta de monitorização **80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra do balançador de carga define:

* Configuração IP frontend para o tráfego de entrada.
* O backend IP pool para receber o tráfego.
* A fonte necessária e o porto de destino. 

Crie uma regra de balançador de carga com [Add-AzLoadBalanceruleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Nomeado **myHTTPRule**
* Ouvir no **Porto 80** na piscina frontal **myFrontEnd**.
* Envio de tráfego de rede equilibrado de carga para a piscina de endereço de backend **myBackEndPool** usando **a porta 80**. 
* Utilizando a sonda de saúde **myHealthProbe**.
* Protocolo **TCP**.
* Tempo de **15 minutos** inativo.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -IdleTimeoutInMinutes $idl
```

### <a name="create-load-balancer-resource"></a>Criar recurso de equilibrador de carga

Criar um Balanceador de carga pública com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Nomeado **myLoadBalancer**
* No **leste.**
* No grupo de recursos **CreateIntLBQS-rg.**

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Criar interfaces de rede

Criar três interfaces de rede com [o New-AzNetworkInterface:](/powershell/module/az.network/new-aznetworkinterface)

#### <a name="vm-1"></a>VM 1

* Chamado **myNicVM1**.
* No grupo de recursos **CreateIntLBQS-rg.**
* No local **a leste.**
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**
* Anexado ao balanceador de carga **myLoadBalancer** no **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Chamado **myNicVM2**.
* No grupo de recursos **CreateIntLBQS-rg.**
* No local **a leste.**
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**
* Anexado ao balanceador de carga **myLoadBalancer** no **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Criar conjunto de disponibilidade para máquinas virtuais

Utilize [o New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) para criar um conjunto de disponibilidade para as máquinas virtuais:

* Chamado **myAvSet**.
* No grupo de recursos **CreateIntLBQS-rg.**
* No **local leste.**

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'CreateIntLBQS-rg'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Defina um nome de utilizador e palavra-passe para as VMs com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Crie as máquinas virtuais com:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Sistema operativo Set-AzVM](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Chamado **myVM1**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Anexado à interface de rede **myNicVM1**.
* Anexado ao balanceador de carga **myLoadBalancer**.
* No **local leste.**
* No conjunto de disponibilidade **myAvSet.**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* Chamado **myVM2**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Anexado à interface de rede **myNicVM2**.
* Anexado ao balanceador de carga **myLoadBalancer**.
* No **local leste.**
* No conjunto de disponibilidade **myAvSet.**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

Leva alguns minutos para criar e configurar os três VMs.

---

## <a name="install-iis"></a>Instalar o IIS

Utilize [a Configuração AzVMExtension](/powershell/module/az.compute/set-azvmextension?view=latest) para instalar a extensão de script personalizada. 

A extensão executa o powerShell Add-WindowsFeature Web-Server instalar o webserver IIS e, em seguida, atualiza a página Default.htm para mostrar o nome de anfitrião do VM:

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

### <a name="create-network-interface"></a>Criar interface de rede

Criar uma interface de rede com [o New-AzNetworkInterface:](/powershell/module/az.network/new-aznetworkinterface)

#### <a name="mytestvm"></a>myTestVM

* Chamado **myNicTestVM**.
* No grupo de recursos **CreateIntLBQS-rg.**
* No local **a leste.**
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG.**

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>Criar a máquina virtual

Definir um nome de utilizador e palavra-passe de administrador para a VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Crie a máquina virtual com:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Sistema operativo Set-AzVM](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* Chamado **myTestVM**.
* No grupo de recursos **CreateIntLBQS-rg.**
* Anexado à interface de rede **myNicTestVM**.
* No **local leste.**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
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

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o equilibrador de carga e os recursos restantes.

```azurepowershell-interactive
## Variable for command. ##
$rg = 'CreateIntLBQS-rg'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido

* Criou um balanceador de carga interno padrão ou básico
* Máquinas virtuais anexadas. 
* Configurado a regra de tráfego do balanceador de carga e a sonda de saúde.
* Testei o equilibrador de carga.

Para saber mais sobre o Azure Load Balancer, continue a.
> [!div class="nextstepaction"]
> [What is Azure Load Balancer?](load-balancer-overview.md) (O que é o Balanceador de Carga do Azure?)