---
title: Rotear o tráfego de rede Azure PowerShell | Microsoft Docs
description: Neste artigo, saiba como rotear o tráfego de rede com uma tabela de rotas usando o PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 986371e479f7718fff2e1699401987cb0ca8f623
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163985"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Rotear o tráfego de rede com uma tabela de rotas usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Azure encaminha automaticamente o tráfego entre todas as sub-redes numa rede virtual, por predefinição. Pode criar as sua próprias rotas para substituir o encaminhamento predefinido do Azure. A capacidade de criar rotas personalizadas é útil se, por exemplo, pretender encaminhar o tráfego entre sub-redes através de uma aplicação virtual de rede (NVA). Neste artigo, vai aprender a:

* Criar uma tabela de rotas
* Criar uma rota
* Criar uma rede virtual com várias sub-redes
* Associar uma tabela de rotas a uma sub-rede
* Criar uma NVA que encaminha o tráfego
* Implementar máquinas virtuais (VMs) em sub-redes diferentes
* Encaminhar o tráfego de uma sub-rede para outra através de uma NVA

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o Azure PowerShell módulo versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Antes de criar uma tabela de rotas, crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group para todos os recursos criados neste artigo.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Crie uma tabela de rotas com [New-AzRouteTable](/powershell/module/az.network/new-azroutetable). O exemplo a seguir cria uma tabela de rotas chamada *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Criar uma rota

Crie uma rota recuperando o objeto de tabela de rotas com [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable), crie uma rota com [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig)e, em seguida, grave a configuração de rota na tabela de rotas com [set-AzRouteTable](/powershell/module/az.network/set-azroutetable).

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Antes de poder associar uma tabela de rotas a uma sub-rede, você precisa criar uma rede virtual e uma sub-rede. Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVirtualNetwork* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Crie três sub-redes criando três configurações de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo a seguir cria três configurações de sub-rede para sub-redes *públicas*, *privadas*e *DMZ* :

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Grave as configurações de sub-rede na rede virtual com [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), que cria as sub-redes na rede virtual:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Associe a tabela de rotas *myRouteTablePublic* à sub-rede *pública* com [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) e, em seguida, grave a configuração de sub-rede na rede virtual com [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork).

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>Criar uma NVA

Uma NVA é uma VM que executa uma função de rede, como encaminhamento, proteção com firewalls ou otimização de WAN.

Antes de criar uma VM, crie uma interface de rede.

### <a name="create-a-network-interface"></a>Criar uma interface de rede

Antes de criar uma interface de rede, você precisa recuperar a ID de rede virtual com [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork), depois a ID de sub-rede com [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig). Crie uma interface de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) na sub-rede *DMZ* com encaminhamento IP habilitado:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Criar uma VM

Para criar uma VM e anexar uma interface de rede existente a ela, você deve primeiro criar uma configuração de VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). A configuração inclui a interface de rede criada na etapa anterior. Quando um nome de usuário e uma senha forem solicitados, selecione o nome e a senha que você deseja fazer logon na VM.

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

Crie a VM usando a configuração de VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM chamada *myVmNva*.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

A opção `-AsJob` cria a VM em segundo plano, para que você possa continuar para a próxima etapa.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual para que você possa validar que o tráfego da sub-rede *pública* seja roteado para a sub-rede *privada* por meio da solução de virtualização de rede em uma etapa posterior.

Crie uma VM na sub-rede *pública* com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM chamada *myVmPublic* na sub-rede *pública* da rede virtual *myVirtualNetwork* .

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Crie uma VM na sub-rede *privada* .

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

A criação da VM demora alguns minutos. Não continue com a próxima etapa até que a VM seja criada e o Azure retorne a saída para o PowerShell.

## <a name="route-traffic-through-an-nva"></a>Encaminhar o tráfego através de uma NVA

Use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para retornar o endereço IP público da VM *myVmPrivate* . O exemplo a seguir retorna o endereço IP público da VM *myVmPrivate* :

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Use o comando a seguir para criar uma sessão de área de trabalho remota com a VM *myVmPrivate* do computador local. Substitua `<publicIpAddress>` pelo endereço IP devolvido no comando anterior.

```
mstsc /v:<publicIpAddress>
```

Abra o ficheiro RDP transferido. Se lhe for pedido, selecione **Ligar**.

Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM (poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM) e, em seguida, selecione **OK**. Poderá receber um aviso de certificado durante o processo de início de sessão. Selecione **Sim** para continuar a ligação.

Em uma etapa posterior, o comando `tracert.exe` é usado para testar o roteamento. O tracert usa o protocolo ICMP, que é negado por meio do firewall do Windows. Introduza o seguinte comando do PowerShell na VM *myVmPrivate* para ativar o ICMP através da Firewall do Windows:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Embora a rota de rastreamento seja usada para testar o roteamento neste artigo, não é recomendável permitir o ICMP através do firewall do Windows para implantações de produção.

Você habilitou o encaminhamento de IP no Azure para a interface de rede da VM em Habilitar encaminhamento de IP. Dentro da VM, é necessário que o sistema operativo, ou uma aplicação em execução na mesma, consiga também reencaminhar o tráfego de rede. Habilite o encaminhamento de IP no sistema operacional do *myVmNva*.

Em um prompt de comando na VM *myVmPrivate* , a área de trabalho remota para o *myVmNva*:

``` 
mstsc /v:myvmnva
```

Para ativar o reencaminhamento de IP no sistema operativo, introduza o seguinte comando no PowerShell da VM *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```

Reinicie a VM *myVmNva*, o que também desliga a sessão de ambiente de trabalho remoto.

Ainda ligado à VM *myVmPrivate*, crie uma sessão de ambiente de trabalho remoto para a VM *myVmPublic*, após a VM *myVmNva* ser reiniciada:

``` 
mstsc /v:myVmPublic
```

Introduza o seguinte comando do PowerShell na VM *myVmPublic* para ativar o ICMP através da Firewall do Windows:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Para testar o encaminhamento do tráfego de rede para a VM *myVmPrivate* a partir da VM *myVmPublic*, introduza o seguinte comando do PowerShell na VM *myVmPublic*:

```
tracert myVmPrivate
```

A resposta é semelhante ao seguinte exemplo:

```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:

1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4

Trace complete.
```

Pode ver que o primeiro salto é 10.0.2.4, que é o endereço IP privado da NVA. O segundo salto é 10.0.1.4, o endereço IP privado da VM *myVmPrivate*. A rota adicionada à tabela de rotas *myRouteTablePublic* e associada à sub-rede *Pública* fez com que o Azure encaminhe o tráfego através da NVA, em vez de diretamente para a sub-rede *Privada*.

Feche a sessão de ambiente de trabalho remoto para a VM *myVmPublic*, o que mantém a ligação à VM *myVmPrivate* ativa.

Para testar o encaminhamento do tráfego de rede para a VM *myVmPublic* a partir da VM *myVmPrivate*, introduza o seguinte comando numa linha de comandos na VM *myVmPrivate*:

```
tracert myVmPublic
```

A resposta é semelhante ao seguinte exemplo:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:

1     1 ms     1 ms     1 ms  10.0.0.4

Trace complete.
```

Pode ver que o tráfego é encaminhado diretamente da VM *myVmPrivate* para a VM *myVmPublic*. Por predefinição, o Azure encaminha o tráfego diretamente entre sub-redes.

Feche a sessão de ambiente de trabalho remoto para a VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você criou uma tabela de rotas e a associou a uma sub-rede. Você criou uma solução de virtualização de rede simples que encaminhava o tráfego de uma sub-rede pública para uma sub-rede privada. Implante uma variedade de soluções de virtualização de rede pré-configuradas que executam funções de rede, como firewall e otimização de WAN do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Para saber mais sobre o encaminhamento, veja [Descrição geral do encaminhamento](virtual-networks-udr-overview.md) e [Manage a route table](manage-route-table.md) (Gerir uma tabela de rotas).

Embora possa implementar muitos recursos do Azure dentro de uma rede virtual, não é possível implementar recursos de alguns serviços PaaS do Azure nas redes virtuais. Contudo, pode restringir o acesso aos recursos de alguns serviços PaaS do Azure ao tráfego apenas a partir de uma sub-rede de rede virtual. Para saber como, consulte [restringir o acesso à rede para recursos de PaaS](tutorial-restrict-network-access-to-resources-powershell.md).