---
title: Criar uma rede virtual - quickstart - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Neste arranque rápido, cria-se uma rede virtual utilizando o portal Azure. Uma rede virtual permite que os recursos do Azure comuniquem uns com os outros e com a internet.
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b27f050d3d37daab05e8c5125d6b75a6bb4dea50
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199038"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Início Rápido :criar uma rede virtual com o PowerShell

Uma rede virtual permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem-se em privado entre si e com a internet. 

Neste início rápido, vai aprender a criar uma rede virtual. Depois de criar uma rede virtual, vai implementar duas VMs na rede virtual. Em seguida, conecta-se aos VMs a partir da internet e comunica-se em privado através da rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell instalado localmente ou Azure Cloud Shell

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Criar um grupo de recursos e uma rede virtual

Há um punhado de passos que você tem que percorrer para obter o seu grupo de recursos e rede virtual configurados.

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar uma rede virtual, tem de criar um grupo de recursos para acolher a rede virtual. Criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Este exemplo cria um grupo de recursos chamado **CreateVNetQS-rg** na localização **Eastus:**

```azurepowershell-interactive
$rg = @{
    Name = 'CreateVNetQS-rg'
    Location = 'EastUS'
}
New-AzResourceGroup @rg
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Criar uma rede virtual com [a New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Este exemplo cria uma rede virtual padrão chamada **myVNet** na localização **EastUS:**

```azurepowershell-interactive
$vnet = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

O Azure implementa recursos para uma sub-rede dentro de uma rede virtual, por isso é necessário criar uma sub-rede. Criar uma configuração de sub-rede nomeada **predefinição** com [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnet = @{
    Name = 'default'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet
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
$vm1 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM1'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm1 -AsJob
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
$vm2 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM2'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm2
```

Terá de criar outro utilizador e senha. Azure leva alguns minutos para criar o VM.

> [!IMPORTANT]
> Não continue com o próximo passo até que Azure termine.  Saberá que é feito quando voltar a produção para o PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Para obter o endereço IP público do VM, utilize [o Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress).

Este exemplo devolve o endereço IP público do **myVm1** VM:

```azurepowershell-interactive
$ip = @{
    Name = 'myVM1'
    ResourceGroupName = 'CreateVNetQS-rg'
}
Get-AzPublicIpAddress @ip | select IpAddress
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

1. No Ambiente de Trabalho Remoto do **myVm1,** abra o PowerShell.

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

1. Para permitir que **o myVm2** ping **myVm1** em um passo posterior, insira este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Este comando permite que o ICMP entre na firewall do Windows.

1. Feche a ligação de ambiente de trabalho remoto à **myVm1**.

1. Repita os passos em [Ligar a um VM da internet](#connect-to-a-vm-from-the-internet). Desta vez, ligue-se ao **myVm2**.

1. Numa linha de comandos na VM **myVm2**, introduza `ping myvm1`.

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

    Vai receber respostas da **myVm1** porque permitiu o ICMP através da firewall do Windows na VM **myVm1** num passo anterior.

1. Feche a ligação de ambiente de trabalho remoto à **myVm2**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar com a rede virtual e os VMs, utilize o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que tem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateVNetQS-rg' -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido: 

* Criou uma rede virtual padrão e dois VMs. 
* Ligou-se a um VM da internet e comunicou-se em privado entre os dois VMs.

A comunicação privada entre VMs é ilimitada numa rede virtual. 

Avance para o próximo artigo para saber mais sobre a configuração de diferentes tipos de comunicações de rede VM:
> [!div class="nextstepaction"]
> [Filtre o tráfego de rede](tutorial-filter-network-traffic.md)
