---
title: Diferenças e considerações para o Managed Disks no Azure Stack | Documentos da Microsoft
description: Saiba mais sobre as diferenças e considerações ao trabalhar com os Managed Disks no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 02/26/2019
ms.openlocfilehash: 28210048cd007fc10dcd4cf5e92577cbd121e2a3
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368277"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Pilha Managed Disks do Azure: diferenças e considerações

Este artigo resume as diferenças conhecidas entre [Managed Disks do Azure Stack](azure-stack-manage-vm-disks.md) e [Managed Disks do Azure](../../virtual-machines/windows/managed-disks-overview.md). Para saber mais sobre das principais diferenças entre o Azure Stack e o Azure, consulte a [considerações da chave](azure-stack-considerations.md) artigo.

Discos geridos simplifica a gestão de discos para IaaS VMs ao gerir o [contas de armazenamento](../azure-stack-manage-storage-accounts.md) associadas aos discos VM.

> [!Note]  
> Discos geridos no Azure Stack está disponível no 1808 update. Ele é habilitado por padrão durante a criação de máquinas virtuais através do portal do Azure Stack do 1811 update.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Referência rápida: Gerido diferenças de disco

| Funcionalidade | (Global) do Azure | Azure Stack |
| --- | --- | --- |
|Encriptação para dados Inativos |Encriptação do serviço de armazenamento do Azure (SSE), Azure Disk Encryption (ADE)     |Encriptação de AES de 128 bits do BitLocker      |
|Imagem          | Suporte de imagem personalizada gerida |Suportadas|
|Opções de cópia de segurança |Suporta o serviço de cópia de segurança do Azure |Ainda não é suportado |
|Opções de recuperação após desastre |Suporte do Azure Site Recovery |Ainda não é suportado|
|Tipos de disco     |Premium SSD e Standard HDD SSD Standard (pré-visualização) |Premium SSD, HDD padrão |
|Discos Premium  |Totalmente suportado |Pode ser aprovisionado, mas sem limite de desempenho ou garantir  |
|Premium disks IOPs  |Depende do tamanho do disco  |2300 IOPs por disco |
|Débito de discos Premium |Depende do tamanho do disco |145 MB por segundo por disco |
|Tamanho do disco  |Azure Premium Disk: P4 (32 GiB) para P80 (32 TiB)<br>Azure Standard SSD Disk: E10 (128 GiB) para E80 (32 TiB)<br>Disco HDD padrão do Azure: S4 (32 GiB) para S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GiB |
|Cópia do instantâneo de discos|Tire um instantâneo do Azure discos geridos anexados a uma VM em execução suportada|Ainda não é suportado |
|Análise de desempenho de discos |Agregar as métricas e por métrica de disco suportada |Ainda não é suportado |
|Migração      |Fornecer ferramenta para migrar de não geridos do Azure Resource Manager VMs existentes sem a necessidade de recriar a VM  |Ainda não é suportado |

> [!NOTE]  
> IOPs de discos geridos e o débito no Azure Stack é um número de limite, em vez de um número aprovisionado, que pode ser afetado pelo hardware e cargas de trabalho em execução no Azure Stack.

## <a name="metrics"></a>Métricas

Também existem diferenças com métricas de armazenamento:

- Com o Azure Stack, os dados de transação nas métricas de armazenamento não distinguem largura de banda de rede internos ou externos.
- Dados de transação de pilha do Azure nas métricas de armazenamento não incluem o acesso à máquina virtual para os discos montados.

## <a name="api-versions"></a>Versões da API

Pilha de Managed Disks do Azure suporta as seguintes versões de API:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Converter para discos geridos

Pode utilizar o seguinte script para converter uma VM atualmente aprovisionada da não geridos para discos geridos. Substitua os marcadores de posição pelos seus próprios valores:

```powershell
$subscriptionId = 'subid'

# The name of your resource group
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk is located.
# The location should be the same as the location of the storage account in which VHD file is stored.
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>Imagens gerenciadas

O Azure Stack oferece suporte ao *geridos imagens*, que permitem a criação de um objeto de imagem gerida numa VM generalizada (ambos geridos e não geridos) que só pode criar gerida disco VMs no futuro. Imagens gerenciadas ativar dois cenários a seguir:

- Tem generalizada VMs não geridas e quiser utilizar discos geridos a partir de agora.
- Tem uma VM generalizada gerida e gostaria de criar vários, semelhante de VMs geridas.

### <a name="step-1-generalize-the-vm"></a>Passo 1: Generalizar a VM
Para Windows, siga a secção "Generalize o VM do Windows com o Sysprep" aqui: https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep Para o Linux, siga o passo 1 aqui: https://docs.microsoft.com/en-us/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm 

Nota: Não se esqueça de generalizar a VM. Criar uma VM a partir de uma imagem que não tenha sido generalizada corretamente levará a um erro de VMProvisioningTimeout.

### <a name="step-2-create-the-managed-image"></a>Passo 2: Criar a imagem gerida
Pode utilizar o portal, o powershell ou a cli para fazer isso. Siga o documento do Azure aqui: https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource

### <a name="step-3-choose-the-use-case"></a>Passo 3: Escolha o caso de utilização:
#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Caso 1: Migrar VMs não geridas para discos geridos
Não se esqueça de generalizar a VM corretamente antes de efetuar este passo. Post generalização, esta VM não pode ser utilizado futher. Criar uma VM a partir de uma imagem que não tenha sido generalizada corretamente levará a um erro de VMProvisioningTimeout. 

Siga as instruções [aqui](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account) para criar uma imagem gerida a partir de um VHD generalizado numa conta de armazenamento. Esta imagem pode ser utilizada para criar VMs gerenciadas no futuro.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Caso 2: Criar a VM gerida de imagem gerida com o Powershell

Depois de criar uma imagem gerida existente de um disco de VM com o script [aqui](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell) , o script de exemplo seguinte cria uma VM do Linux semelhante a partir de um objeto de imagem existente:

Módulo do powershell do Azure Stack 1.7.0 ou acima: Siga as instruções [aqui](../../virtual-machines/windows/create-vm-generalized-managed.md) 

Módulo do powershell do Azure Stack 1.6.0 ou abaixo:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename
# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Também pode utilizar o portal para criar uma VM a partir de uma imagem gerida. Para obter mais informações, veja o Azure managed artigos de imagem [criar uma imagem gerida de uma VM generalizada no Azure](../../virtual-machines/windows/capture-image-resource.md) e [criar uma VM a partir de uma imagem gerida](../../virtual-machines/windows/create-vm-generalized-managed.md).

## <a name="configuration"></a>Configuração

Depois de aplicar o 1808 atualizar ou posterior, tem de efetuar a seguinte configuração antes de utilizar discos geridos:

- Se uma subscrição tiver sido criada antes da atualização de 1808, siga os passos para atualizar a subscrição abaixo. Caso contrário, a implementação de VMs nesta subscrição poderá falhar com uma mensagem de erro "Erro interno no Gestor de discos."
   1. No portal do inquilino, aceda a **subscrições** e encontrar a subscrição. Clique em **fornecedores de recursos**, em seguida, clique em **Microsoft. Compute**e, em seguida, clique em **voltar a registar**.
   2. Na mesma subscrição, aceda a **controlo de acesso (IAM)** e certifique-se de que **disco gerido do Azure Stack –** está listado.
- Se utilizar um ambiente multi-inquilino, peça ao seu operador da cloud (que pode ser na sua organização ou do fornecedor de serviços) para reconfigurar cada um dos seus diretórios de convidado seguindo os passos na [este artigo](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). Caso contrário, a implementação de VMs numa assinatura associada esse diretório de convidado poderá falhar com uma mensagem de erro "Erro interno no Gestor de discos."

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre as máquinas virtuais do Azure Stack](azure-stack-compute-overview.md)
