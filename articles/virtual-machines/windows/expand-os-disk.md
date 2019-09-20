---
title: Expandir a unidade do sistema operacional de uma VM do Windows em um Azure | Microsoft Docs
description: Expanda o tamanho da unidade do sistema operacional de uma máquina virtual usando o Azure PowerShell no modelo de implantação do Gerenciador de recursos.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: 692046070ffc04942a5d8a73825f6cb59e462f8b
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147216"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Como expandir a unidade do sistema operacional de uma máquina virtual

Quando você cria uma nova VM (máquina virtual) em um grupo de recursos implantando uma imagem do [Azure Marketplace](https://azure.microsoft.com/marketplace/), a unidade do sistema operacional padrão geralmente é de 127 GB (algumas imagens têm tamanhos de disco do sistema operacional menores por padrão). Embora seja possível adicionar discos de dados na VM (consoante o SKU que escolheu) e além disso é recomendado que instale aplicações e cargas de trabalho intensivas de CPU nestes discos de adenda, é frequente que os clientes precisem de expandir a unidade de SO para suportar determinados cenários, como o seguinte:

- Suportar aplicações antigas que instalam componentes na unidade do SO.
- Migrar uma máquina virtual ou PC físicos no local com uma unidade de SO maior.


> [!IMPORTANT]
> O redimensionamento do disco do sistema operacional de uma máquina virtual do Azure requer que a máquina virtual seja desalocada.
>
> Depois de expandir os discos, você precisa [expandir o volume dentro do sistema operacional](#expand-the-volume-within-the-os) para tirar proveito do disco maior.
> 


[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="resize-a-managed-disk"></a>Redimensionar um disco gerenciado

Abra o ISE do Powershell ou a janela do Powershell no modo administrativo e siga os passos abaixo:

1. Entre em sua conta do Microsoft Azure no modo de gerenciamento de recursos e selecione sua assinatura da seguinte maneira:
   
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
5. Obtenha uma referência para o disco do sistema operacional gerenciado. Defina o tamanho do disco do sistema operacional gerenciado para o valor desejado e atualize o disco da seguinte maneira:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2048 GB para discos do sistema operacional. (É possível expandir o blob VHD para além desse tamanho, mas o sistema operacional só poderá trabalhar com os primeiros 2048 GB de espaço).
   > 
   > 
6. Atualizar a VM pode demorar alguns segundos. Assim que o comando terminar a execução, reinicie a VM da seguinte forma:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

E já está! Agora RDP para a VM, abra a Gestão de Computadores (ou gestão de discos) e expanda a unidade através do espaço alocado recentemente.

## <a name="resize-an-unmanaged-disk"></a>Redimensionar um disco não gerenciado

Abra o ISE do Powershell ou a janela do Powershell no modo administrativo e siga os passos abaixo:

1. Entre em sua conta do Microsoft Azure no modo de gerenciamento de recursos e selecione sua assinatura da seguinte maneira:
   
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
5. Defina o tamanho do disco do sistema operacional não gerenciado para o valor desejado e atualize a VM da seguinte maneira:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2048 GB para discos do sistema operacional. (É possível expandir o blob VHD para além desse tamanho, mas o sistema operacional só poderá trabalhar com os primeiros 2048 GB de espaço).
   > 
   > 
   
6. Atualizar a VM pode demorar alguns segundos. Assim que o comando terminar a execução, reinicie a VM da seguinte forma:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Scripts para disco do sistema operacional

Abaixo está o script completo para sua referência para discos gerenciados e não gerenciados:


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

## <a name="resizing-data-disks"></a>Redimensionando discos de dados

Este artigo se concentra principalmente na expansão do disco do sistema operacional da VM, mas o script também pode ser usado para expandir os discos de dados anexados à VM. Por exemplo, para expandir o primeiro disco de dados ligado à VM, substitua o `OSDisk` objeto do `StorageProfile` com a `DataDisks` matriz e utilizar um indexador numérico para obter uma referência para o disco de dados anexados primeiro, conforme mostrado abaixo:

**Disco gerenciado**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Disco não gerenciado**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Da mesma forma, você pode fazer referência a outros discos de dados anexados à VM, seja usando um índice, conforme mostrado acima ou a propriedade **Name** do disco:


**Disco gerenciado**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Disco não gerenciado**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Expandir o volume dentro do sistema operacional

Depois de expandir o disco para a VM, você precisará entrar no sistema operacional e expandir o volume para abranger o novo espaço. Há vários métodos para expandir uma partição. Esta seção aborda a conexão da VM usando uma conexão RDP para expandir a partição usando o **DiskPart**.

1. Abra uma conexão RDP para sua VM.

2.  Abra um prompt de comando e digite **DiskPart**.

2.  No prompt do **DiskPart** , digite `list volume`. Anote o volume que você deseja estender.

3.  No prompt do **DiskPart** , digite `select volume <volumenumber>`. Isso seleciona o volume *volumenumber* que você deseja estender em espaço contíguo vazio no mesmo disco.

4.  No prompt do **DiskPart** , digite `extend [size=<size>]`. Isso estende o volume selecionado por *tamanho* em megabytes (MB).


## <a name="next-steps"></a>Passos seguintes

Você também pode anexar discos usando o [portal do Azure](attach-managed-disk-portal.md).
