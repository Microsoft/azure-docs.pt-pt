---
title: Crie uma imagem de um disco rígido virtual em Azure
description: Aprenda a criar uma cópia de um VM Azure para usar como back up ou para problemas de resolução de problemas.
author: roygara
manager: twooley
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 92957bd078c04a9bb7ac35f9d30f042a44e10764
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100639"
---
# <a name="create-a-snapshot"></a>Criar um instantâneo

Um instantâneo é uma cópia completa e apenas de leitura de um disco rígido virtual (VHD). Pode tirar uma foto de um Sistema operativo ou de um vHD de disco de dados para usar como cópia de segurança ou para resolver problemas com a máquina virtual (VM).

Se vai usar o instantâneo para criar um novo VM, recomendamos que desligue o VM de forma limpa antes de tirar uma foto, para limpar quaisquer processos que estejam em curso.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure 

Para criar um instantâneo, complete os seguintes passos: 
1.  No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso**.
2. Procure e selecione **Snapshot**.
3. Na janela **Snapshot,** selecione **Criar**. A janela **de instantâneo Criar** aparece.
4. Introduza um **nome** para a foto.
5. Selecione um [grupo de Recursos](../../azure-resource-manager/management/overview.md#resource-groups) existente ou introduza o nome de um novo. 
6. Selecione uma **Localização** para o datacenter do Azure.  
7. Para **o disco Source,** selecione o disco gerido para instantâneo.
8. Selecione o **tipo de conta** a utilizar para armazenar o instantâneo. Selecione **Standard_HDD,** a menos que precise que o instantâneo seja guardado num disco de alto desempenho.
9. Selecione **Criar**.

## <a name="use-powershell"></a>Utilizar o PowerShell

Os seguintes passos mostram como copiar o disco VHD e criar a configuração instantânea. Em seguida, pode tirar uma foto do disco utilizando o cmdlet [New-AzSnapshot.](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) 

 

1. Definir alguns parâmetros: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Obtenha o VM:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Crie a configuração instantânea. Para este exemplo, o instantâneo é do disco OS:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Se você gostaria de armazenar seu instantâneo em armazenamento resiliente de zona, crie-o `-SkuName Standard_ZRS` em uma região que suporte zonas de [disponibilidade](../../availability-zones/az-overview.md) e inclua o parâmetro.   
   
4. Tire a foto:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Passos seguintes

Crie uma máquina virtual a partir de um instantâneo criando um disco gerido a partir de um instantâneo e, em seguida, anexando o novo disco gerido como o disco OS. Para mais informações, consulte a amostra em [Create a VM a partir de um instantâneo com powerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
