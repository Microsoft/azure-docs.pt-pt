---
title: Expandir a unidade de SO de um Windows VM em um Azure
description: Expandir o tamanho da unidade de SO de uma máquina virtual utilizando o Azure PowerShell no modelo de implementação do Gestor de Recursos.
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
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: df27d7b25010fa68fc86ffe093318b2b0b7f4e96
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393834"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Como expandir a unidade do SO das máquinas virtuais

Quando cria uma nova máquina virtual (VM) num grupo de recursos, implantando uma imagem do [Azure Marketplace,](https://azure.microsoft.com/marketplace/)a unidade de sistema operativo padrão é frequentemente de 127 GB (algumas imagens têm tamanhos de disco de SO menores por padrão). Embora seja possível adicionar discos de dados ao VM (o número depende do SKU que escolheu) e recomendamos a instalação de aplicações e cargas de trabalho intensivas de CPU nestes discos de adenda, muitas vezes, os clientes precisam expandir a unidade de SO para suportar cenários específicos:

- Para suportar aplicações antigas que instalam componentes na unidade de SO.
- Para migrar um PC físico ou VM a partir do local com uma unidade de SO maior.

> [!IMPORTANT]
> Redimensionar um DISCO de DADOS ou Um Disco de Dados de uma Máquina Virtual Azure requer que a máquina virtual seja negociada.
>
> A redução de um disco existente não é suportada, e pode potencialmente resultar em perda de dados.
> 
> Depois de expandir os discos, é necessário [expandir o volume dentro do SISTEMA](#expand-the-volume-within-the-os) para tirar partido do disco maior.

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Redimensione um disco gerido no portal Azure

1. No [portal Azure,](https://portal.azure.com)vá à máquina virtual na qual pretende expandir o disco. **Selecione Stop** para negociar o VM.
2. Quando o VM for parado, no menu esquerdo em **Definições** , selecione **Discos**.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="Screenshot que mostra a opção Discos selecionada na secção Definições do menu.":::

 
3. No **nome do disco,** selecione o disco que pretende redimensionar.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="Screenshot que mostra o painel de Discos com um nome de disco selecionado.":::

4. No menu esquerdo em **Definições** , selecione **Configuração**.

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="Screenshot que mostra a opção de Configuração selecionada na secção Definições do menu.":::

5. Em **Tamanho (GiB)** , selecione o tamanho do disco que pretende.
   
   > [!WARNING]
   > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2.048 GB para discos de SO. (É possível expandir a bolha VHD para além desse tamanho, mas o SO funciona apenas com os primeiros 2.048 GB de espaço.)
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="Screenshot que mostra o painel de configuração com o tamanho do disco selecionado.":::

6. Selecione **Save** (Guardar).

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="Screenshot que mostra o painel de configuração com o botão Guardar selecionado.":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>Redimensione um disco gerido utilizando o PowerShell

Abra a sua janela PowerShell ISE ou PowerShell em modo administrativo e siga os passos abaixo:

1. Inscreva-se na sua conta Microsoft Azure no modo de gestão de recursos e selecione a sua subscrição:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Desajuste o nome do seu grupo de recursos e nome VM:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Obtenha uma referência ao seu VM:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Pare o VM antes de redimensionar o disco:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Obtenha uma referência ao disco de OS gerido. Desave o tamanho do disco de OS gerido para o valor pretendido e atualize o Disco:
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2.048 GB para discos de SO. (É possível expandir a bolha VHD para além desse tamanho, mas o SO funciona apenas com os primeiros 2.048 GB de espaço.)
    > 
         
6. Atualizar o VM pode demorar alguns segundos. Quando o comando terminar a execução, reinicie o VM:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

E já está! Agora RDP para a VM, abra a Gestão de Computadores (ou gestão de discos) e expanda a unidade através do espaço alocado recentemente.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>Redimensione um disco não gerido utilizando o PowerShell

Abra a sua janela PowerShell ISE ou PowerShell em modo administrativo e siga os passos abaixo:

1. Inscreva-se na sua conta Microsoft Azure no modo de gestão de recursos e selecione a sua subscrição:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Desajuste o nome do seu grupo de recursos e os nomes de VM:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Obtenha uma referência ao seu VM:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Pare o VM antes de redimensionar o disco:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Desajuste o tamanho do disco de OS não gerido para o valor pretendido e atualize o VM:
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > O novo tamanho deve ser maior que o tamanho do disco existente. O máximo permitido é de 2.048 GB para discos de SO. (É possível expandir a bolha VHD para além desse tamanho, mas o SO só será capaz de trabalhar com os primeiros 2.048 GB de espaço.)
    > 
    > 
   
6. Atualizar o VM pode demorar alguns segundos. Quando o comando terminar a execução, reinicie o VM:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>Scripts para disco DE

Abaixo está o script completo para a sua referência para discos geridos e não geridos:


**Discos geridos**

```powershell
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

Este artigo está focado principalmente na expansão do disco de SO do VM, mas o script também pode ser usado para expandir os discos de dados ligados ao VM. Por exemplo, para expandir o primeiro disco de dados ligado à VM, substitua o `OSDisk` objeto do `StorageProfile` com a `DataDisks` matriz e utilizar um indexador numérico para obter uma referência para o disco de dados anexados primeiro, conforme mostrado abaixo:

**Disco gerido**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**Disco não gerido**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

Da mesma forma, pode fazer referência a outros discos de dados anexados ao VM, utilizando um índice como mostrado acima ou a propriedade **Name** do disco:


**Disco gerido**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Disco não gerido**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Expandir o volume dentro do SISTEMA

Quando expandiu o disco para o VM, é preciso entrar no SO e expandir o volume para abranger o novo espaço. Existem vários métodos para expandir uma partição. Esta secção abrange a ligação do VM utilizando uma ligação RDP para expandir a partição utilizando **o DiskPart**.

1. Abra uma ligação RDP ao seu VM.

2. Abra uma linha de comando e escreva **a parte do disco**.

3. Na solicitação **DISKPART,** escreva `list volume` . Tome nota do volume que pretende estender.

4. Na solicitação **DISKPART,** escreva `select volume <volumenumber>` . Isto seleciona o *volume de volume* que pretende estender para um espaço contíguo e vazio no mesmo disco.

5. Na solicitação **DISKPART,** escreva `extend [size=<size>]` . Isto estende o volume selecionado por *tamanho* em megabytes (MB).


## <a name="next-steps"></a>Passos seguintes

Também pode anexar discos utilizando o [portal Azure](attach-managed-disk-portal.md).
