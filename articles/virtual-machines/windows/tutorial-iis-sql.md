---
title: Tutorial - criar VMs a executar um SQL, IIS, .NET pilha no Azure | Documentos da Microsoft
description: Neste tutorial, vai aprender a instalar a pilha do Azure SQL, IIS, .NET numa máquina virtual do Windows no Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 770fa89bc08b448482b4a74d21ee307df8a9f836
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167920"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Tutorial: Instalar o SQL, IIS, .NET da pilha numa VM do Windows com o Azure PowerShell

Neste tutorial, vamos instalar um .NET de SQL, IIS, pilha com o Azure PowerShell. Este pilha consiste em duas VMs com o Windows Server 2016, uma com o IIS e .NET e a outra com o SQL Server.

> [!div class="checklist"]
> * Criar uma VM 
> * Instalar o IIS e o SDK de .NET Core na VM
> * Criar uma VM a executar o SQL Server
> * Instalar a extensão do SQL Server

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-an-iis-vm"></a>Criar uma VM de IIS 

Neste exemplo, utilizamos [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet no PowerShell Cloud Shell para criar rapidamente uma VM do Windows Server 2016 e, em seguida, instalar o IIS e o .NET Framework. AS VMs do IIS e SQL partilham um grupo de recursos e a rede virtual, pelo que iremos criar variáveis para esses nomes.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Instalar o IIS e o .NET framework usando a extensão de script personalizado com o [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) cmdlet.

```azurepowershell-interactive
Set-AzVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Criar outra sub-rede

Crie uma segunda sub-rede para a VM do SQL. Obtenha a vNet com [Get-AzVirtualNetwork]{/powershell/module/az.network/get-azvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Criar uma configuração para a sub-rede com [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Atualizar a vNet com a nova utilizando de informações de sub-rede [AzVirtualNetwork de conjunto](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>VM do Azure SQL

Utilize uma imagem do Azure Marketplace pré-configurada de um servidor SQL para criar a VM do SQL. Ao criar a VM pela primeira vez, instalamos então a Extensão do SQL Server na VM. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Uso [Set-AzVMSqlServerExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsqlserverextension) para adicionar o [extensão do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) à VM do SQL.

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, instalou a pilha SQL&#92;IIS&#92;.NET com o Azure PowerShell. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM 
> * Instalar o IIS e o SDK de .NET Core na VM
> * Criar uma VM a executar o SQL Server
> * Instalar a extensão do SQL Server

Avance para o tutorial seguinte para aprender a proteger o servidor Web IIS com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor Web IIS com certificados SSL](tutorial-secure-web-server.md)

