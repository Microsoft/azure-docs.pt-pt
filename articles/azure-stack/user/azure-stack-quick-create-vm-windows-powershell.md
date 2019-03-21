---
title: Criar uma máquina virtual do Windows com o PowerShell no Azure Stack | Documentos da Microsoft
description: Crie uma máquina virtual do Windows com o PowerShell no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: f6c8966582c0c5ae31818ce9d88b511cef811328
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099564"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Início rápido: criar uma máquina virtual do Windows Server com o PowerShell no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode criar uma máquina virtual do Windows Server 2016 com o Azure Stack do PowerShell. Siga os passos neste artigo para criar e utilizar uma máquina virtual. Este artigo também dá-lhe os passos para:

* Ligar à máquina virtual com um cliente remoto.
* Instalar o servidor web IIS e ver a home page do padrão.
* Limpe seus recursos.

> [!NOTE]
>  Pode executar os passos descritos neste artigo do Development Kit do Azure Stack ou de um cliente externo com base no Windows, se estiver ligado através de uma VPN.

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que o operador do Azure Stack adicionou a **Windows Server 2016** imagem para o mercado do Azure Stack.

* O Azure Stack requer uma versão específica do Azure PowerShell para criar e gerir os recursos. Se não tiver configurado para o Azure Stack do PowerShell, siga os passos para [instalar](azure-stack-powershell-install.md) PowerShell.

* Com o Azure Stack do PowerShell configurar, terá de ligar ao seu ambiente do Azure Stack. Para obter instruções, consulte [ligue-se ao Azure Stack com o PowerShell como um utilizador](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico em que o Azure Stack os recursos são implementados e geridos. A partir de seu kit de desenvolvimento ou o sistema integrado do Azure Stack, execute o seguinte bloco de código para criar um grupo de recursos. Valores são atribuídos para todas as variáveis neste documento, pode utilizar estes valores ou atribuir novos valores.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Criar recursos de armazenamento

Crie uma conta de armazenamento e um contentor de armazenamento para armazenar a imagem do Windows Server 2016.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>Criar recursos de rede

Crie uma rede virtual, uma sub-rede e um endereço IP público. Estes recursos são utilizados para fornecer conectividade de rede para a máquina virtual.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Crie um grupo de segurança de rede e uma regra de grupo de segurança de rede

O grupo de segurança de rede protege a máquina virtual utilizando regras de entrada e saídas. Permite criar uma regra de entrada para a porta 3389 para permitir ligações de ambiente de trabalho remoto de entrada e de uma regra de entrada para a porta 80 para permitir tráfego de entrada web.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Criar uma placa de rede para a máquina virtual

A placa de rede liga a máquina virtual a uma sub-rede, um grupo de segurança de rede e um endereço IP público.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Criar uma configuração da máquina virtual. Esta configuração inclui as definições utilizadas quando implementar a máquina virtual. Por exemplo: as credenciais, o tamanho e a imagem de máquina virtual.

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

Remotamente na máquina virtual que criou no passo anterior, terá do endereço IP público. Execute o seguinte comando para obter o endereço IP público da máquina virtual:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

Utilize o seguinte comando para criar uma sessão de ambiente de trabalho remoto com a máquina virtual. Substitua o endereço IP pelo publicIPAddress da máquina virtual. Quando lhe for pedido, introduza o nome de utilizador e palavra-passe que utilizou ao criar a máquina virtual.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalar o IIS através do PowerShell

Agora que iniciou sessão na VM do Azure, pode utilizar uma única linha do PowerShell para instalar o IIS e ativar a regra de firewall local para permitir o tráfego da Web. Abra uma janela do PowerShell e execute o comando seguinte:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Ver a página de boas-vindas do IIS

Com o IIS instalado e com a porta 80 aberta na sua VM, pode utilizar um browser à sua escolha para ver a página de boas-vindas do IIS predefinida. Utilize o *publicIpAddress* documentadas na secção anterior para visitar a página predefinida.

![Site predefinido do IIS](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-virtual-machine"></a>Eliminar a máquina virtual

Quando já não for necessário, utilize o seguinte comando para remover o grupo de recursos que contém a máquina virtual e respetivos recursos relacionados:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou uma máquina de virtual do Windows simples. Para saber mais sobre as máquinas virtuais do Azure Stack, avance para [considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md).
