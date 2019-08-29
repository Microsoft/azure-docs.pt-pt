---
title: Tutorial - Criar e gerir redes virtuais do Azure para VMs do Windows | Microsoft Docs
description: Neste tutorial, irá aprender a utilizar o Azure PowerShell para criar e gerir redes virtuais do Azure para máquinas virtuais do Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d5a780617b8f46c5ec5f00fbfc45b7d91c29a836
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100200"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Tutorial: Criar e gerenciar redes virtuais do Azure para máquinas virtuais do Windows com Azure PowerShell

As máquinas virtuais do Azure utilizam a rede do Azure para a comunicação de rede interna e externa. Este tutorial mostra a implementação de duas máquinas virtuais e a configuração da rede do Azure para essas VMs Os exemplos neste tutorial pressupõem que as VMs estão hospedando um aplicativo Web com um back-end de banco de dados, no entanto, um aplicativo não é implantado no tutorial. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma rede virtual e uma sub-rede
> * Crie um endereço IP público
> * Criar uma VM de front-end
> * Proteger o tráfego de rede
> * Criar a VM de back-end


## <a name="vm-networking-overview"></a>Descrição geral do funcionamento em rede de VMs

As redes virtuais do Azure permitem ligações de rede seguras entre as máquinas virtuais, a Internet e outros serviços do Azure, como a Base de Dados SQL do Azure. As redes virtuais são divididas em segmentos lógicos, chamados sub-redes. As sub-redes são utilizadas para controlar o fluxo de rede e como limite de segurança. Ao implementar uma VM, incluem, geralmente, uma interface de rede virtual, que é ligada a uma sub-rede.

Ao concluir este tutorial, pode ver estes recursos serem criados:

![Rede virtual com duas sub-redes](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - a rede virtual que as VMs utilizam para comunicar entre si e com a Internet.
- *myFrontendSubnet* - a sub-rede em *myVNet* que os recursos de front-end utilizam.
- *myPublicIPAddress* - o endereço IP público utilizado para aceder a *myFrontendVM* a partir da Internet.
- *myFrontendNic* - a interface de rede utilizada por *myFrontendVM* para comunicar com *myBackendVM*.
- *myFrontendVM* - a VM utilizada para comunicar entre a Internet e *myBackendVM*.
- *myBackendNSG* - o grupo de segurança de rede que controla a comunicação entre *myFrontendVM* e *myBackendVM*.
- *myBackendSubnet* - a sub-rede associada a *myBackendNSG* e utilizada pelos recursos de back-end.
- *myBackendNic* - a interface de rede utilizada por *myBackendVM* para comunicar com *myFrontendVM*.
- *myBackendVM* - A VM que utiliza a porta 1433 para comunicar com *myFrontendVM*.


## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.


## <a name="create-subnet"></a>Criar sub-rede 

Neste tutorial, é criada uma rede virtual individual com duas sub-redes. Uma sub-rede de front-end para alojar uma aplicação Web e uma sub-rede de back-end para alojar um servidor de bases de dados.

Para poder criar uma rede virtual, crie um grupo de recursos usando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um grupo de recursos denominado *myRGNetwork* na localização *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Crie uma configuração de sub-rede chamada *myFrontendSubnet* usando [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Crie também uma configuração de sub-rede denominada *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Criar a rede virtual

Crie uma VNET chamada *myVNet* usando *myFrontendSubnet* e *myBackendSubnet* usando [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Nesta fase, já está criada uma rede e segmentada em duas sub-redes, uma para os serviços de front-end e outra para os de back-end. Na secção seguinte, são criadas máquinas virtuais e ligadas a estas sub-redes.

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Os endereços IP públicos permitem que os recursos do Azure estejam acessíveis na Internet. O método de atribuição do endereço IP público pode ser configurado como dinâmico ou estático. Por predefinição, o endereço IP público é atribuído dinamicamente. Os endereços IP dinâmicos são libertados quando uma VM é desalocada. Este comportamento faz com que o endereço IP mude durante qualquer operação que inclua desalocar uma VM.

O método de alocação pode ser definido como estático, o que garante que o endereço IP permaneça atribuído a uma VM, mesmo durante um estado desalocado. Se você estiver usando um endereço IP estático, o próprio endereço IP não poderá ser especificado. Em vez disso, ele é alocado de um pool de endereços disponíveis.

Crie um endereço IP público denominado *myPublicIPAddress* usando [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress):

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Pode alterar o parâmetro -AllocationMethod para `Static` para atribuir um endereço IP público estático.

## <a name="create-a-front-end-vm"></a>Criar uma VM de front-end

Para uma VM comunicar numa rede virtual, necessita de uma interface de rede virtual (NIC). Crie uma NIC usando [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Defina o nome de utilizador e a palavra-passe necessários para a conta de administrador na VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Vai utilizar estas credenciais para ligar à VM nos passos adicionais:

```azurepowershell-interactive
$cred = Get-Credential
```

Crie as VMs usando [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Proteger o tráfego de rede

Os grupos de segurança de rede (NSG) contêm uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos ligados a Redes Virtuais do Azure (VNet). Os NSGs podem ser associados a sub-redes ou a interfaces de rede individuais. Um NSG está associado a uma interface de rede somente se aplica à VM associada. Quando um NSG é associado a uma sub-rede, as regras são aplicadas a todos os recursos ligados à mesma.

### <a name="network-security-group-rules"></a>Regras do grupo de segurança de rede

As regras do NSG definem as portas de rede através das quais o tráfego é permitido ou recusado. As regras podem incluir intervalos de endereços IP de origem e de destino, para que o tráfego seja controlado entre sistemas ou sub-redes específicos. As regras do NSG também incluem uma prioridade (entre 1 e 4096). As regras são avaliadas por ordem de prioridade. Uma regra com uma prioridade de 100 é avaliada antes de uma regra com prioridade de 200.

Todos os NSGs contêm um conjunto de regras predefinidas. As regras padrão não podem ser excluídas, mas como são atribuídas à prioridade mais baixa, elas podem ser substituídas pelas regras que você criar.

- **Rede virtual** - o tráfego que tem origem e termina numa rede virtual é permitido nas direções de entrada e de saída.
- **Internet** - o tráfego de saída é permitido, mas o tráfego de entrada é bloqueado.
- **Balanceador de carga** - permitir que o balanceador de carga do Azure sonde o estado de funcionamento das VMs e das instâncias de função. Se não estiver a utilizar um conjunto com balanceamento de carga, pode substituir esta regra.

### <a name="create-network-security-groups"></a>Criar grupos de segurança de rede

Crie uma regra de entrada chamada *myFrontendNSGRule* para permitir o tráfego da Web de entrada em *myFrontendVM* usando [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Pode limitar o tráfego interno para *myBackendVM* a partir de apenas *myFrontendVM* ao criar um NSG para a sub-rede de back-end. O exemplo seguinte cria uma regra de NSG denominada *myBackendNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Adicione um grupo de segurança de rede chamado *myFrontendNSG* usando [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Agora, adicione um grupo de segurança de rede chamado *myBackendNSG* usando New-AzNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Adicione os grupos de segurança de rede às sub-redes:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Criar uma VM de back-end

A forma mais fácil de criar a VM de back-end para este tutorial é utilizar uma imagem do SQL Server. Este tutorial apenas cria a VM com o servidor de base de dados, mas não fornece informações sobre como aceder à base de dados.

Crie *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Defina o nome de utilizador e a palavra-passe necessários para a conta de administrador na VM com Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Crie *myBackendVM*.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

A imagem neste exemplo tem SQL Server instalado, mas não é usada neste tutorial. Ele está incluído para mostrar como você pode configurar uma VM para manipular o tráfego da Web e uma VM para lidar com o gerenciamento de banco de dados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou e protegeu redes do Azure relacionadas com máquinas virtuais. 

> [!div class="checklist"]
> * Criar uma rede virtual e uma sub-rede
> * Crie um endereço IP público
> * Criar uma VM de front-end
> * Proteger o tráfego de rede
> * Criar uma VM de back-end

Avance para o tutorial seguinte para saber mais sobre a monitorização da proteção de dados nas máquinas virtuais com o Azure Backup.

> [!div class="nextstepaction"]
> [Fazer uma cópia de segurança de máquinas virtuais do Windows no Azure](./tutorial-backup-vms.md)
