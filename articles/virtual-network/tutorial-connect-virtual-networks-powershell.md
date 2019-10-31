---
title: Conectar redes virtuais com o emparelhamento de rede virtual-PowerShell | Microsoft Docs
description: Neste artigo, você aprende a conectar redes virtuais com o emparelhamento de rede virtual, usando Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 63584eedb0e51ddbca0d644bc17db3facd9225b5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164020"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Conectar redes virtuais com o emparelhamento de rede virtual usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode ligar redes virtuais entre si com o peering de rede virtual. Depois de as redes virtuais estarem em modo de peering, os recursos nas duas redes virtuais conseguem comunicar entre si, com a mesma latência e largura de banda, como se os recursos estivessem na mesma rede virtual. Neste artigo, vai aprender a:

* Criar duas redes virtuais
* Ligar duas redes virtuais com um peering de rede virtual
* Implementar uma máquina virtual (VM) em cada rede virtual
* Comunicar entre VMs

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o Azure PowerShell módulo versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-virtual-networks"></a>Criar redes virtuais

Antes de criar uma rede virtual, você precisa criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVirtualNetwork1* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo a seguir cria uma configuração de sub-rede com um prefixo de endereço 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Grave a configuração de sub-rede na rede virtual com [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), que cria a sub-rede:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Crie uma rede virtual com um prefixo de endereço 10.1.0.0/16 e uma sub-rede:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Colocar redes virtuais em modo de peering

Crie um emparelhamento com [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). O exemplo a seguir tem *myVirtualNetwork1* para *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Na saída retornada após a execução do comando anterior, você verá que o **PeeringState** é *iniciado*. O emparelhamento permanece no estado *iniciado* até que você crie o emparelhamento de *myVirtualNetwork2* para *myVirtualNetwork1*. Crie um emparelhamento de *myVirtualNetwork2* para *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Na saída retornada após a execução do comando anterior, você verá que o **PeeringState** está *conectado*. O Azure também alterou o estado de emparelhamento do emparelhamento *myVirtualNetwork1-myVirtualNetwork2* para *conectado*. Confirme se o estado de emparelhamento do emparelhamento *myVirtualNetwork1-myVirtualNetwork2* foi alterado para *conectado* com [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Os recursos em uma rede virtual não podem se comunicar com recursos na outra rede virtual até que o **PeeringState** para os emparelhamentos em ambas as redes virtuais esteja *conectado*.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual, para que possa comunicar entre as mesmas num passo posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM chamada *myVm1* na rede virtual *myVirtualNetwork1* . A opção `-AsJob` cria a VM em segundo plano, para que você possa continuar para a próxima etapa. Quando solicitado, insira o nome de usuário e a senha que você deseja fazer logon na VM.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

A criação da VM demora alguns minutos. Não continue com etapas posteriores até que o Azure crie a VM e retorne a saída para o PowerShell.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

Você pode se conectar ao endereço IP público de uma VM da Internet. Use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para retornar o endereço IP público de uma VM. O exemplo seguinte devolve o endereço IP público da VM *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Use o comando a seguir para criar uma sessão de área de trabalho remota com a VM *myVm1* do computador local. Substitua `<publicIpAddress>` pelo endereço IP devolvido no comando anterior.

```
mstsc /v:<publicIpAddress>
```

Um arquivo de protocolo RDP (. RDP) é criado, baixado em seu computador e aberto. Insira o nome de usuário e a senha (talvez seja necessário selecionar **mais opções**e, em seguida, **usar uma conta diferente**, para especificar as credenciais inseridas quando você criou a VM) e, em seguida, clique em **OK**. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para continuar com a ligação.

Na VM *myVm1* , habilite o protocolo ICMP por meio do firewall do Windows para que você possa executar o ping dessa VM do *myVm2* em uma etapa posterior, usando o PowerShell:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Embora o ping seja usado para se comunicar entre as VMs neste artigo, não é recomendável o ICMP através do firewall do Windows para implantações de produção.

Para ligar à VM *myVm2*, introduza o seguinte comando numa linha de comandos da VM *myVm1*:

```
mstsc /v:10.1.0.4
```

Como você habilitou o ping no *myVm1*, agora você pode executar o ping dele pelo endereço IP em um prompt de comando na VM *myVm2* :

```
ping 10.0.0.4
```

Você recebe quatro respostas. Desligue as sessões RDP das *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a conectar duas redes na mesma região do Azure, com o emparelhamento de rede virtual. Também pode ligar em modo de peering máquinas virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region) e em [diferentes subscrições do Azure](create-peering-different-subscriptions.md#powershell), bem como criar [designs de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com peering. Para obter mais informações sobre o peering de rede virtual, veja [Descrição geral do peering de rede virtual](virtual-network-peering-overview.md) e [Gerir peerings de rede virtual](virtual-network-manage-peering.md).

Você pode [conectar seu próprio computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) por meio de uma VPN e interagir com recursos em uma rede virtual ou em redes virtuais emparelhadas. Para que os scripts reutilizáveis concluam muitas das tarefas abordadas nos artigos de rede virtual, consulte [exemplos de script](powershell-samples.md).