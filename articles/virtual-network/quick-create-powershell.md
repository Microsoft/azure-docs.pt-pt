---
title: Criar uma rede virtual-início rápido-Azure PowerShell
titlesuffix: Azure Virtual Network
description: Neste início rápido, vai aprender a criar uma rede virtual com o Portal do Azure. Uma rede virtual permite que recursos do Azure, como máquinas virtuais, se comuniquem de forma privada entre si e com a Internet.
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
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161521"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Início Rápido :criar uma rede virtual com o PowerShell

Uma rede virtual permite que recursos do Azure, como VMs (máquinas virtuais), se comuniquem em modo privado e com a Internet. Neste início rápido, vai aprender a criar uma rede virtual. Depois de criar uma rede virtual, vai implementar duas VMs na rede virtual. Em seguida, conecte-se às VMs da Internet e comunique-se de forma privada pela rede virtual.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você decidir instalar e usar o PowerShell localmente em vez disso, este guia de início rápido exigirá que você use Azure PowerShell módulo versão 1.0.0 ou posterior. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps) para obter informações de instalação e atualização.

Por fim, se você estiver executando o PowerShell localmente, também precisará executar `Connect-AzAccount`. Esse comando cria uma conexão com o Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Criar um grupo de recursos e uma rede virtual

Há algumas etapas que você precisa percorrer para obter o grupo de recursos e a rede virtual configurados.

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Para poder criar uma rede virtual, você precisa criar um grupo de recursos para hospedar a rede virtual. Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Este exemplo cria um grupo de recursos chamado *MyResource* Group no local *eastus* :

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* na localização *lesteus* :

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

O Azure implanta recursos em uma sub-rede em uma rede virtual, portanto, você precisa criar uma sub-rede. Crie uma configuração de sub-rede chamada *padrão* com [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associar a sub-rede à rede virtual

Você pode gravar a configuração de sub-rede na rede virtual com [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Este comando cria a sub-rede:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie a primeira VM com [New-AzVM](/powershell/module/az.compute/new-azvm). Quando você executar o próximo comando, suas credenciais serão solicitadas. Insira um nome de usuário e uma senha para a VM:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

A opção `-AsJob` cria a VM em segundo plano. Você pode continuar para a próxima etapa.

Quando o Azure começar a criar a VM em segundo plano, você obterá algo assim:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

Crie a segunda VM com este comando:

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Você precisará criar outro usuário e senha. O Azure leva alguns minutos para criar a VM.

> [!IMPORTANT]
> Não continue com a próxima etapa até que o Azure seja concluído.  Você saberá que isso é feito quando retorna a saída para o PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para retornar o endereço IP público de uma VM. Este exemplo retorna o endereço IP público da VM *myVm1* :

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Abra um prompt de comando no computador local. Execute o comando `mstsc`. Substitua `<publicIpAddress>` pelo endereço IP público retornado da última etapa:

> [!NOTE]
> Se você estiver executando esses comandos em um prompt do PowerShell no computador local e estiver usando o módulo AZ PowerShell versão 1,0 ou posterior, poderá continuar nessa interface.

```cmd
mstsc /v:<publicIpAddress>
```
1. Se lhe for pedido, selecione **Ligar**.

1. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM.

    > [!NOTE]
    > Talvez seja necessário selecionar **mais opções** > **use uma conta diferente**, para especificar as credenciais que você inseriu quando criou a VM.

1. Selecione **OK**.

1. Você pode receber um aviso de certificado. Se você fizer isso, selecione **Sim** ou **continuar**.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

1. No Área de Trabalho Remota do *myVm1*, abra o PowerShell.

1. Introduza `ping myVm2`.

    Você obterá algo assim:

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

    O ping falha, pois ele usa o protocolo ICMP. Por padrão, o ICMP não é permitido por meio do firewall do Windows.

1. Para permitir que o *myVm2* execute ping em *myVm1* em uma etapa posterior, digite este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Esse comando permite que o ICMP seja ligado por meio do firewall do Windows.

1. Feche a ligação de ambiente de trabalho remoto à *myVm1*.

1. Repita as etapas em [conectar-se a uma VM da Internet](#connect-to-a-vm-from-the-internet). Desta vez, conecte-se ao *myVm2*.

1. Numa linha de comandos na VM *myVm2*, introduza `ping myvm1`.

    Você obterá algo assim:

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

Quando você terminar de usar a rede virtual e as VMs, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele tem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma rede virtual predefinida e duas VMs. Ligou a uma VM a partir da Internet e comunicou em privado entre a VM e outra VM. Para saber mais sobre as definições de rede virtual, veja [Gerir uma rede virtual](manage-virtual-network.md).

O Azure permite comunicação privada irrestrita entre máquinas virtuais. Por padrão, o Azure permite apenas conexões de área de trabalho remota de entrada para VMs do Windows da Internet. Para saber mais sobre como configurar diferentes tipos de comunicações de rede de VM, vá para o tutorial [Filtrar tráfego de rede](tutorial-filter-network-traffic.md) .
