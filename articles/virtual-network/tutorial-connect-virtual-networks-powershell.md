---
title: Ligue redes virtuais com o estomo VNet - Azure PowerShell
description: Neste artigo, aprende-se a ligar redes virtuais com o espreitamento de rede virtual, utilizando o Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d93efaedbb0596382b0601a17098311e075618b7
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791985"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Conecte redes virtuais com o espreitamento de rede virtual usando o PowerShell

Pode ligar redes virtuais entre si com o peering de rede virtual. Depois de as redes virtuais estarem em modo de peering, os recursos nas duas redes virtuais conseguem comunicar entre si, com a mesma latência e largura de banda, como se os recursos estivessem na mesma rede virtual. Neste artigo, vai aprender a:

* Criar duas redes virtuais
* Ligar duas redes virtuais com um peering de rede virtual
* Implementar uma máquina virtual (VM) em cada rede virtual
* Comunicar entre VMs

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 1.0.0 ou mais tarde do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-virtual-networks"></a>Criar redes virtuais

Antes de criar uma rede virtual, tem de criar um grupo de recursos para a rede virtual, e todos os outros recursos criados neste artigo. Criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Criar uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVirtualNetwork1* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo a seguir cria uma configuração de sub-rede com um prefixo de endereço de 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Escreva a configuração da sub-rede para a rede virtual com [Set-AzVirtualNetwork,](/powershell/module/az.network/Set-azVirtualNetwork)que cria a sub-rede:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Criar uma rede virtual com um prefixo de endereço 10.1.0.0/16 e uma sub-rede:

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

Crie um espreitante com [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). O exemplo a seguir é *myVirtualNetwork1* ao *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Na saída devolvida após a execução do comando anterior, vê-se que o **Estado-Peering** é *Iniciado*. O espreitamento permanece no estado *iniciado* até criar o espreitamento do *myVirtualNetwork2* para *o myVirtualNetwork1*. Crie um espreitante do *myVirtualNetwork2* para *o myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Na saída devolvida após a execução do comando anterior, vê-se que o **Estado-Peering** está *ligado*. O Azure também alterou o estado de observação do *myVirtualNetwork1-myVirtualNetwork2* olhando para *Connected*. Confirme que o estado de observação do *myVirtualNetwork1-myVirtualNetwork2* mudou para *Connected* with [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Os recursos numa rede virtual não podem comunicar com recursos na outra rede virtual até que o **Estado-Peering** para os seus pares em ambas as redes virtuais esteja *conectado*.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual, para que possa comunicar entre as mesmas num passo posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Criar um VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria um VM chamado *myVm1* na rede virtual *myVirtualNetwork1.* A `-AsJob` opção cria o VM em segundo plano, para que possa continuar até ao passo seguinte. Quando solicitado, insira o nome de utilizador e a palavra-passe com a seguinte.

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

A criação da VM demora alguns minutos. Não continue com passos posteriores até que a Azure crie o VM e devolva a saída à PowerShell.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

Pode ligar-se ao endereço IP público de um VM a partir da internet. Utilize [o Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para devolver o endereço IP público de um VM. O exemplo seguinte devolve o endereço IP público da VM *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Utilize o seguinte comando para criar uma sessão de ambiente de trabalho remota com o *myVm1* VM do computador local. Substitua `<publicIpAddress>` pelo endereço IP devolvido no comando anterior.

```
mstsc /v:<publicIpAddress>
```

É criado um ficheiro Remote Desktop Protocol (.rdp) que é criado, descarregado para o seu computador e aberto. Introduza o nome de utilizador e a palavra-passe (poderá ter de selecionar **Mais escolhas**, em seguida, **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM) e, em seguida, clique em **OK**. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para continuar com a ligação.

No *myVm1* VM, ative o Protocolo de Mensagem de Controlo de Internet (ICMP) através da firewall do Windows para que possa extrair este VM do *myVm2* num passo posterior, utilizando o PowerShell:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Embora o ping seja utilizado para comunicar entre VMs neste artigo, não é recomendado o ICMP através do Windows Firewall para implementações de produção.

Para ligar à VM *myVm2*, introduza o seguinte comando numa linha de comandos da VM *myVm1*:

```
mstsc /v:10.1.0.4
```

Uma vez que ativou o ping no *myVm1,* pode agora pingá-lo por endereço IP a partir de uma solicitação de comando no *myVm2* VM:

```
ping 10.0.0.4
```

Recebes quatro respostas. Desligue as sessões RDP das *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, utilize [o Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a ligar duas redes na mesma região do Azure, com olhando para a rede virtual. Também pode ligar em modo de peering máquinas virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region) e em [diferentes subscrições do Azure](create-peering-different-subscriptions.md#powershell), bem como criar [designs de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) com peering. Para obter mais informações sobre o peering de rede virtual, veja [Descrição geral do peering de rede virtual](virtual-network-peering-overview.md) e [Gerir peerings de rede virtual](virtual-network-manage-peering.md).

Pode [ligar o seu próprio computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) através de uma VPN e interagir com recursos numa rede virtual, ou em redes virtuais. Para scripts reutilizáveis para completar muitas das tarefas abrangidas pelos artigos de rede virtual, consulte [as amostras de script](powershell-samples.md).
