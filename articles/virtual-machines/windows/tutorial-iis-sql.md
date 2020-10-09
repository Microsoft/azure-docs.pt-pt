---
title: Tutorial - Criar VMs executando uma sql, IIS, .NET stack em Azure
description: Neste tutorial, vai aprender a instalar a pilha do Azure SQL, IIS, .NET numa máquina virtual do Windows no Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1c53194bd345c18ac582acd538f1e8f8e1e34d54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87027857"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Tutorial: Instale a pilha SQL, IIS, .NET num Windows VM com Azure PowerShell

Neste tutorial, instalamos uma stack SQL, IIS, .NET utilizando a Azure PowerShell. Este pilha consiste em duas VMs com o Windows Server 2016, uma com o IIS e .NET e a outra com o SQL Server.

> [!div class="checklist"]
> * Criar uma VM 
> * Instalar o IIS e o SDK de .NET Core na VM
> * Criar uma VM a executar o SQL Server
> * Instalar a extensão do SQL Server

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-an-iis-vm"></a>Criar um IIS VM 

Neste exemplo, utilizamos o cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm) na PowerShell Cloud Shell para criar rapidamente um VM Windows Server 2016 e, em seguida, instalar o IIS e o .NET Framework. AS VMs do IIS e SQL partilham um grupo de recursos e a rede virtual, pelo que iremos criar variáveis para esses nomes.


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

Instale o IIS e a estrutura .NET utilizando a extensão de script personalizada com o [cmdlet Set-AzVMExtension.](/powershell/module/az.compute/set-azvmextension)

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

Crie uma segunda sub-rede para a VM do SQL. Obtenha o vNet usando [Get-AzVirtualNetwork]{/powershell/module/az.network/get-azvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Crie uma configuração para a [sub-rede utilizando Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Atualizar o vNet com as novas informações da sub-rede utilizando [o Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)
   
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

Utilize [o Set-AzVMSqlServerExtension](/powershell/module/az.compute/set-azvmsqlserverextension) para adicionar a extensão do [SQL Server](../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md) ao SQL VM.

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, instalou a pilha SQL&#92;IIS&#92;.NET com o Azure PowerShell. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM 
> * Instalar o IIS e o SDK de .NET Core na VM
> * Criar uma VM a executar o SQL Server
> * Instalar a extensão do SQL Server

Avance para o próximo tutorial para aprender como proteger o servidor web do IIS com certificados TLS/SSL.

> [!div class="nextstepaction"]
> [Servidor web IIS seguro com certificados TLS/SSL](tutorial-secure-web-server.md)
