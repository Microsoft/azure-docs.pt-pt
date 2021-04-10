---
title: Crie uma imagem instantânea de um disco rígido virtual usando o portal ou PowerShell
description: Aprenda a criar uma cópia de um VM Azure para usar como back up ou para problemas de resolução de problemas usando o portal ou PowerShell.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.author: rogarana
ms.openlocfilehash: 9070b69ac4c6b85791ff3dd4662273e75a3cd22c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556065"
---
# <a name="create-a-snapshot-using-the-portal-or-powershell"></a>Crie um instantâneo utilizando o portal ou PowerShell

Um instantâneo é uma cópia completa e só de leitura de um disco rígido virtual (VHD). Pode tirar uma fotografia de um SISTEMA ou VHD de disco de dados para usar como cópia de segurança ou para resolver problemas de máquina virtual (VM).

Se vai utilizar o instantâneo para criar um novo VM, recomendamos que desligue o VM de forma limpa antes de tirar uma foto, para limpar quaisquer processos em curso.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure 

Para criar um instantâneo, complete os seguintes passos: 
1.  No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso**.
2. Procure e selecione **Snapshot**.
3. Na janela **Snapshot,** **selecione Criar**. Aparece a janela **de instantâneo Create.**
4. Insira um **nome** para a foto.
5. Selecione um grupo de [Recursos](../../azure-resource-manager/management/overview.md#resource-groups) existente ou insira o nome de um novo. 
6. Selecione uma **Localização** para o datacenter do Azure.  
7. Para **o disco Source**, selecione o disco gerido para o instantâneo.
8. Selecione o **tipo de Conta** a utilizar para armazenar o instantâneo. Selecione **Standard_HDD**, a menos que precise que a fotografia seja armazenada num disco de alto desempenho.
9. Selecione **Criar**.

## <a name="use-powershell"></a>Utilizar o PowerShell

Os passos a seguir mostram como copiar o disco VHD e criar a configuração instantânea. Em seguida, pode tirar uma fotografia do disco utilizando o cmdlet [New-AzSnapshot.](/powershell/module/az.compute/new-azsnapshot) 

 

1. Definir alguns parâmetros: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Pegue o VM:

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. Crie a configuração instantânea. Para este exemplo, o instantâneo é do disco de so:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > Se quiser armazenar o seu instantâneo em armazenamento resiliente de zona, crie-o numa região que suporte [zonas de disponibilidade](../../availability-zones/az-overview.md) e inclua o `-SkuName Standard_ZRS` parâmetro.   
   
4. Tire a foto:

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Passos seguintes

Crie uma máquina virtual a partir de um instantâneo criando um disco gerido a partir de um instantâneo e, em seguida, anexando o novo disco gerido como o disco DE. Para obter mais informações, consulte a amostra em [Criar um VM a partir de uma foto com PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot).