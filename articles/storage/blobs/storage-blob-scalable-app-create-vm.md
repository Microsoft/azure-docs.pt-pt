---
title: Criar uma VM e uma conta de armazenamento para uma aplicação dimensionável no Azure | Microsoft Docs
description: Saiba como implementar uma VM para servir para executar uma aplicação dimensionável através do armazenamento de blobs do Azure
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: fdc88f4ba893eed516aeca174e68fdda12a42a0f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Criar uma máquina virtual e uma conta de armazenamento para uma aplicação dimensionável

Este tutorial é a primeira parte de uma série. Este tutorial mostra como implementar uma aplicação que carrega e transfere grandes quantidades de dados aleatórios com uma conta de armazenamento do Azure. Quando tiver terminado, terá uma aplicação de consola em execução numa máquina virtual na qual carrega e transfere grandes quantidades de dados para uma conta de armazenamento.

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar uma conta do Storage
> * Criar uma máquina virtual
> * Configurar uma extensão de script personalizado

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o módulo do Azure PowerShell versão 3.6 ou posterior. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzureRmAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Criar uma conta do Storage
 
O exemplo carrega 50 ficheiros grandes para um contentor de blobs numa conta de Armazenamento do Azure. Uma conta de armazenamento fornece um espaço de nomes exclusivo para armazenar e aceder aos objetos de dados de armazenamento do Azure. Crie uma conta de armazenamento no grupo de recursos que criou através do comando [New-AzureRmStorageAccount](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccount).

No comando seguinte, substitua o nome da conta de armazenamento de Blobs globalmente exclusivo onde vir o marcador de posição `<blob_storage_account>`.

```powershell-interactive
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Criar uma configuração da máquina virtual. Esta configuração inclui as definições que são utilizadas ao implementar a máquina virtual, como uma imagem de máquina virtual, o tamanho e a configuração da autenticação. Ao executar este passo, serão pedidas credenciais. Os valores que introduzir são configurados, como o nome de utilizador e a palavra-passe para a máquina virtual.

Criar a máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Implementar a configuração

Para este tutorial, existem pré-requisitos que têm de ser instalados na máquina virtual. A extensão de script personalizado serve para executar um script do PowerShell que conclui as seguintes tarefas:

> [!div class="checklist"]
> * Instalar o .NET core 2.0
> * Instalar o chocolatey
> * Instalar o GIT
> * Clonar o repositório de exemplo
> * Restaurar pacotes NuGet
> * Criar 50 ficheiros de 1 GB com dados aleatórios

Execute o seguinte cmdlet para finalizar a configuração da máquina virtual. Este passo demora entre 5 a 15 minutos.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzureRMVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Passos seguintes

Na primeira parte da série, aprendeu como criar uma conta de armazenamento, implementar uma máquina virtual e configurar a máquina virtual com os pré-requisitos necessários, tais como:

> [!div class="checklist"]
> * Criar uma conta do Storage
> * Criar uma máquina virtual
> * Configurar uma extensão de script personalizado

Avance para a segunda parte da série para carregar grandes quantidades de dados para uma conta de armazenamento através da repetição exponencial e do paralelismo.

> [!div class="nextstepaction"]
> [Carregar grandes quantidades de ficheiros grandes em paralelo para uma conta de armazenamento](storage-blob-scalable-app-upload-files.md)
