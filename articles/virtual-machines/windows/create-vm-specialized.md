---
title: Criar um Windows VM a partir de um VHD especializado em Azure
description: Crie um novo Windows VM anexando um disco gerido especializado como disco OS utilizando o modelo de implementação do Gestor de Recursos.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 3df7d3d01dcd5e5b097eba53ef0dae29e86fd0a5
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973262"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Create a Windows VM from a specialized disk by using PowerShell (Utilizar o PowerShell para criar uma VM do Windows a partir de um disco especializado)

Crie um novo VM anexando um disco gerido especializado como o disco DE. Um disco especializado é uma cópia de um disco rígido virtual (VHD) de um VM existente que contém as contas de utilizador, aplicações e outros dados do estado a partir do seu VM original. 

Quando se utiliza um VHD especializado para criar um novo VM, o novo VM mantém o nome de computador do VM original. Outras informações específicas do computador também são mantidas e, em alguns casos, esta informação duplicada pode causar problemas. Ao copiar um VM, esteja ciente de que tipos de informações específicas do computador as suas aplicações dependem.

Tem várias opções:
* [Utilize um disco gerido existente.](#option-1-use-an-existing-disk) Esta opção é útil se tiver um VM que não esteja a funcionar corretamente. Pode eliminar o VM e depois reutilizar o disco gerido para criar um novo VM. 
* [Carregar um VHD](#option-2-upload-a-specialized-vhd) 
* [Copie um Azure VM existente usando instantâneos](#option-3-copy-an-existing-azure-vm)

Também pode utilizar o portal Azure para [criar um novo VM a partir de um VHD especializado.](create-vm-specialized-portal.md)

Este artigo mostra-lhe como usar discos geridos. Se tiver uma implantação antiga que exija a utilização de uma conta de armazenamento, consulte [criar um VM a partir de um VHD especializado numa conta de armazenamento.](/previous-versions/azure/virtual-machines/windows/sa-create-vm-specialized)

Recomendamos que limite o número de implementações simultâneas a 20 VMs de um único VHD ou instantâneo. 

## <a name="option-1-use-an-existing-disk"></a>Opção 1: Utilizar um disco existente

Se tiver um VM que apagou e pretende reutilizar o disco DE para criar um novo VM, utilize [o Get-AzDisk](/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Pode agora anexar este disco como disco DE A um [novo VM](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Opção 2: Carregar um VHD especializado

Você pode carregar o VHD a partir de um VM especializado criado com uma ferramenta de virtualização no local, como Hyper-V, ou um VM exportado de outra nuvem.

### <a name="prepare-the-vm"></a>Preparar a VM
Utilize o VHD como é para criar um novo VM. 
  
  * [Prepare um VHD do Windows para fazer o upload para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalize o VM utilizando o Sysprep.
  * Remova quaisquer ferramentas e agentes de virtualização de hóspedes instalados no VM (como ferramentas VMware).
  * Certifique-se de que o VM está configurado para obter o endereço IP e as definições de DNS do DHCP. Isto garante que o servidor obtém um endereço IP dentro da rede virtual quando este começa. 


### <a name="upload-the-vhd"></a>Faça o upload do VHD

Agora pode enviar um VHD diretamente para um disco gerido. Para obter instruções, consulte [o Upload a VHD to Azure utilizando a Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Opção 3: Copiar um Azure VM existente

Pode criar uma cópia de um VM que utiliza discos geridos tirando uma foto do VM e, em seguida, usando esse instantâneo para criar um novo disco gerido e um novo VM.

Se quiser copiar um VM existente para outra região, talvez queira utilizar a azcopia para [criar uma cópia de um disco noutra região.](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) 

### <a name="take-a-snapshot-of-the-os-disk"></a>Tire uma foto do disco de so

Pode tirar uma foto de um VM inteiro (incluindo todos os discos) ou de apenas um disco. Os passos seguintes mostram-lhe como tirar uma foto do disco de SO do seu VM com o cmdlet [New-AzSnapshot.](/powershell/module/az.compute/new-azsnapshot) 

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
Pegue o nome do disco SO.

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

Tira a foto.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Para utilizar esta imagem para criar um VM que precisa de um desempenho elevado, adicione o parâmetro `-AccountType Premium_LRS` ao comando New-AzSnapshotConfig. Este parâmetro cria o instantâneo de modo a que seja armazenado como um Disco Gerido Premium. Os Discos Geridos Premium são mais caros do que o Standard, por isso certifique-se de que vai precisar de Premium antes de usar este parâmetro.

### <a name="create-a-new-disk-from-the-snapshot"></a>Criar um novo disco a partir do instantâneo

Crie um disco gerido a partir do instantâneo utilizando [o New-AzDisk](/powershell/module/az.compute/new-azdisk). Este exemplo utiliza *o myOSDisk* para o nome do disco.

Criar um novo grupo de recursos para o novo VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Desa parte para o nome do disco OS. 

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

Criar redes e outros recursos VM para serem utilizados pelo novo VM.

### <a name="create-the-subnet-and-virtual-network"></a>Criar a sub-rede e a rede virtual

Crie a [rede virtual](../../virtual-network/virtual-networks-overview.md) e a sub-rede para o VM.

1. Crie a sub-rede. Este exemplo cria uma sub-rede chamada *mySubNet,* no grupo de recursos *myDestinationResourceGroup*, e define o prefixo do endereço da sub-rede para *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Criar a rede virtual. Este exemplo define o nome da rede virtual para *myVnetName*, a localização para *Os Eua Ocidentais*, e o prefixo de endereço para a rede virtual para *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança da rede e uma regra RDP
Para poder iniciar sedução no seu VM com protocolo remoto de ambiente de trabalho (RDP), terá de ter uma regra de segurança que permita o acesso do RDP à porta 3389. No nosso exemplo, o VHD para o novo VM foi criado a partir de um VM especializado existente, para que possa utilizar uma conta que existiu na máquina virtual de origem para RDP.

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

Para obter mais informações sobre os pontos finais e as regras NSG, consulte [portas de abertura para um VM em Azure utilizando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e NIC
Para ativar a comunicação com a máquina virtual na rede virtual, você precisará de um [endereço IP público](../../virtual-network/public-ip-addresses.md) e uma interface de rede.

1. Crie o IP público. Neste exemplo, o nome do endereço IP público está definido para *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Criar o NIC. Neste exemplo, o nome NIC é definido para *o meu Nome DeNico.*
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Definir o nome e o tamanho VM

Este exemplo define o nome VM ao *myVM* e ao tamanho VM para *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Adicione o NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Adicione o disco DE 

Adicione o disco OS à configuração utilizando [o Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk). Este exemplo define o tamanho do disco para *128 GB* e anexa o disco gerido como um disco *Windows* OS.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Complete o VM 

Crie o VM utilizando [o New-AzVM](/powershell/module/az.compute/new-azvm) com as configurações que acabámos de criar.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Se este comando for bem sucedido, verá saídas como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verifique se o VM foi criado
Deverá ver o VM recém-criado no [portal Azure](https://portal.azure.com) sob as máquinas Virtual **Browse**  >  **Virtual machines**, ou utilizando os seguintes comandos PowerShell.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Inscreva-se na sua nova máquina virtual. Para obter mais informações, consulte [Como ligar e iniciar sessão numa máquina virtual Azure que executa o Windows](connect-logon.md).