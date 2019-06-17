---
title: Expanda a unidade do SO de uma VM do Windows no Azure | Documentos da Microsoft
description: Expanda o tamanho da unidade do sistema operacional de uma máquina virtual com o Azure Powershell o modelo de implementação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: 81e6b5558ab90f154ebf121a558704b00b97444d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684333"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Como expandir a unidade do SO de uma máquina virtual

Quando cria uma nova máquina virtual (VM) num grupo de recursos ao implementar uma imagem a partir [do Azure Marketplace](https://azure.microsoft.com/marketplace/), a unidade do SO predefinido é, muitas vezes, 127 GB (algumas imagens têm tamanhos de disco de SO menores por padrão). Embora seja possível adicionar discos de dados na VM (consoante o SKU que escolheu) e além disso é recomendado que instale aplicações e cargas de trabalho intensivas de CPU nestes discos de adenda, é frequente que os clientes precisem de expandir a unidade de SO para suportar determinados cenários, como o seguinte:

- Suportar aplicações antigas que instalam componentes na unidade do SO.
- Migrar uma máquina virtual ou PC físicos no local com uma unidade de SO maior.


> [!IMPORTANT]
> Redimensionar o disco de SO da Máquina Virtual do Azure fará com que ele seja reiniciado.
>
> Depois de expandir os discos, precisa [expandir o volume do sistema operacional](#expand-the-volume-within-the-os) para tirar partido do disco maior.
> 


[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="resize-a-managed-disk"></a>Redimensionar um disco gerido

Abra o ISE do Powershell ou a janela do Powershell no modo administrativo e siga os passos abaixo:

1. Inicie sessão na sua conta do Microsoft Azure no modo de gestão de recursos e selecione a sua subscrição da seguinte forma:
   
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
5. Obter uma referência para o disco de SO gerido. Definir o tamanho do disco do SO gerido para o valor pretendido e atualize o disco da seguinte forma:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2048 GB para discos de SO. (É possível expandir o blob VHD esse tamanho, mas o sistema operacional só poderá trabalhar com os primeiros 2048 GB de espaço.)
   > 
   > 
6. Atualizar a VM pode demorar alguns segundos. Assim que o comando terminar a execução, reinicie a VM da seguinte forma:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

E já está! Agora RDP para a VM, abra a Gestão de Computadores (ou gestão de discos) e expanda a unidade através do espaço alocado recentemente.

## <a name="resize-an-unmanaged-disk"></a>Redimensionar um disco não gerido

Abra o ISE do Powershell ou a janela do Powershell no modo administrativo e siga os passos abaixo:

1. Inicie sessão na sua conta do Microsoft Azure no modo de gestão de recursos e selecione a sua subscrição da seguinte forma:
   
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
5. Defina o tamanho do disco do SO não gerido para o valor pretendido e atualize a VM da seguinte forma:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2048 GB para discos de SO. (É possível expandir o blob VHD esse tamanho, mas o sistema operacional só poderá trabalhar com os primeiros 2048 GB de espaço.)
   > 
   > 
   
6. Atualizar a VM pode demorar alguns segundos. Assim que o comando terminar a execução, reinicie a VM da seguinte forma:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Scripts para o disco do SO

Segue-se o script completo para sua referência para discos geridos e não geridos:


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

Este artigo se concentra principalmente em expandir o disco do SO da VM, mas o script também pode ser usado para expandir os discos de dados ligados à VM. Por exemplo, para expandir o primeiro disco de dados ligado à VM, substitua o `OSDisk` objeto do `StorageProfile` com a `DataDisks` matriz e utilizar um indexador numérico para obter uma referência para o disco de dados anexados primeiro, conforme mostrado abaixo:

**Disco gerido**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Disco não gerido**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Da mesma forma, pode referenciar outros discos de dados ligados à VM, utilizando um índice, conforme mostrado acima ou o **nome** propriedade dos discos:


**Disco gerido**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Disco não gerido**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Expandir o volume do sistema operacional

Assim que tiver expandido o disco para a VM, terá de ir para o sistema operacional e expandir o volume para abranger o novo espaço. Existem vários métodos para expandir uma partição. Esta secção explica como ligar a VM através de uma ligação de RDP para expandir a partição usando **DiskPart**.

1. Abra uma ligação de RDP para a VM.

2.  Abra uma linha de comandos e escreva **diskpart**.

2.  Com o **DISKPART** linha de comandos, escreva `list volume`. Tome nota do volume que pretende expandir.

3.  Com o **DISKPART** linha de comandos, escreva `select volume <volumenumber>`. Selecione o volume *volumenumber* que pretende expandir para o espaço vazio, contíguo no mesmo disco.

4.  Com o **DISKPART** linha de comandos, escreva `extend [size=<size>]`. Isto expande o volume selecionado pelo *tamanho* em megabytes (MB).


## <a name="next-steps"></a>Passos Seguintes

Também pode anexar discos com o [portal do Azure](attach-managed-disk-portal.md).
