---
title: Tutorial – Gerir discos do Azure com o Azure PowerShell | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar o Azure PowerShell para criar e gerir discos do Azure para máquinas virtuais
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2018
ms.author: cynthn
ms.custom: mvc
ms.component: disks
ms.openlocfilehash: d8ae255242979978ac0f16a1b483a99796772192
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464413"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Tutorial – Gerir discos do Azure com o Azure PowerShell

As máquinas virtuais do Azure utilizam discos para armazenar o sistema operativo, as aplicações e os dados das VMs. Ao criar uma VM, é importante escolher um tamanho de disco e a configuração adequados para a carga de trabalho esperada. Este tutorial abrange a implementação e gestão de discos de VM. Vai obter informações sobre:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Expor e preparar os discos de dados

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="default-azure-disks"></a>Discos do Azure predefinidos

Quando uma máquina virtual do Azure é criada, dois discos são automaticamente expostos à máquina virtual. 

**Disco do sistema operativo** - os discos do sistema operativo podem ter o tamanho máximo de 4 terabytes e alojam o sistema operativo das VMs.  O disco do SO é atribuído a uma letra de unidade de *C:* por predefinição. A configuração da colocação em cache do disco do SO está otimizada para desempenho do SO. O disco do SO **não deve** alojar aplicações ou dados. Para aplicações e dados, utilize um disco de dados, que se encontra detalhado posteriormente neste artigo.

**Disco temporário** - os discos temporários utilizam uma unidade de estado sólido que está localizada no mesmo anfitrião da VM do Azure. Os discos temporários são de elevado desempenho e servem para operações como o processamento de dados temporários. No entanto, se a VM for movida para um novo anfitrião, todos os dados armazenados num disco temporário são removidos. O tamanho do disco temporário é determinado pelo [tamanho da VM](sizes.md). Os discos temporários estão atribuídos a uma letra de unidade de *D:* por predefinição.



## <a name="azure-data-disks"></a>Discos de dados do Azure

Podem ser adicionados mais discos para instalar aplicações e armazenar dados. Os discos de dados devem ser utilizados em qualquer situação em que seja preciso armazenamento de dados duradouro e reativo. Cada disco de dados tem a capacidade máxima de 4 terabytes. O tamanho da máquina virtual determina quantos discos de dados podem ser expostos a uma VM. Para cada vCPU de VM, podem ser expostos quatro discos de dados. 


## <a name="vm-disk-types"></a>Tipos de disco de VM

O Azure oferece dois tipos de discos.

**Discos Standard** - são apoiados por HDDs e oferecem armazenamento económico, mantendo o desempenho. Os discos Standard são ideais para uma carga de trabalho de desenvolvimento e teste económica.

**Discos Premium** - são apoiados por um disco de elevado desempenho baseado em SSD e de baixa latência. São perfeitos para as VMs com carga de trabalho de produção. O Armazenamento Premium suporta VMs da série DS, série DSv2, série GS e série FS. Os discos Premium são fornecidos em cinco tipos (P10, P20, P30, P40, P50), e o tamanho do disco determina o tipo de disco. Ao selecionar um tamanho de disco, o valor é arredondado para o tipo seguinte. Por exemplo, se o tamanho for inferior a 128 GB, o tipo de disco é P10 ou entre 129 GB e 512 GB, o disco é P20.

### <a name="premium-disk-performance"></a>Desempenho do disco Premium

|Tipo de disco de armazenamento Premium | P4 | P6 | P10 | P20 | P30 | P40 | P50 | p60 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Tamanho do disco (arredondado) | 32 GiB | 64 GiB | 128 GiB | 512 GiB | 1.024 GiB (1 TiB) | 2.048 GiB (2 TiB) | 4.095 GiB (4 TiB) | 8.192 GiB (8 TiB)
| IOPs Máx por disco | 120 | 240 | 500 | 2.300 | 5.000 | 7.500 | 7.500 | 12.500 |
Débito por disco | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s | 480 MB/s |

Enquanto a tabela acima identifica o IOPS máximo por disco, um nível mais elevado de desempenho pode ser alcançado ao repartir vários discos de dados. Por exemplo, podem ser anexados 64 discos de dados à VM Standard_GS5. Se cada um destes discos for dimensionado como um P30, pode ser alcançado um máximo de 80.000 IOPS. Para obter informações detalhadas sobre o IOPS máximo por VM, veja [Tipos e tamanhos de VMs](./sizes.md).

## <a name="create-and-attach-disks"></a>Criar e anexar discos

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente. Se for preciso, crie uma máquina virtual com os seguintes comandos.

Defina o nome de utilizador e a palavra-passe necessários para a conta de administrador na máquina virtual com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):


Criar a máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Será pedido para introduzir um nome de utilizador e palavra-passe para a conta de administradores para a VM.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Crie a configuração inicial com [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). O exemplo a seguir configura um disco com 128 gigabytes.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Crie o disco de dados com o comando [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk).

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Obtenha a máquina virtual que pretende para adicionar o disco de dados com o comando [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm).

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Adicione o disco de dados à configuração da máquina virtual com o comando [Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Atualize a máquina virtual com o comando [Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Preparar discos de dados

Depois de um disco ser exposto à máquina virtual, o sistema operativo tem de ser configurado para utilizar o disco. O exemplo seguinte mostra como configurar manualmente o primeiro disco adicionado à VM. Também pode utilizar a [extensão de script personalizada](./tutorial-automate-vm-deployment.md) para automatizar este processo.

### <a name="manual-configuration"></a>Configuração manual

Crie uma ligação RDP com a máquina virtual. Abra o PowerShell e execute este script.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Verificar o disco de dados

Para verificar que o disco de dados está ligado, veja `StorageProfile` para o `DataDisks` anexado.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

A saída deverá ter um aspeto semelhante a este exemplo:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu sobre os tópicos de discos de VM, como:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Expor e preparar os discos de dados

Avance para o próximo tutorial para saber mais sobre como automatizar a configuração da VM.

> [!div class="nextstepaction"]
> [Automatizar a configuração da VM](./tutorial-automate-vm-deployment.md)
