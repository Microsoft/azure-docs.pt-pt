---
title: Criar um VM Windows a partir de um VHD especializado em Azure
description: Crie um novo VM do Windows anexando um disco gerido especializado como o disco OS utilizando o modelo de implementação do Gestor de Recursos.
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
ms.openlocfilehash: fc157c2253a718860e028fa493574cb9aa2ccdf2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243370"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Criar um VM Windows a partir de um disco especializado utilizando o PowerShell

Crie um novo VM anexando um disco gerido especializado como o disco OS. Um disco especializado é uma cópia de um disco rígido virtual (VHD) de um VM existente que contém as contas, aplicações e outros dados do estado do seu VM original. 

Quando utiliza um VHD especializado para criar um novo VM, o novo VM mantém o nome do computador do VM original. Outras informações específicas do computador também são mantidas e, em alguns casos, esta informação duplicada pode causar problemas. Ao copiar um VM, esteja ciente dos tipos de informação específica do computador em que as suas aplicações dependem.

Tem várias opções:
* [Utilize um disco gerido existente](#option-1-use-an-existing-disk). Esta opção é útil se tiver um VM que não esteja a funcionar corretamente. Pode eliminar o VM e, em seguida, reutilizar o disco gerido para criar um novo VM. 
* [Carregar um VHD](#option-2-upload-a-specialized-vhd) 
* [Copiar um VM Azure existente utilizando instantâneos](#option-3-copy-an-existing-azure-vm)

Também pode utilizar o portal Azure para [criar um novo VM a partir de um VHD especializado.](create-vm-specialized-portal.md)

Este artigo mostra-lhe como usar discos geridos. Se tiver uma implementação do legado que requer a utilização de uma conta de armazenamento, consulte [Criar um VM a partir de um VHD especializado numa conta](sa-create-vm-specialized.md)de armazenamento .

Recomendamos que limite o número de implementações simultâneas a 20 VMs de um único VHD ou instantâneo. 

## <a name="option-1-use-an-existing-disk"></a>Opção 1: Utilize um disco existente

Se tiver um VM que apagou e quiser reutilizar o disco OS para criar um novo VM, use [O Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Agora pode anexar este disco como o disco OS a um [novo VM](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Opção 2: Faça upload de um VHD especializado

Você pode carregar o VHD a partir de um VM especializado criado com uma ferramenta de virtualização no local, como Hyper-V, ou um VM exportado de outra nuvem.

### <a name="prepare-the-vm"></a>Preparar a VM
Utilize o VHD como é para criar um novo VM. 
  
  * [Prepare um VHD do Windows para fazer o upload para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalize o VM utilizando o Sysprep.
  * Remova quaisquer ferramentas e agentes de virtualização de hóspedes instalados no VM (como ferramentas VMware).
  * Certifique-se de que o VM está configurado para obter as definições de endereço IP e DNS do DHCP. Isto garante que o servidor obtém um endereço IP dentro da rede virtual quando é iniciado. 


### <a name="upload-the-vhd"></a>Carregar o VHD

Agora pode enviar um VHD diretamente para um disco gerido. Para obter instruções, consulte [o upload de um VHD para Azure utilizando o Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Opção 3: Copiar um VM Azure existente

Pode criar uma cópia de um VM que utiliza discos geridos tirando uma foto do VM e, em seguida, usando esse instantâneo para criar um novo disco gerido e um novo VM.

Se quiser copiar um VM existente para outra região, talvez queira usar a zcopy para [criar uma cópia de um disco noutra região](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Tire uma foto do disco osso

Pode tirar uma foto de um VM inteiro (incluindo todos os discos) ou de apenas um disco. Os seguintes passos mostram-lhe como tirar uma foto do disco OS do seu VM com o cmdlet [New-AzSnapshot.](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) 

Primeiro, estabeleça alguns parâmetros. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Pegue o objeto VM.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Pegue o nome do disco soco.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Crie a configuração instantânea. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Tira a fotografia.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Para utilizar este instantâneo para criar um VM que precisa de ser de alto desempenho, adicione o parâmetro `-AccountType Premium_LRS` ao comando New-AzSnapshotConfig. Este parâmetro cria o instantâneo para que seja armazenado como um Disco Gerido Premium. Os Discos Geridos Premium são mais caros do que o Standard, por isso certifique-se de que vai precisar de Premium antes de utilizar este parâmetro.

### <a name="create-a-new-disk-from-the-snapshot"></a>Criar um novo disco a partir do instantâneo

Crie um disco gerido a partir do instantâneo utilizando [o New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). Este exemplo utiliza *o myOSDisk* para o nome do disco.

Crie um novo grupo de recursos para o novo VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Detete o nome do disco OS. 

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


## <a name="create-the-new-vm"></a>Criar o novo VM 

Crie redes e outros recursos VM para serem utilizados pelo novo VM.

### <a name="create-the-subnet-and-virtual-network"></a>Criar a rede de sub-rede e virtual

Crie a [rede virtual](../../virtual-network/virtual-networks-overview.md) e a sub-rede para o VM.

1. Crie a sub-rede. Este exemplo cria uma subnet denominada *mySubNet*, no grupo de recursos *myDestinationResourceGroup,* e define o prefixo de endereço de sub-rede para *10.0.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Criar a rede virtual. Este exemplo define o nome da rede virtual para *myVnetName*, a localização para *Oeste dos EUA,* e o prefixo de endereço para a rede virtual para *10.0.0.0.16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança da rede e uma regra de RDP
Para poder iniciar sessão no seu VM com protocolo de ambiente de trabalho remoto (RDP), terá de ter uma regra de segurança que permita o acesso rdp na porta 3389. No nosso exemplo, o VHD para o novo VM foi criado a partir de um VM especializado existente, para que possa usar uma conta que existia na máquina virtual de origem para RDP.

Este exemplo define o nome do grupo de segurança da rede (NSG) para *myNsg* e o nome da regra RDP para *myRdpRule*.

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

Para obter mais informações sobre pontos finais e regras de NSG, consulte [abrir portas para um VM em Azure utilizando](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)o PowerShell .

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e NIC
Para permitir a comunicação com a máquina virtual na rede virtual, necessitará de um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e de uma interface de rede.

1. Crie o IP público. Neste exemplo, o nome de endereço IP público é definido para *o myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Crie o NIC. Neste exemplo, o nome NIC é definido para *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Detete o nome e o tamanho do VM

Este exemplo define o nome VM para *myVM* e o tamanho VM para *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Adicione o NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Adicione o disco OS 

Adicione o disco OS à configuração utilizando [o Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). Este exemplo define o tamanho do disco para *128 GB* e fixa o disco gerido como um disco *do Windows* OS.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Complete o VM 

Crie o VM utilizando [o New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) com as configurações que acabamos de criar.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Se este comando for bem sucedido, verá uma saída como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verifique se o VM foi criado
Deve ver o VM recém-criado, quer no [portal Azure,](https://portal.azure.com) em **Browse** > **Máquinas Virtuais,** quer utilizando os seguintes comandos PowerShell.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Passos Seguintes
Inscreva-se na sua nova máquina virtual. Para mais informações, consulte [Como ligar e iniciar sessão numa máquina virtual Azure que executa o Windows](connect-logon.md).

