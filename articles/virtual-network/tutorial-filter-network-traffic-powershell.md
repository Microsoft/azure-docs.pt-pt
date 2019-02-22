---
title: Filtrar o tráfego de rede - Azure PowerShell | Documentos da Microsoft
description: Neste artigo, irá aprender a filtrar o tráfego de rede a uma sub-rede, com um grupo de segurança de rede, com o PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 023662f0293debb1b40fc8ea10bb725eab7be4d8
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649939"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtrar o tráfego de rede com um grupo de segurança de rede com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode filtrar o tráfego de rede de entrada e de saída de uma sub-rede de rede virtual com um grupo de segurança de rede. Os grupos de segurança de rede contêm regras de segurança que filtram o tráfego de rede por endereço IP, porta e protocolo. As regras de segurança são aplicadas a recursos implementados numa sub-rede. Neste artigo, vai aprender a:

* Crie um grupo de segurança de rede e regras de segurança.
* Criar uma rede virtual e associar um grupo de segurança de rede a uma sub-rede
* Implementar máquinas virtuais (VMs) numa sub-rede
* Testar os filtros de tráfego

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Os grupos de segurança de rede contêm regras de segurança. As regras de segurança especificam uma origem e um destino. As origens e destinos podem ser grupos de segurança de aplicações.

### <a name="create-application-security-groups"></a>Criar grupos de segurança de aplicações

Primeiro, crie um grupo de recursos para todos os recursos criados neste artigo com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos na localização *eastus*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Crie um grupo de segurança de aplicações com [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup). Os grupo de segurança de aplicações permitem-lhe agrupar servidores com requisitos de filtragem de portas semelhante. O exemplo seguinte cria dois grupos de segurança de aplicações.

```azurepowershell-interactive
$webAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Criar regras de segurança

Crie uma regra de segurança com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). O exemplo seguinte cria uma regra que permite o tráfego de entrada da Internet para o grupo de segurança de aplicações *myWebServers* através das portas 80 e 443:

```azurepowershell-interactive
$webRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

Neste artigo, o RDP (porta 3389) está exposta à Internet para o *myAsgMgmtServers* VM. Em ambientes de produção, em vez de expor a porta 3389 à Internet, recomenda-se que ligue aos recursos do Azure que quer gerir com uma [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou da ligação de rede [privada](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). O exemplo seguinte cria um grupo de segurança de rede com o nome *myNsg*:

```powershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Criar uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual com o nome *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)e, em seguida, escreva a configuração de sub-rede para a rede virtual com [conjunto AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork). O exemplo seguinte adiciona uma sub-rede com o nome *mySubnet* à rede virtual e associa o grupo de segurança de rede *myNsg* à mesma:

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Antes de criar as VMs, obtenha o objeto de rede virtual com a sub-rede com o [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```

Crie um endereço IP público para cada VM com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```powershell-interactive
$publicIpWeb = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Crie duas interfaces de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)e atribuir um endereço IP público à interface de rede. O exemplo seguinte cria uma interface de rede, associa o endereço IP público de *myVmWeb* à mesma e torna-a membro do grupo de segurança de rede *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

O exemplo seguinte cria uma interface de rede, associa o endereço IP público de *myVmMgmt* à mesma e torna-a membro do grupo de segurança de aplicações *myAsgMgmtServers*:

```powershell-interactive
$mgmtNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Crie duas VMs na rede virtual, para que possa confirmar a filtragem de tráfego num passo posterior.

Crie uma configuração de VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig), em seguida, criar a VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo seguinte cria uma VM que vai funcionar como servidor Web. A opção `-AsJob` cria a VM em segundo plano, para que possa prosseguir para o passo seguinte:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $webNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Crie uma VM que vai funcionar como servidor de gestão:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

A criação da máquina virtual demora alguns minutos. Não continue para o passo seguinte enquanto o Azure não concluir a criação da VM.

## <a name="test-traffic-filters"></a>Testar os filtros de tráfego

Uso [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para devolver o endereço IP público de uma VM. O exemplo seguinte devolve o endereço IP público da VM *myVmMgmt*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Utilize o seguinte comando para criar uma sessão de ambiente de trabalho remoto com a VM *myVmMgmt* a partir do computador local. Substitua `<publicIpAddress>` pelo endereço IP devolvido no comando anterior.

```
mstsc /v:<publicIpAddress>
```

Abra o ficheiro RDP transferido. Se lhe for pedido, selecione **Ligar**.

Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM (poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM) e, em seguida, selecione **OK**. Poderá receber um aviso de certificado durante o processo de início de sessão. Selecione **Sim** para continuar a ligação.

A ligação é bem-sucedida, porque a porta 3389 pode receber tráfego de entrada da Internet para o grupo de segurança de aplicações *myAsgMgmtServers* no qual a interface de rede ligada à VM *myVmMgmt* se encontra.

Utilize o seguinte comando para criar uma ligação de ambiente de trabalho remoto para a VM *myVmWeb* a partir da VM *myVmMgmt*, com o seguinte comando do PowerShell:

``` 
mstsc /v:myvmWeb
```

A ligação é bem-sucedida, porque uma regra de segurança predefinida em cada grupo de segurança de rede permite o tráfego em todas as portas entre todos os endereços IP dentro de uma rede virtual. Não pode criar uma ligação de ambiente de trabalho remoto para a VM *myVmWeb* a partir da Internet porque a regra de segurança para *myAsgWebServers* não permite a porta 3389 de entrada da Internet.

Utilize o seguinte comando para instalar o Microsoft IIS na VM *myVmWeb* a partir do PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Depois de concluída a instalação do IIS, desligue da VM *myVmWeb*, permanecendo na ligação de ambiente de trabalho remoto da VM *myVmMgmt*. Para ver o ecrã de boas-vindas do IIS, abra o browser e navegue para http://myVmWeb.

Desligue da VM *myVmMgmt*.

No computador, introduza o seguinte comando do PowerShell para obter o endereço IP público do servidor *myVmWeb*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Para confirmar se consegue aceder ao servidor Web *myVmWeb* a partir de fora do Azure, abra um browser no computador e navegue para `http://<public-ip-address-from-previous-step>`. A ligação é bem-sucedida, porque a porta 80 pode receber tráfego de entrada da Internet para o grupo de segurança de aplicações *myAsgWebServers* no qual a interface de rede ligada à VM *myVmWeb* se encontra.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou um grupo de segurança de rede e associou-o a uma sub-rede de rede virtual. Para saber mais sobre os grupos de segurança de rede, veja [Descrição geral dos grupos de segurança de rede](security-overview.md) e [Manage a network security group](manage-network-security-group.md) (Gerir um grupo de segurança de rede).

O Azure encaminha o tráfego entre sub-redes por predefinição. Em alternativa, pode optar por encaminhar o tráfego entre sub-redes através de uma VM que funcione, por exemplo, como uma firewall. Para saber como, veja [criar uma tabela de rotas](tutorial-create-route-table-powershell.md).
