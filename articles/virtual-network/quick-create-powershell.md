---
title: Criar uma rede virtual - quickstart - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Neste início rápido, vai aprender a criar uma rede virtual com o Portal do Azure. Uma rede virtual permite que os recursos do Azure, como máquinas virtuais, comuniquem-se em privado entre si e com a internet.
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
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84707468"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Início Rápido :criar uma rede virtual com o PowerShell

Uma rede virtual permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem-se em privado entre si e com a internet. Neste início rápido, vai aprender a criar uma rede virtual. Depois de criar uma rede virtual, vai implementar duas VMs na rede virtual. Em seguida, conecta-se aos VMs a partir da internet e comunica-se em privado através da rede virtual.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o PowerShell localmente, este arranque rápido requer que utilize a versão 1.0 ou mais tarde do módulo Azure PowerShell. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Consulte [o módulo Azure PowerShell](/powershell/azure/install-az-ps) para obter informações de instalação e atualização.

Finalmente, se estiver a executar o PowerShell localmente, também terá de `Connect-AzAccount` correr. Este comando cria uma ligação com O Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Criar um grupo de recursos e uma rede virtual

Há um punhado de passos que você tem que percorrer para obter o seu grupo de recursos e rede virtual configurados.

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar uma rede virtual, tem de criar um grupo de recursos para acolher a rede virtual. Criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *leste:*

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Criar uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* na localização *EastUS:*

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

O Azure implementa recursos para uma sub-rede dentro de uma rede virtual, por isso é necessário criar uma sub-rede. Criar uma configuração de sub-rede nomeada *predefinição* com [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associar a sub-rede à rede virtual

Pode escrever a configuração da sub-rede para a rede virtual com [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Este comando cria a sub-rede:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie o primeiro VM com [New-AzVM](/powershell/module/az.compute/new-azvm). Quando se dirige o próximo comando, é-nos solicitado credenciais. Introduza um nome de utilizador e senha para o VM:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

A `-AsJob` opção cria o VM em segundo plano. Pode continuar até ao próximo passo.

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
> Não continue com o próximo passo até que Azure termine.  Saberá que é feito quando voltar a produção para o PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Utilize [o Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para devolver o endereço IP público de um VM. Este exemplo devolve o endereço IP público do *myVm1* VM:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Abra um pedido de comando no seu computador local. Execute o comando `mstsc`. `<publicIpAddress>`Substitua-o pelo endereço IP público devolvido do último passo:

> [!NOTE]
> Se estiver a executar estes comandos a partir de uma solicitação PowerShell no seu computador local, e estiver a utilizar a versão 1.0 ou posterior do módulo Az PowerShell, pode continuar nessa interface.

```cmd
mstsc /v:<publicIpAddress>
```
1. Se lhe for pedido, selecione **Ligar**.

1. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM.

    > [!NOTE]
    > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

1. Selecione **OK**.

1. Pode receber um aviso de certificado. Se o fizer, selecione **Sim** ou **Continue**.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

1. No Ambiente de Trabalho Remoto do *myVm1,* abra o PowerShell.

1. Introduza `ping myVm2`.

    Vai ter algo assim de volta.

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

    O ping falha, porque utiliza o Protocolo de Mensagem de Controlo de Internet (ICMP). Por predefinição, o ICMP não é permitido através da sua firewall do Windows.

1. Para permitir que *o myVm2* ping *myVm1* em um passo posterior, insira este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Este comando permite que o ICMP entre na firewall do Windows.

1. Feche a ligação de ambiente de trabalho remoto à *myVm1*.

1. Repita os passos em [Ligar a um VM da internet](#connect-to-a-vm-from-the-internet). Desta vez, ligue-se ao *myVm2*.

1. Numa linha de comandos na VM *myVm2*, introduza `ping myvm1`.

    Vai ter algo assim de volta.

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

O Azure permite uma comunicação privada sem restrições entre máquinas virtuais. Por predefinição, o Azure só permite ligações remotas de ambiente de trabalho ao Windows VMs a partir da internet. Para saber mais sobre a configuração de diferentes tipos de comunicações de rede VM, aceda ao tutorial de tráfego da [rede Filter.](tutorial-filter-network-traffic.md)
