---
title: Conecte redes virtuais com peering VNet - Azure PowerShell
description: Neste artigo, aprende-se a conectar redes virtuais com o peering de rede virtual, utilizando o Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
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
ms.openlocfilehash: caa620c2389bb6e2387636bc262ceb2de99d8e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201310"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Conecte redes virtuais com o peering de rede virtual usando powerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode ligar redes virtuais entre si com o peering de rede virtual. Depois de as redes virtuais estarem em modo de peering, os recursos nas duas redes virtuais conseguem comunicar entre si, com a mesma latência e largura de banda, como se os recursos estivessem na mesma rede virtual. Neste artigo, vai aprender a:

* Criar duas redes virtuais
* Ligar duas redes virtuais com um peering de rede virtual
* Implementar uma máquina virtual (VM) em cada rede virtual
* Comunicar entre VMs

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 1.0.0 ou posterior do módulo PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-virtual-networks"></a>Criar redes virtuais

Antes de criar uma rede virtual, tem de criar um grupo de recursos para a rede virtual, e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Criar uma rede virtual com [new-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual chamada *myVirtualNetwork1* com o prefixo de endereço *10.0.0.0.0/16*.

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

Escreva a configuração da sub-rede para a rede virtual com [set-AzVirtualNetwork,](/powershell/module/az.network/Set-azVirtualNetwork)que cria a sub-rede:

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

Crie um peering com [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). O exemplo seguinte é *o meu VirtualNetwork1* para *o myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Na saída devolvida após a execução do comando anterior, vê-se que o **Estado De Speering** é *iniciado*. O peering permanece no estado *iniciado* até criar o peering do *myVirtualNetwork2* para *myVirtualNetwork1*. Crie um olhar do *myVirtualNetwork2* para *o myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Na saída devolvida após a execução do comando anterior, vê-se que o **Estado De Epeering** está *ligado*. O Azure também mudou o estado de peering do *myVirtualNetwork1-myVirtualNetwork2* peering to *Connected*. Confirme que o estado de observação do *miVirtualNetwork1-myVirtualNetwork2 alterou-se* para *Connected* with [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Os recursos de uma rede virtual não podem comunicar com recursos na outra rede virtual até que o **Peering State** para os pares em ambas as redes virtuais esteja *conectado.*

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual, para que possa comunicar entre as mesmas num passo posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie um VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo seguinte cria um VM chamado *myVm1* na rede virtual *myVirtualNetwork1.* A `-AsJob` opção cria o VM em segundo plano, para que possa continuar até ao próximo passo. Quando solicitado, introduza o nome de utilizador e a palavra-passe com o que pretende iniciar sessão no VM com.

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

A criação da VM demora alguns minutos. Não continue com passos posteriores até que o Azure crie o VM e desloque a saída à PowerShell.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

Pode ligar-se ao endereço IP público de um VM a partir da internet. Utilize o [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para devolver o endereço IP público de um VM. O exemplo seguinte devolve o endereço IP público da VM *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Utilize o seguinte comando para criar uma sessão remota de ambiente de trabalho com o *myVm1* VM a partir do seu computador local. Substitua `<publicIpAddress>` pelo endereço IP devolvido no comando anterior.

```
mstsc /v:<publicIpAddress>
```

É criado um ficheiro Remote Desktop Protocol (.rdp), descarregado para o seu computador e aberto. Introduza o nome de utilizador e a palavra-passe (poderá ter de selecionar **Mais escolhas**, depois **utilize uma conta diferente**, para especificar as credenciais que inseriu quando criou o VM), e depois clique em **OK**. Poderá receber um aviso de certificado durante o processo de início de sessão. Clique em **Sim** ou **Continuar** para continuar com a ligação.

No *myVm1* VM, ative o Protocolo de Mensagem de Controlo de Internet (ICMP) através da firewall do Windows para que possa pingar este VM a partir do *myVm2* num passo posterior, utilizando o PowerShell:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Embora o ping seja utilizado para comunicar entre VMs neste artigo, não é recomendado o ICMP através do Firewall do Windows para implementações de produção.

Para ligar à VM *myVm2*, introduza o seguinte comando numa linha de comandos da VM *myVm1*:

```
mstsc /v:10.1.0.4
```

Uma vez que permitiu o ping no *myVm1,* pode agora aplicá-lo pelo endereço IP a partir de um pedido de comando no *myVm2* VM:

```
ping 10.0.0.4
```

Recebequatro respostas. Desligue as sessões RDP das *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize o [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que contém.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ligar duas redes na mesma região de Azure, com o peering de rede virtual. Também pode ligar em modo de peering máquinas virtuais em diferentes [regiões suportadas](virtual-network-manage-peering.md#cross-region) e em [diferentes subscrições do Azure](create-peering-different-subscriptions.md#powershell), bem como criar [designs de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) com peering. Para obter mais informações sobre o peering de rede virtual, veja [Descrição geral do peering de rede virtual](virtual-network-peering-overview.md) e [Gerir peerings de rede virtual](virtual-network-manage-peering.md).

Pode [ligar o seu próprio computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) através de uma VPN, e interagir com recursos numa rede virtual ou em redes virtuais. Para que scripts reutilizáveis completem muitas das tarefas abrangidas pelos artigos de rede virtuais, consulte [amostras](powershell-samples.md)de script .