---
title: Tutorial – Gerir discos do Azure com o Azure PowerShell
description: Neste tutorial, vai aprender a utilizar o Azure PowerShell para criar e gerir discos do Azure para máquinas virtuais
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a61d7425a7a907230008ab1d4f15a836150e1518
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549061"
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

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="default-azure-disks"></a>Discos do Azure predefinidos

Quando uma máquina virtual do Azure é criada, dois discos são automaticamente expostos à máquina virtual. 

**Disco do sistema operativo** - os discos do sistema operativo podem ter o tamanho máximo de 4 terabytes e alojam o sistema operativo das VMs. Se criar uma nova máquina virtual (VM) a partir de uma imagem do [Azure Marketplace,](https://azure.microsoft.com/marketplace/) a tipicamente 127 GB (mas algumas imagens têm tamanhos de disco de SO menores). O disco do SO é atribuído a uma letra de unidade de *C:* por predefinição. A configuração da colocação em cache do disco do SO está otimizada para desempenho do SO. O disco do SO **não deve** alojar aplicações ou dados. Para aplicações e dados, utilize um disco de dados, que se encontra detalhado posteriormente neste artigo.

**Disco temporário** - os discos temporários utilizam uma unidade de estado sólido que está localizada no mesmo anfitrião da VM do Azure. Os discos temporários são de elevado desempenho e servem para operações como o processamento de dados temporários. No entanto, se a VM for movida para um novo anfitrião, todos os dados armazenados num disco temporário são removidos. O tamanho do disco temporário é determinado pelo [tamanho da VM](../sizes.md). Os discos temporários estão atribuídos a uma letra de unidade de *D:* por predefinição.

## <a name="azure-data-disks"></a>Discos de dados do Azure

Podem ser adicionados mais discos para instalar aplicações e armazenar dados. Os discos de dados devem ser utilizados em qualquer situação em que seja preciso armazenamento de dados duradouro e reativo. O tamanho da máquina virtual determina quantos discos de dados podem ser expostos a uma VM.

## <a name="vm-disk-types"></a>Tipos de disco de VM

O Azure oferece dois tipos de discos.

**Discos Standard** - são apoiados por HDDs e oferecem armazenamento económico, mantendo o desempenho. Os discos Standard são ideais para uma carga de trabalho de desenvolvimento e teste económica.

Discos premium - **apoiados** por disco ssd baseado, de alto desempenho, de baixa latência. São perfeitos para as VMs com carga de trabalho de produção. Tamanhos VM com um  **S** no [nome de tamanho,](../vm-naming-conventions.md)normalmente suportam o Armazenamento Premium. Por exemplo, séries DS, séries DSv2, sérieS GS e VMs da série FS suportam armazenamento premium. Quando seleciona um tamanho de disco, o valor é arredondado para o tipo seguinte. Por exemplo, se o tamanho do disco for superior a 64 GB, mas inferior a 128 GB, o tipo de disco é P10. 
<br>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Ao providenciar um disco de armazenamento premium, ao contrário do armazenamento padrão, é-lhe garantida a capacidade, iOPS e a produção desse disco. Por exemplo, se criar um disco P50, o Azure prevê capacidade de armazenamento de 4.095 GB, 7.500 IOPS e 250-MB/s para o disco. A sua aplicação pode utilizar a maior ou parte da capacidade e desempenho. Os discos Premium SSD são projetados para fornecer latências de um dígito baixo e IOPS alvo e produção descrita na tabela anterior 99,9% das vezes.

Enquanto a tabela acima identifica o IOPS máximo por disco, um nível mais elevado de desempenho pode ser alcançado ao repartir vários discos de dados. Por exemplo, podem ser anexados 64 discos de dados à VM Standard_GS5. Se cada um destes discos for dimensionado como um P30, pode ser alcançado um máximo de 80.000 IOPS. Para obter informações detalhadas sobre o IOPS máximo por VM, veja [Tipos e tamanhos de VMs](../sizes.md).

## <a name="create-and-attach-disks"></a>Criar e expor discos

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente. Se for preciso, crie uma máquina virtual com os seguintes comandos.

Defina o nome de utilizador e a palavra-passe necessários para a conta de administrador na máquina virtual com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):


Crie a máquina virtual com [New-AzVM](/powershell/module/az.compute/new-azvm). Será pedido para introduzir um nome de utilizador e palavra-passe para a conta de administradores para a VM.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Crie a configuração inicial com [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig). O exemplo a seguir configura um disco com 128 gigabytes.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Crie o disco de dados com o comando [New-AzDisk.](/powershell/module/az.compute/new-azdisk)

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Obtenha a máquina virtual a que pretende adicionar o disco de dados com o comando [Get-AzVM.](/powershell/module/az.compute/get-azvm)

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Adicione o disco de dados à configuração da máquina virtual com o comando [Add-AzVMDataDisk.](/powershell/module/az.compute/add-azvmdatadisk)

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Atualize a máquina virtual com o comando [Update-AzVM.](/powershell/module/az.compute/add-azvmdatadisk)

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Preparar dados de discos

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


## <a name="next-steps"></a>Passos seguintes

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
