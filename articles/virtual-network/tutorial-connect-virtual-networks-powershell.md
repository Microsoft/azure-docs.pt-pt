---
title: Ligar redes virtuais com o peering de rede virtual - PowerShell | Documentos da Microsoft
description: Neste artigo, saiba como ligar redes virtuais com a rede virtual peering, com o Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 49a6c91587905a8f7086b46b275a5078197939eb
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649956"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Ligar redes virtuais com o peering de rede virtual com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode ligar redes virtuais entre si com o peering de rede virtual. Depois de as redes virtuais estarem em modo de peering, os recursos nas duas redes virtuais conseguem comunicar entre si, com a mesma latência e largura de banda, como se os recursos estivessem na mesma rede virtual. Neste artigo, vai aprender a:

* Criar duas redes virtuais
* Ligar duas redes virtuais com um peering de rede virtual
* Implementar uma máquina virtual (VM) em cada rede virtual
* Comunicar entre VMs

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-virtual-networks"></a>Criar redes virtuais

Antes de criar uma rede virtual, terá de criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Criar um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Criar uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual denominada *myVirtualNetwork1* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo seguinte cria uma configuração de sub-rede com um prefixo de endereço 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Escreva a configuração de sub-rede para a rede virtual com [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), que cria a sub-rede:

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

Criar um peering [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). Os seguintes elementos de rede de exemplo *myVirtualNetwork1* ao *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Na saída devolvida após o comando anterior é executado, verá que o **PeeringState** é *iniciado*. O peering permanece no *iniciado* até que cria o peering a partir de estado *myVirtualNetwork2* para *myVirtualNetwork1*. Cria um peering a partir *myVirtualNetwork2* ao *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Na saída devolvida após o comando anterior é executado, verá que o **PeeringState** é *ligado*. Azure também alterou o estado do peering do *myVirtualNetwork1-myVirtualNetwork2* peering para *ligado*. Confirme que o estado do peering para o *myVirtualNetwork1-myVirtualNetwork2* peering alterado para *ligado* com [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Recursos de uma rede virtual não consegue comunicar com os recursos em outra rede virtual até que o **PeeringState** para os peerings em ambas as redes virtuais é *ligado*.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual, para que possa comunicar entre as mesmas num passo posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [novo-AzVM](/powershell/module/az.compute/new-azvm). O exemplo seguinte cria uma VM com o nome *myVm1* no *myVirtualNetwork1* rede virtual. O `-AsJob` opção cria a VM em segundo plano, para poder continuar para o passo seguinte. Quando lhe for pedido, introduza o nome de utilizador e palavra-passe que pretende iniciar sessão na VM com.

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

A criação da VM demora alguns minutos. Não continue com passos posteriores até que o Azure cria a VM e devolve o resultado para o PowerShell.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

Pode ligar ao endereço IP público de uma VM a partir da internet. Uso [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para devolver o endereço IP público de uma VM. O exemplo seguinte devolve o endereço IP público da VM *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Utilize o seguinte comando para criar uma sessão de área de trabalho remota com o *myVm1* VM a partir do seu computador local. Substitua `<publicIpAddress>` pelo endereço IP devolvido no comando anterior.

```
mstsc /v:<publicIpAddress>
```

Um ficheiro do protocolo de ambiente de trabalho remoto (. rdp) é criado, transferido para o computador e aberto. Introduza o nome de utilizador e palavra-passe (poderá ter de selecionar **mais escolhas**, em seguida, **utilizar uma conta diferente**, para especificar as credenciais que introduziu quando criou a VM) e, em seguida, clique em **OK** . Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para continuar com a ligação.

Sobre o *myVm1* VM, ativar o controlo de mensagem ICMP (Internet Protocol) através do Windows firewall para que pode efetuar o ping esta VM a partir de *myVm2* num passo posterior, com o PowerShell:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Apesar do ping é utilizado para comunicar entre VMs neste artigo, não é recomendado permitir o ICMP através da Firewall do Windows para implementações de produção.

Para ligar à VM *myVm2*, introduza o seguinte comando numa linha de comandos da VM *myVm1*:

```
mstsc /v:10.1.0.4
```

Uma vez que ativou o ping na *myVm1*, pode agora fazer ping por endereço IP a partir de um prompt de comando no *myVm2* VM:

```
ping 10.0.0.4
```

Receberá quatro respostas. Desligue as sessões RDP das *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos nele contidos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a ligar duas redes na mesma região do Azure, com o peering de rede virtual. Também pode ligar em modo de peering máquinas virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region) e em [diferentes subscrições do Azure](create-peering-different-subscriptions.md#powershell), bem como criar [designs de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com peering. Para obter mais informações sobre o peering de rede virtual, veja [Descrição geral do peering de rede virtual](virtual-network-peering-overview.md) e [Gerir peerings de rede virtual](virtual-network-manage-peering.md).

Pode [ligar o seu computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) através de uma VPN e interagir com os recursos numa rede virtual, ou em redes virtuais em modo de peering. Para obter scripts reutilizáveis concluir a muitas das tarefas abordadas os artigos de rede virtual, consulte [exemplos de scripts](powershell-samples.md).