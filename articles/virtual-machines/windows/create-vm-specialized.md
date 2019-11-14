---
title: Criar uma VM do Windows de um VHD especializado no Azure
description: Crie uma nova VM do Windows anexando um disco gerenciado especializado como o disco do sistema operacional usando o modelo de implantação do Gerenciador de recursos.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: ac18056f9bfdf22c55b5effac810b8c24ab4d81d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033863"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Criar uma VM do Windows a partir de um disco especializado usando o PowerShell

Crie uma nova VM anexando um disco gerenciado especializado como o disco do sistema operacional. Um disco especializado é uma cópia de um VHD (disco rígido virtual) de uma VM existente que contém as contas de usuário, aplicativos e outros dados de estado da VM original. 

Quando você usa um VHD especializado para criar uma nova VM, a nova VM retém o nome do computador da VM original. Outras informações específicas do computador também são mantidas e, em alguns casos, essas informações duplicadas podem causar problemas. Ao copiar uma VM, lembre-se de quais tipos de informações específicas do computador seus aplicativos dependem.

Você tem várias opções:
* [Use um disco gerenciado existente](#option-1-use-an-existing-disk). Essa opção será útil se você tiver uma VM que não está funcionando corretamente. Você pode excluir a VM e, em seguida, reutilizar o disco gerenciado para criar uma nova VM. 
* [Carregar um VHD](#option-2-upload-a-specialized-vhd) 
* [Copiar uma VM do Azure existente usando instantâneos](#option-3-copy-an-existing-azure-vm)

Você também pode usar o portal do Azure para [criar uma nova VM de um VHD especializado](create-vm-specialized-portal.md).

Este artigo mostra como usar discos gerenciados. Se você tiver uma implantação herdada que requer o uso de uma conta de armazenamento, consulte [criar uma VM de um VHD especializado em uma conta de armazenamento](sa-create-vm-specialized.md).

Recomendamos que você limite o número de implantações simultâneas a 20 VMs de um único VHD ou instantâneo. 

## <a name="option-1-use-an-existing-disk"></a>Opção 1: usar um disco existente

Se você tiver uma VM que você excluiu e quiser reutilizar o disco do sistema operacional para criar uma nova VM, use [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Agora você pode anexar esse disco como o disco do sistema operacional a uma [nova VM](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Opção 2: carregar um VHD especializado

Você pode carregar o VHD de uma VM especializada criada com uma ferramenta de virtualização local, como o Hyper-V, ou uma VM exportada de outra nuvem.

### <a name="prepare-the-vm"></a>Preparar a VM
Use o VHD como está para criar uma nova VM. 
  
  * [Preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalizar a VM usando Sysprep.
  * Remova quaisquer ferramentas de virtualização de convidado e agentes que estejam instalados na VM (como ferramentas do VMware).
  * Verifique se a VM está configurada para obter o endereço IP e as configurações de DNS do DHCP. Isso garante que o servidor obtenha um endereço IP dentro da rede virtual quando ele for iniciado. 


### <a name="upload-the-vhd"></a>Carregar o VHD

Agora você pode carregar um VHD diretamente em um disco gerenciado. Para obter instruções, consulte [carregar um VHD no Azure usando Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Opção 3: copiar uma VM do Azure existente

Você pode criar uma cópia de uma VM que usa discos gerenciados tirando um instantâneo da VM e, em seguida, usando esse instantâneo para criar um novo disco gerenciado e uma nova VM.

Se você quiser copiar uma VM existente para outra região, talvez queira usar azcopy para criar [uma cópia de um disco em outra região](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Tirar um instantâneo do disco do sistema operacional

Você pode tirar um instantâneo de uma VM inteira (incluindo todos os discos) ou de apenas um único disco. As etapas a seguir mostram como tirar um instantâneo apenas do disco do sistema operacional de sua VM com o cmdlet [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) . 

Primeiro, defina alguns parâmetros. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Obter o objeto da VM.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Obtenha o nome do disco do sistema operacional.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Crie a configuração de instantâneo. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Tirar o instantâneo.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Para usar esse instantâneo para criar uma VM que precisa ser de alto desempenho, adicione o parâmetro `-AccountType Premium_LRS` ao comando New-AzSnapshotConfig. Esse parâmetro cria o instantâneo para que ele seja armazenado como um disco gerenciado Premium. Os Managed Disks Premium são mais caros do que o padrão, portanto, certifique-se de que você precisará de Premium antes de usar esse parâmetro.

### <a name="create-a-new-disk-from-the-snapshot"></a>Criar um novo disco a partir do instantâneo

Crie um disco gerenciado com base no instantâneo usando [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Este exemplo usa *myOSDisk* para o nome do disco.

Crie um novo grupo de recursos para a nova VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Defina o nome do disco do sistema operacional. 

```powershell
$osDiskName = 'myOsDisk'
```

Crie o disco gerenciado.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Criar a nova VM 

Criar rede e outros recursos de VM a serem usados pela nova VM.

### <a name="create-the-subnet-and-virtual-network"></a>Criar a sub-rede e a rede virtual

Crie a [rede virtual](../../virtual-network/virtual-networks-overview.md) e a sub-rede para a VM.

1. Crie a sub-rede. Este exemplo cria uma sub-rede chamada *mysubnet*, no grupo de recursos *myDestinationResourceGroup*, e define o prefixo de endereço de sub-rede como *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Criar a rede virtual. Este exemplo define o nome da rede virtual como *myVnetName*, o local para o *oeste dos EUA*e o prefixo de endereço para a rede virtual como *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para poder entrar em sua VM com o protocolo RDP (Remote Desktop Protocol), você precisará ter uma regra de segurança que permita o acesso RDP na porta 3389. Em nosso exemplo, o VHD para a nova VM foi criado a partir de uma VM especializada existente, para que você possa usar uma conta que existia na máquina virtual de origem para RDP.

Este exemplo define o nome do grupo de segurança de rede (NSG) como *myNsg* e o nome da regra RDP como *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Para obter mais informações sobre pontos de extremidade e regras de NSG, consulte [abrindo portas para uma VM no Azure usando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e uma NIC
Para habilitar a comunicação com a máquina virtual na rede virtual, você precisará de um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e de uma interface de rede.

1. Crie o IP público. Neste exemplo, o nome do endereço IP público é definido como *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Crie a NIC. Neste exemplo, o nome da NIC é definido como *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Definir o nome e o tamanho da VM

Este exemplo define o nome da VM como *myVM* e o tamanho da vm como *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Adicionar a NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Adicionar o disco do sistema operacional 

Adicione o disco do sistema operacional à configuração usando [set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). Este exemplo define o tamanho do disco para *128 GB* e anexa o disco gerenciado como um disco do sistema operacional *Windows* .
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Concluir a VM 

Crie a VM usando [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) com as configurações que acabamos de criar.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Se esse comando for bem-sucedido, você verá uma saída como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verifique se a VM foi criada
Você deve ver a VM recém-criada na [portal do Azure](https://portal.azure.com) em **procurar** > **máquinas virtuais**ou usando os comandos do PowerShell a seguir.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Entre em sua nova máquina virtual. Para obter mais informações, consulte [como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md).

