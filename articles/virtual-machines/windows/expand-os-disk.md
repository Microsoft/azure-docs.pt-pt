---
title: Expandir a unidade de OS de um Windows VM num Azure
description: Expanda o tamanho da unidade de OS de uma máquina virtual utilizando o Azure Powershell no modelo de implementação do Gestor de Recursos.
author: mimckitt
manager: vashan
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: mimckitt
ms.subservice: disks
ms.openlocfilehash: e69b041a2e4c8a0715adb6ab126a3aede42f7dde
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869693"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Como expandir a unidade do SO das máquinas virtuais

Quando se cria uma nova máquina virtual (VM) num Grupo de Recursos, implantando uma imagem do [Azure Marketplace,](https://azure.microsoft.com/marketplace/)a unidade de OS padrão é frequentemente de 127 GB (algumas imagens têm tamanhos de disco osso mais pequenos por padrão). Embora seja possível adicionar discos de dados na VM (consoante o SKU que escolheu) e além disso é recomendado que instale aplicações e cargas de trabalho intensivas de CPU nestes discos de adenda, é frequente que os clientes precisem de expandir a unidade de SO para suportar determinados cenários, como o seguinte:

- Suportar aplicações antigas que instalam componentes na unidade do SO.
- Migrar uma máquina virtual ou PC físicos no local com uma unidade de SO maior.


> [!IMPORTANT]
> Redimensionar o Disco OS de uma Máquina Virtual Azure requer que a máquina virtual seja realojada.
>
> Depois de expandir os discos, é necessário [expandir o volume dentro do SISTEMA](#expand-the-volume-within-the-os) para tirar partido do disco maior.
> 


 


## <a name="resize-a-managed-disk"></a>Redimensionar um disco gerido

Abra o ISE do Powershell ou a janela do Powershell no modo administrativo e siga os passos abaixo:

1. Inscreva-se na sua conta Microsoft Azure no modo de gestão de recursos e selecione a sua subscrição da seguinte forma:
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Defina o nome do grupo de recursos e o nome VM da seguinte forma:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obter uma referência para a VM da seguinte forma:
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Pare a VM antes de redimensionar o disco da seguinte forma:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Obtenha uma referência ao disco osso gerido. Detete o tamanho do disco OS gerido para o valor desejado e atualize o Disco da seguinte forma:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2048 GB para discos OS. (É possível expandir a bolha VHD para além desse tamanho, mas o Sistema operativo só será capaz de trabalhar com o primeiro gb de espaço de 2048.)
   > 
   > 
6. Atualizar a VM pode demorar alguns segundos. Assim que o comando terminar a execução, reinicie a VM da seguinte forma:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

E já está! Agora RDP para a VM, abra a Gestão de Computadores (ou gestão de discos) e expanda a unidade através do espaço alocado recentemente.

## <a name="resize-an-unmanaged-disk"></a>Redimensionar um disco não gerido

Abra o ISE do Powershell ou a janela do Powershell no modo administrativo e siga os passos abaixo:

1. Inscreva-se na sua conta Microsoft Azure no modo de gestão de recursos e selecione a sua subscrição da seguinte forma:
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Defina o nome do grupo de recursos e o nome VM da seguinte forma:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obter uma referência para a VM da seguinte forma:
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Pare a VM antes de redimensionar o disco da seguinte forma:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Detete o tamanho do disco OS não gerido para o valor desejado e atualize o VM da seguinte forma:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2048 GB para discos OS. (É possível expandir a bolha VHD para além desse tamanho, mas o Sistema operativo só será capaz de trabalhar com o primeiro gb de espaço de 2048.)
   > 
   > 
   
6. Atualizar a VM pode demorar alguns segundos. Assim que o comando terminar a execução, reinicie a VM da seguinte forma:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Scripts para disco OS

Abaixo está o script completo para a sua referência para discos geridos e não geridos:


**Discos geridos**

```Powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Discos não geridos**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Redimensionamento de discos de dados

Este artigo está focado principalmente na expansão do disco OS do VM, mas o script também pode ser usado para expandir os discos de dados ligados ao VM. Por exemplo, para expandir o primeiro disco de dados ligado à VM, substitua o `OSDisk` objeto do `StorageProfile` com a `DataDisks` matriz e utilizar um indexador numérico para obter uma referência para o disco de dados anexados primeiro, conforme mostrado abaixo:

**Disco gerido**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Disco não gerido**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Da mesma forma, pode fazer referência a outros discos de dados ligados ao VM, quer utilizando um índice como indicado acima, quer a propriedade **nome** do disco:


**Disco gerido**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Disco não gerido**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Expandir o volume dentro do SO

Depois de expandir o disco para o VM, precisa de entrar no SISTEMA e expandir o volume para abranger o novo espaço. Existem vários métodos para expandir uma partição. Esta secção cobre a ligação do VM utilizando uma ligação RDP para expandir a divisória utilizando **o DiskPart**.

1. Abra uma ligação RDP ao seu VM.

2.  Abra uma peça de comando e digite **disqueid .**

2.  No aviso **DISKPART,** escreva `list volume`. Tome nota do volume que pretende estender.

3.  No aviso **DISKPART,** escreva `select volume <volumenumber>`. Isto seleciona o *volume de volume* que pretende estender em espaço contíguo e vazio no mesmo disco.

4.  No aviso **DISKPART,** escreva `extend [size=<size>]`. Isto alarga o volume selecionado em *tamanho* em megabytes (MB).


## <a name="next-steps"></a>Passos seguintes

Também pode fixar discos utilizando o [portal Azure](attach-managed-disk-portal.md).
