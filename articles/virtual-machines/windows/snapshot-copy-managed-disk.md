---
title: Criar um instantâneo de um VHD no Azure | Microsoft Docs
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
ms.openlocfilehash: 76110fa04441540875e65c8bc056fc21555c5db6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102288"
---
# <a name="create-a-snapshot"></a>Criar um instantâneo

Um instantâneo é uma cópia completa e somente leitura de um disco rígido virtual (VHD). Você pode tirar um instantâneo de um sistema operacional ou VHD de disco de dados para usar como um backup ou para solucionar problemas de VM (máquina virtual).

Se você pretende usar o instantâneo para criar uma nova VM, recomendamos que você desligue corretamente a VM antes de tirar um instantâneo, para limpar todos os processos em andamento.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **criar um recurso**e, em seguida, pesquise e selecione **instantâneo**.
3. Na janela **instantâneo** , selecione **criar**. A janela **criar instantâneo** é exibida.
4. Insira um **nome** para o instantâneo.
5. Selecione um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) existente ou insira o nome de um novo. 
6. Selecione uma **Localização** para o datacenter do Azure.  
7. Para **disco de origem**, selecione o disco gerenciado para instantâneo.
8. Selecione o **tipo de conta** a ser usado para armazenar o instantâneo. Selecione **Standard_HDD**, a menos que você precise que o instantâneo seja armazenado em um disco de alto desempenho.
9. Selecione **Criar**.

## <a name="use-powershell"></a>Utilizar o PowerShell

As etapas a seguir mostram como copiar o disco VHD, criar a configuração de instantâneo e tirar um instantâneo do disco usando o cmdlet [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) . 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

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
   > Se você quiser armazenar o instantâneo em um armazenamento com flexibilidade de zona, crie-o em uma região que ofereça suporte a [zonas](../../availability-zones/az-overview.md) de `-SkuName Standard_ZRS` disponibilidade e inclua o parâmetro.   
   
4. Faça o instantâneo:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Passos Seguintes

Crie uma máquina virtual de um instantâneo criando um disco gerenciado com base em um instantâneo e anexando o novo disco gerenciado como o disco do sistema operacional. Para obter mais informações, consulte o exemplo em [criar uma VM com base em um instantâneo com o PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
