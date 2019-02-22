---
title: Encaminhar o tráfego de rede do Azure PowerShell | Documentos da Microsoft
description: Neste artigo, saiba como encaminhar o tráfego de rede com uma tabela de rotas com o PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ad09d41b004fe2b8a4090dce16a7a70f9c57b1f3
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651504"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Encaminhar o tráfego de rede com uma tabela de rotas com o PowerShell

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

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Antes de poder criar uma tabela de rotas, crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* para todos os recursos criados neste artigo.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Criar uma tabela de rotas com [New-AzRouteTable](/powershell/module/az.network/new-azroutetable). O exemplo seguinte cria uma tabela de rotas com o nome *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Criar uma rota

Criar uma rota ao obter o objeto de tabela de rota com [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable), criar uma rota com [adicionar AzRouteConfig](/powershell/module/az.network/add-azrouteconfig), em seguida, escreva a configuração de rota à tabela de rotas com [ Conjunto AzRouteTable](/powershell/module/az.network/set-azroutetable).

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

Antes de poder associar uma tabela de rotas a uma sub-rede, tem de criar uma rede virtual e uma sub-rede. Criar uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo seguinte cria uma rede virtual denominada *myVirtualNetwork* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Pode criar três sub-redes ao criar três configurações de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo seguinte cria três configurações de sub-rede da *pública*, *privada*, e *DMZ* sub-redes:

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

Escrever as configurações de sub-rede para a rede virtual com [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), que cria as sub-redes na rede virtual:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Associar o *myRouteTablePublic* tabela de rotas para o *público* sub-rede com [conjunto AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) e, em seguida, escreva a configuração de sub-rede para o rede virtual com [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork).

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

Antes de criar uma interface de rede, tem de obter o virtual de rede Id com o [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork), em seguida, o Id de sub-rede com [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig). Criar uma interface de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) no *DMZ* sub-rede com o reencaminhamento de IP ativado:

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

Para criar uma VM e expor uma interface de rede existente para o mesmo, tem primeiro de criar uma configuração de VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). A configuração inclui a interface de rede que criou no passo anterior. Quando lhe for pedido um nome de utilizador e palavra-passe, selecione o nome de utilizador e palavra-passe que pretende iniciar sessão na VM com o.

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

Criar a VM com a configuração de VM com o [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo seguinte cria uma VM com o nome *myVmNva*.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

O `-AsJob` opção cria a VM em segundo plano, para poder continuar para o passo seguinte.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual para que possa validar que o tráfego dos *pública* sub-rede é encaminhada para o *privada* sub-rede através do dispositivo de rede virtual num passo posterior.

Criar uma VM no *pública* sub-rede com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo seguinte cria uma VM com o nome *myVmPublic* no *público* sub-rede do *myVirtualNetwork* rede virtual.

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

Criar uma VM no *privada* sub-rede.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

A criação da VM demora alguns minutos. Não continue para o passo seguinte até que a VM é criada e Azure devolve saído para o PowerShell.

## <a name="route-traffic-through-an-nva"></a>Encaminhar o tráfego através de uma NVA

Uso [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para devolver o endereço IP público do *myVmPrivate* VM. O exemplo seguinte devolve o endereço IP público dos *myVmPrivate* VM:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Utilize o seguinte comando para criar uma sessão de área de trabalho remota com o *myVmPrivate* VM a partir do seu computador local. Substitua `<publicIpAddress>` pelo endereço IP devolvido no comando anterior.

```
mstsc /v:<publicIpAddress>
```

Abra o ficheiro RDP transferido. Se lhe for pedido, selecione **Ligar**.

Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM (poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM) e, em seguida, selecione **OK**. Poderá receber um aviso de certificado durante o processo de início de sessão. Selecione **Sim** para continuar a ligação.

Num passo posterior, o `tracert.exe` comando é utilizado para testar o encaminhamento. Tracert usa o controle de mensagem ICMP (Internet Protocol), que é negado através da Firewall do Windows. Introduza o seguinte comando do PowerShell na VM *myVmPrivate* para ativar o ICMP através da Firewall do Windows:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Apesar de encaminhamento de rastreio é utilizado para testar o encaminhamento neste artigo, não é recomendado permitir o ICMP através da Firewall do Windows para implementações de produção.

Ativou o reencaminhamento de IP no Azure para a interface de rede da VM em Ativar reencaminhamento de IP. Dentro da VM, é necessário que o sistema operativo, ou uma aplicação em execução na mesma, consiga também reencaminhar o tráfego de rede. Ativar o reencaminhamento de IP dentro do sistema operativo dos *myVmNva*.

Na linha de comandos no *myVmPrivate* VM, o ambiente de trabalho remoto para o *myVmNva*:

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
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
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

Quando já não for necessário, utilize [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos nele contidos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou uma tabela de rotas e associou-o a uma sub-rede. Criou uma aplicação virtual de rede simples que encaminhar o tráfego de sub-rede público a uma sub-rede privada. Implementar uma variedade de aplicações virtuais de rede previamente configuradas que executam funções de rede, tais como firewall e otimização de WAN do [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Para saber mais sobre o encaminhamento, veja [Descrição geral do encaminhamento](virtual-networks-udr-overview.md) e [Manage a route table](manage-route-table.md) (Gerir uma tabela de rotas).

Embora possa implementar muitos recursos do Azure dentro de uma rede virtual, não é possível implementar recursos de alguns serviços PaaS do Azure nas redes virtuais. Contudo, pode restringir o acesso aos recursos de alguns serviços PaaS do Azure ao tráfego apenas a partir de uma sub-rede de rede virtual. Para saber como, veja [restringir o acesso à rede a recursos de PaaS](tutorial-restrict-network-access-to-resources-powershell.md).