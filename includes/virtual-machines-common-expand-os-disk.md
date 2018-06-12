---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: sdwheeler
ms.service: virtual-machines
ms.topic: include
ms.date: 04/18/2018
ms.author: kirpas;iainfou;sewhee
ms.custom: include file
ms.openlocfilehash: c8b48c9b3ebd6b40640a744f00673158c07cdc3a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35323804"
---
## <a name="overview"></a>Descrição geral
Quando cria uma nova máquina virtual (VM) num grupo de recursos ao implementar uma imagem de [Azure Marketplace](https://azure.microsoft.com/marketplace/), unidade predefinida do SO, muitas vezes, é de 127 GB (algumas imagens tem tamanhos de disco de SO mais pequenos por predefinição). Embora seja possível adicionar discos de dados na VM (consoante o SKU que escolheu) e além disso é recomendado que instale aplicações e cargas de trabalho intensivas de CPU nestes discos de adenda, é frequente que os clientes precisem de expandir a unidade de SO para suportar determinados cenários, como o seguinte:

1. Suportar aplicações antigas que instalam componentes na unidade do SO.
2. Migrar uma máquina virtual ou PC físicos no local com uma unidade de SO maior.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: Resource Manager e Clássico. Este artigo abrange a utilização do modelo de implementação do Resource Manager. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.
> 
> 
> [!WARNING]
> Redimensionar o disco de SO uma Máquina Virtual do Azure irá fazer com que a reiniciar.
>

## <a name="resize-the-os-drive"></a>Redimensionar a unidade do SO
Neste artigo, irá realizar a tarefa de redimensionamento da unidade de SO com os módulos do gestor de recursos do [Azure Powershell](/powershell/azureps-cmdlets-docs). Vamos mostrar redimensionar a unidade de SO para não gerido e gerido discos, uma vez que a abordagem para redimensionar discos difere entre os dois tipos de disco.

### <a name="for-resizing-unmanaged-disks"></a>Para o redimensionamento de discos não gerido:

Abra o ISE do Powershell ou a janela do Powershell no modo administrativo e siga os passos abaixo:

1. Inicie de sessão na sua conta do Microsoft Azure no modo de gestão de recursos e selecione a sua subscrição da seguinte forma:
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Defina o nome do grupo de recursos e o nome VM da seguinte forma:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obter uma referência para a VM da seguinte forma:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Pare a VM antes de redimensionar o disco da seguinte forma:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Eis o momento pelo qual tanto esperou! Defina o tamanho do disco de SO não gerido para o valor pretendido e atualize a VM da seguinte forma:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2048 GB para os discos de SO. (É possível expandir o blob VHD esse tamanho, mas o SO apenas será possível trabalhar com o primeira 2048 GB de espaço).
   > 
   > 
6. Atualizar a VM pode demorar alguns segundos. Assim que o comando terminar a execução, reinicie a VM da seguinte forma:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

### <a name="for-resizing-managed-disks"></a>Para o redimensionamento de discos geridos:

Abra o ISE do Powershell ou a janela do Powershell no modo administrativo e siga os passos abaixo:

1. Inicie de sessão na sua conta do Microsoft Azure no modo de gestão de recursos e selecione a sua subscrição da seguinte forma:
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Defina o nome do grupo de recursos e o nome VM da seguinte forma:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obter uma referência para a VM da seguinte forma:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Pare a VM antes de redimensionar o disco da seguinte forma:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Obter uma referência para o disco de SO gerido. Defina o tamanho do disco de SO gerido para o valor pretendido e atualizar o disco da seguinte forma:
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2048 GB para os discos de SO. (É possível expandir o blob VHD esse tamanho, mas o SO apenas será possível trabalhar com o primeira 2048 GB de espaço).
   > 
   > 
6. Atualizar a VM pode demorar alguns segundos. Assim que o comando terminar a execução, reinicie a VM da seguinte forma:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

E já está! Agora RDP para a VM, abra a Gestão de Computadores (ou gestão de discos) e expanda a unidade através do espaço alocado recentemente.

## <a name="summary"></a>Resumo
Neste artigo, utilizámos os módulos do Azure Resource Manager do Powershell para expandir o disco de SO da máquina virtual IaaS. Reproduzido abaixo é o script completado para uma referência para os discos não gerido e gerido:

Discos não geridos:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
Managed Disks:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="for-resizing-data-disks"></a>Para o redimensionamento de discos de dados
Embora este artigo, vamos concentra-se principalmente em expansão do disco de SO gerido/não gerido da VM, o script programado também pode ser utilizado para expandir os discos de dados ligados à VM. Por exemplo, para expandir o primeiro disco de dados ligado à VM, substitua o ```OSDisk``` objeto do ```StorageProfile``` com a ```DataDisks``` matriz e utilizar um indexador numérico para obter uma referência para o disco de dados anexados primeiro, conforme mostrado abaixo:

Disco gerido:
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Disco gerido:
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

Da mesma forma, pode referenciar outros discos de dados ligados à VM, utilizando um índice, conforme mostrado acima ou a propriedade ```Name``` do disco conforme ilustrado abaixo:

Disco gerido:
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
Disco gerido:
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

Se quiser saber como anexar discos a uma VM do Azure Resource Manager, veja este [artigo](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

