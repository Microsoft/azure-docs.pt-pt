---
title: Criar uma rede virtual - quickstart - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Neste início rápido, vai aprender a criar uma rede virtual com o Portal do Azure. Uma rede virtual permite que os recursos do Azure, como máquinas virtuais, comuniquem-se em privado uns com os outros, e com a internet.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: kumud
ms.openlocfilehash: 1d30b35264707c59c899cc3a224e4affa2a4696e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241445"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Início Rápido :criar uma rede virtual com o PowerShell

Uma rede virtual permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem-se em privado entre si, e com a internet. Neste início rápido, vai aprender a criar uma rede virtual. Depois de criar uma rede virtual, vai implementar duas VMs na rede virtual. Em seguida, ligue-se aos VMs a partir da internet e comunique-se em privado através da rede virtual.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o PowerShell localmente, este quickstart requer que utilize a versão 1.0.0 do módulo PowerShell Azure. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Consulte o [módulo PowerShell Azure](/powershell/azure/install-az-ps) para instalar e atualizar informações.

Finalmente, se estiver a executar a PowerShell localmente, também terá de correr `Connect-AzAccount`. Este comando cria uma ligação com o Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Criar um grupo de recursos e uma rede virtual

Há um punhado de passos que você tem que passar para obter o seu grupo de recursos e rede virtual configurado.

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de poder criar uma rede virtual, tem de criar um grupo de recursos para hospedar a rede virtual. Crie um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Criar uma rede virtual com [new-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* na localização *EastUS:*

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

O Azure descoloca recursos numa rede virtual, por isso é preciso criar uma subrede. Criar uma configuração de sub-rede chamada *padrão* com [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associar a subneta à rede virtual

Pode escrever a configuração da sub-rede para a rede virtual com [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Este comando cria a sub-rede:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie o primeiro VM com [New-AzVM](/powershell/module/az.compute/new-azvm). Quando diriges o próximo comando, és solicitado por credenciais. Introduza um nome de utilizador e uma palavra-passe para o VM:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

A opção `-AsJob` cria o VM em segundo plano. Pode continuar até ao próximo passo.

Quando o Azure começar a criar o VM em segundo plano, terás algo assim de volta:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

Crie o segundo VM com este comando:

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Terá de criar outro utilizador e senha. Azure leva alguns minutos para criar o VM.

> [!IMPORTANT]
> Não continue com o próximo passo até que Azure termine.  Saberá que é feito quando devolver a produção à PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Utilize o [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para devolver o endereço IP público de um VM. Este exemplo devolve o endereço IP público do *myVm1* VM:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Abra um pedido de comando no seu computador local. Execute o comando `mstsc`. Substitua `<publicIpAddress>` pelo endereço IP público devolvido do último passo:

> [!NOTE]
> Se tiver estado a executar estes comandos a partir de um pedido powerShell no seu computador local, e estiver a utilizar a versão 1.0 ou mais posterior do módulo Az PowerShell, pode continuar nessa interface.

```cmd
mstsc /v:<publicIpAddress>
```
1. Se lhe for pedido, selecione **Ligar**.

1. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM.

    > [!NOTE]
    > Poderá ter de selecionar **Mais escolhas** > **Utilize uma conta diferente,** para especificar as credenciais que inseriu quando criou o VM.

1. Selecione **OK**.

1. Pode receber um aviso de certificado. Se o fizer, selecione **Sim** ou **Continue**.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

1. No Ambiente de Trabalho Remoto do *myVm1,* abra powerShell.

1. Introduza `ping myVm2`.

    Vai sacá-lo de volta.

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    O ping falha, porque utiliza o Protocolo de Mensagem de Controlo de Internet (ICMP). Por predefinição, o ICMP não é permitido através da sua firewall Windows.

1. Para permitir que *o myVm2* ping *myVm1* em um passo posterior, insira este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Este comando permite que o ICMP entre na firewall do Windows.

1. Feche a ligação de ambiente de trabalho remoto à *myVm1*.

1. Repita os passos em [Ligar a um VM a partir da internet](#connect-to-a-vm-from-the-internet). Desta vez, ligue-se ao *myVm2*.

1. Numa linha de comandos na VM *myVm2*, introduza `ping myvm1`.

    Vai sacá-lo de volta.

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Vai receber respostas da *myVm1*porque permitiu o ICMP através da firewall do Windows na VM *myVm1* num passo anterior.

1. Feche a ligação de ambiente de trabalho remoto à *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar com a rede virtual e os VMs, utilize o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que tem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma rede virtual predefinida e duas VMs. Ligou a uma VM a partir da Internet e comunicou em privado entre a VM e outra VM. Para saber mais sobre as definições de rede virtual, veja [Gerir uma rede virtual](manage-virtual-network.md).

O Azure permite uma comunicação privada sem restrições entre máquinas virtuais. Por predefinição, o Azure apenas permite ligações remotas de ambiente de trabalho à entrada de VMs do Windows a partir da internet. Para saber mais sobre a configuração de diferentes tipos de comunicações de rede VM, vá ao tutorial de tráfego da [rede Filter.](tutorial-filter-network-traffic.md)
