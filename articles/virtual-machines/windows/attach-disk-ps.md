---
title: Fixe um disco de dados a um VM do Windows em Azure utilizando o PowerShell
description: Como anexar um disco de dados novo ou existente a um VM do Windows utilizando o PowerShell com o modelo de implementação do Gestor de Recursos.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ce995a84d2290845e83416caf9c8b0004242eed4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356524"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Fixe um disco de dados a um VM windows com PowerShell

Este artigo mostra-lhe como ligar discos novos e existentes a uma máquina virtual do Windows utilizando o PowerShell. 

Primeiro, reveja estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados pode anexar. Para mais informações, consulte [Tamanhos para máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para utilizar SSDs premium, você precisará de um [tipo VM premium ativado](sizes-memory.md)por armazenamento , como a série DS ou a máquina virtual da série GS.

Este artigo utiliza o PowerShell dentro do [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), que é constantemente atualizado para a versão mais recente. Para abrir a Cloud Shell, selecione **Experimente a** partir do topo de qualquer bloco de código.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Adicione um disco de dados vazio a uma máquina virtual

Este exemplo mostra como adicionar um disco de dados vazio a uma máquina virtual existente.

### <a name="using-managed-disks"></a>Utilização de discos geridos

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Utilização de discos geridos numa Zona de Disponibilidade

Para criar um disco numa Zona de Disponibilidade, utilize o [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) com o parâmetro `-Zone`. O exemplo seguinte cria um disco na zona *1*.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>Inicializar o disco

Depois de adicionar um disco vazio, terá de o inicializar. Para inicializar o disco, pode iniciar sessão num VM e utilizar a gestão do disco. Se ativar o [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) e um certificado no VM quando o criou, pode utilizar o PowerShell remoto para inicializar o disco. Também pode utilizar uma extensão de script personalizada:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

O ficheiro script pode conter código para inicializar os discos, por exemplo:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Anexar um disco de dados existente a um VM

Pode anexar um disco gerido existente a um VM como um disco de dados.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Passos seguintes

Também pode implantar discos geridos utilizando modelos. Para mais informações, consulte [a utilização de discos geridos nos modelos](using-managed-disks-template-deployments.md) do Gestor de Recursos Azure ou o [modelo de arranque rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) para a implementação de vários discos de dados.
