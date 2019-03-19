---
title: Criar uma VM do Windows a partir de um VHD especializado no Azure | Documentos da Microsoft
description: Crie uma nova VM do Windows ao anexar um disco gerido especializado como o disco do SO, utilizando o modelo de implementação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: cynthn
ms.openlocfilehash: 364dca8d7cab3698e501e74d0c2817aac1a36f88
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137982"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Criar uma VM do Windows a partir de um disco especializado ao utilizar o PowerShell

Crie uma nova VM ao anexar um disco gerido especializado, como o disco do SO. Um disco especializado é uma cópia de um disco rígido virtual (VHD) a partir de uma VM existente que contém as contas de utilizador, aplicativos e outros dados de estado da original VM. 

Quando utilizar um VHD especializado para criar uma nova VM, a nova VM retém o nome do computador da original VM. Outras informações específicas de computador também são mantidas e, em alguns casos, essas informações duplicadas podem causar problemas. Ao copiar uma VM, tenha em atenção que tipos de informações específicas de computador seus aplicativos dependem.

Tem várias opções:
* [Utilizar um disco gerido existente](#option-1-use-an-existing-disk). Esta opção é útil se tiver uma VM que não está a funcionar corretamente. Pode eliminar a VM e, em seguida, reutilizar o disco gerido para criar uma nova VM. 
* [Carregar um VHD](#option-2-upload-a-specialized-vhd) 
* [Copiar uma VM do Azure existente com instantâneos](#option-3-copy-an-existing-azure-vm)

Também pode utilizar o portal do Azure para [criar uma nova VM a partir de um VHD especializado](create-vm-specialized-portal.md).

Este artigo mostra-lhe como utilizar discos geridos. Se tiver uma implementação legada que requer a utilização de uma conta de armazenamento, consulte [criar uma VM a partir de um VHD especializado numa conta de armazenamento](sa-create-vm-specialized.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="option-1-use-an-existing-disk"></a>Opção 1: Utilizar um disco existente

Se tivesse uma VM que eliminou e quiser reutilizar o disco do SO para criar uma nova VM, utilize [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk?view=azurermps-6.8.1).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Agora pode anexar este disco como o disco do SO para uma [nova VM](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Opção 2: Carregar um VHD especializado

Pode carregar o VHD a partir de uma VM especializada criado com uma ferramenta de virtualização no local, como o Hyper-V ou numa VM exportada de outra cloud.

### <a name="prepare-the-vm"></a>Preparar a VM
Utilize o VHD como-é criar uma nova VM. 
  
  * [Preparar um VHD do Windows para carregar para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Isso não é possível** generalizar a VM através do Sysprep.
  * Remova todos os agentes instalados na VM (por exemplo, as ferramentas do VMware) e ferramentas de Virtualização do convidado.
  * Certifique-se de que a VM está configurada para obter o endereço IP e as definições de DNS de DHCP. Isto garante que o servidor obtém um endereço IP na rede virtual durante o arranque. 


### <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Precisará de uma conta de armazenamento no Azure para armazenar o VHD carregado. Pode utilizar uma conta de armazenamento existente ou crie um novo. 

Mostre as contas de armazenamento disponível.

```powershell
Get-AzStorageAccount
```

Para utilizar uma conta de armazenamento existente, avance para o [carregar o VHD](#upload-the-vhd-to-your-storage-account) secção.

Criar uma conta de armazenamento.

1. Terá o nome do grupo de recursos onde será criada a conta de armazenamento. Utilize Get-AzResourceGroup ver todos os grupos de recursos que estão na sua subscrição.
   
    ```powershell
    Get-AzResourceGroup
    ```

    Crie um grupo de recursos chamado *myResourceGroup* no *E.U.A. oeste* região.

    ```powershell
    New-AzResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Criar uma conta de armazenamento com o nome *mystorageaccount* no novo grupo de recursos, utilizando o [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) cmdlet.
   
    ```powershell
    New-AzStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD para a conta de armazenamento 
Utilize o [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) cmdlet para carregar o VHD para um contentor na conta de armazenamento. Este exemplo carrega o ficheiro *myVHD.vhd* de "discos rígidos de C:\Users\Public\Documents\Virtual\" para uma conta de armazenamento com o nome *mystorageaccount* no  *myResourceGroup* grupo de recursos. O ficheiro é armazenado num contentor com o nome *mycontainer* e o novo nome de ficheiro serão *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se os comandos são com êxito, obterá uma resposta que é semelhante a este:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Este comando pode demorar algum tempo a concluir, consoante a ligação de rede e o tamanho do ficheiro VHD.

### <a name="create-a-managed-disk-from-the-vhd"></a>Criar um disco gerido a partir de VHD

Criar um disco gerido a partir de um VHD especializado na sua conta de armazenamento, utilizando [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Este exemplo utiliza *myOSDisk1* para o nome do disco, coloca o disco num *Standard_LRS* armazenamento e utiliza *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* como o URI para o VHD de origem.

Crie um novo grupo de recursos para a nova VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Crie o novo disco de SO a partir do VHD carregado. 

```powershell
$sourceUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
$osDiskName = 'myOsDisk'
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>Opção 3: Copiar uma VM do Azure existente

Pode criar uma cópia de uma VM que utiliza discos geridos tirando um instantâneo da VM e, em seguida, ao utilizar esse instantâneo para criar um novo gerido disco e uma nova VM.


### <a name="take-a-snapshot-of-the-os-disk"></a>Tirar um instantâneo do disco do SO

Pode tirar um instantâneo de uma VM inteira (incluindo todos os discos) ou de apenas um único disco. Os passos seguintes mostram-lhe como tirar um instantâneo de apenas o disco do SO da VM com o [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) cmdlet. 

Em primeiro lugar, defina alguns parâmetros. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Obtenha o objeto VM.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Obtenha o nome do disco de SO.

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

Tire o instantâneo.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Para utilizar este instantâneo para criar uma VM que tem de ser de alto desempenho, adicione o parâmetro `-AccountType Premium_LRS` para o comando New-AzSnapshotConfig. Este parâmetro cria o instantâneo para que ele é armazenado como disco gerido Premium. Premium Managed Disks são mais caros do que no plano padrão, por isso, certifique-se de que precisará Premium antes de utilizar este parâmetro.

### <a name="create-a-new-disk-from-the-snapshot"></a>Criar um novo disco a partir do instantâneo

Criar um disco gerido a partir do instantâneo, utilizando [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Este exemplo utiliza *myOSDisk* para o nome do disco.

Crie um novo grupo de recursos para a nova VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Defina o nome do disco de SO. 

```powershell
$osDiskName = 'myOsDisk'
```

Crie o disco gerido.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Criar a nova VM 

Crie a rede e outros recursos da VM a ser utilizado pela VM nova.

### <a name="create-the-subnet-and-virtual-network"></a>Criar a sub-rede e rede virtual

Criar a [rede virtual](../../virtual-network/virtual-networks-overview.md) e a sub-rede para a VM.

1. Crie a sub-rede. Este exemplo cria uma sub-rede denominada *mySubNet*, no grupo de recursos *myDestinationResourceGroup*e define o prefixo de endereço de sub-rede para *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Criar a rede virtual. Este exemplo define o nome de rede virtual, como *myVnetName*, a localização para *E.U.A. oeste*e o prefixo de endereço para a rede virtual para *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para conseguir iniciar sessão na sua VM com o protocolo de ambiente de trabalho remoto (RDP), terá de ter uma regra de segurança que permite o acesso RDP na porta 3389. No nosso exemplo, o VHD para a nova VM foi criado a partir de uma VM especializada existente, pelo que pode utilizar uma conta que existiam na máquina de virtual de origem para RDP.

Este exemplo define o nome de grupo (NSG) de segurança de rede, como *myNsg* e o nome da regra RDP para *myRdpRule*.

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

Para obter mais informações sobre pontos de extremidade e as regras do NSG, consulte [abrir portas para uma VM no Azure com o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e a NIC
Para ativar a comunicação com a máquina virtual na rede virtual, será necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie o IP público. Neste exemplo, o nome do endereço IP público é definido como *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Criar a NIC. Neste exemplo, o nome do NIC está definido como *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Defina o nome da VM e o tamanho

Este exemplo define o nome da VM como *myVM* e o tamanho da VM *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Adicionar a NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Adicionar o disco do SO 

Adicionar o disco do SO para a configuração usando [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). Este exemplo define o tamanho do disco para *128 GB* e anexa o disco gerido como um *Windows* disco do SO.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Concluir a VM 

Criar a VM ao utilizar [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) com as configurações que acabamos de criar.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Se este comando é efetuada com êxito, verá a saída como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Certifique-se de que a VM foi criada
Deverá ver a VM criada recentemente ou na [portal do Azure](https://portal.azure.com) sob **procurar** > **máquinas virtuais**, ou utilizando os seguintes comandos do PowerShell.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Passos Seguintes
Inicie sessão na sua nova máquina virtual. Para obter mais informações, consulte [como ligar e iniciar sessão a uma máquina virtual do Azure a executar o Windows](connect-logon.md).

