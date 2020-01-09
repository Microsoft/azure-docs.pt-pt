---
title: Criar um instantâneo de um disco rígido virtual no Azure
description: Saiba como criar uma cópia de uma VM do Azure para usar como um backup ou para solucionar problemas.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: bc74a3eea1f99de6080788d6f3fddcac823092dc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75370906"
---
# <a name="create-a-snapshot"></a>Criar um instantâneo

Um instantâneo é uma cópia completa e somente leitura de um disco rígido virtual (VHD). Você pode tirar um instantâneo de um sistema operacional ou VHD de disco de dados para usar como um backup ou para solucionar problemas de VM (máquina virtual).

Se você pretende usar o instantâneo para criar uma nova VM, recomendamos que você desligue corretamente a VM antes de tirar um instantâneo, para limpar todos os processos em andamento.

## <a name="use-the-azure-portal"></a>Utilizar o Portal do Azure 

Para criar um instantâneo, conclua as seguintes etapas: 
1.  Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso**.
2. Pesquise e selecione **instantâneo**.
3. Na janela **instantâneo** , selecione **criar**. A janela **criar instantâneo** é exibida.
4. Insira um **nome** para o instantâneo.
5. Selecione um [grupo de recursos](../../azure-resource-manager/management/overview.md#resource-groups) existente ou insira o nome de um novo. 
6. Selecione uma **Localização** para o datacenter do Azure.  
7. Para **disco de origem**, selecione o disco gerenciado para instantâneo.
8. Selecione o **tipo de conta** a ser usado para armazenar o instantâneo. Selecione **Standard_HDD**, a menos que você precise que o instantâneo seja armazenado em um disco de alto desempenho.
9. Selecione **Criar**.

## <a name="use-powershell"></a>Utilizar o PowerShell

As etapas a seguir mostram como copiar o disco VHD e criar a configuração de instantâneo. Em seguida, você pode tirar um instantâneo do disco usando o cmdlet [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) . 

 

1. Defina alguns parâmetros: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Obter a VM:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Crie a configuração de instantâneo. Para este exemplo, o instantâneo é do disco do sistema operacional:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Se você quiser armazenar o instantâneo em um armazenamento com capacidade de zona, crie-o em uma região que ofereça suporte a [zonas de disponibilidade](../../availability-zones/az-overview.md) e inclua o parâmetro `-SkuName Standard_ZRS`.   
   
4. Faça o instantâneo:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Passos seguintes

Crie uma máquina virtual de um instantâneo criando um disco gerenciado com base em um instantâneo e anexando o novo disco gerenciado como o disco do sistema operacional. Para obter mais informações, consulte o exemplo em [criar uma VM com base em um instantâneo com o PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
