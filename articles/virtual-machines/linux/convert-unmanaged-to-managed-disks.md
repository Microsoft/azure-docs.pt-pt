---
title: Converter uma máquina virtual Linux de discos não gerenciados em discos gerenciados
description: Como converter uma VM do Linux de discos não gerenciados em discos gerenciados usando CLI do Azure no modelo de implantação do Gerenciador de recursos
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 24024bc66e10937f1ae2fdc5130ffcd8c76c1763
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430982"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual Linux de discos não gerenciados em discos gerenciados

Se você tiver VMs (máquinas virtuais) do Linux existentes que usam discos não gerenciados, poderá converter as VMs para usar o [Managed disks do Azure](../linux/managed-disks-overview.md). Esse processo converte o disco do sistema operacional e todos os discos de dados anexados.

Este artigo mostra como converter VMs usando o CLI do Azure. Se você precisar instalá-lo ou atualizá-lo, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de começar
* Examine [as perguntas frequentes sobre migração para Managed disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Os VHDs originais e a conta de armazenamento utilizada pela VM antes da conversão não são eliminados. Continuam a incorrer em custos. Para evitar a cobrança destes artefactos, elimine os blobs de VHD originais depois de verificar que a conversão está concluída. Se você precisar encontrar esses discos desanexados para excluí-los, consulte nosso artigo [Localizar e excluir discos gerenciados e não geridos do Azure desconectados](find-unattached-disks.md).

## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta seção aborda como converter VMs do Azure de instância única de discos não gerenciados em discos gerenciados. (Se suas VMs estiverem em um conjunto de disponibilidade, consulte a próxima seção.) Você pode usar esse processo para converter as VMs de discos não gerenciados Premium (SSD) em discos gerenciados Premium ou discos não gerenciados padrão (HDD) em discos gerenciados Standard.

1. Desaloque a VM usando [AZ VM DEALLOCATE](/cli/azure/vm). O exemplo a seguir Desaloca a VM denominada `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Converta a VM em Managed disks usando [AZ VM Convert](/cli/azure/vm). O processo a seguir converte a VM chamada `myVM`, incluindo o disco do sistema operacional e os discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Inicie a VM após a conversão em Managed disks usando [AZ VM Start](/cli/azure/vm). O exemplo a seguir inicia a VM chamada `myVM` no grupo de recursos denominado `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Converter VMs em um conjunto de disponibilidade

Se as VMs que você deseja converter em discos gerenciados estiverem em um conjunto de disponibilidade, primeiro você precisará converter o conjunto de disponibilidade em um conjunto de disponibilidade gerenciado.

Todas as VMs no conjunto de disponibilidade devem ser desalocadas antes de converter o conjunto de disponibilidade. Planeje converter todas as VMs em discos gerenciados depois que o próprio conjunto de disponibilidade tiver sido convertido em um conjunto de disponibilidade gerenciado. Em seguida, inicie todas as VMs e continue a operar normalmente.

1. Liste todas as VMs em um conjunto de disponibilidade usando [AZ VM Availability-set list](/cli/azure/vm/availability-set). O exemplo a seguir lista todas as VMs no conjunto de disponibilidade chamado `myAvailabilitySet` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Desaloque todas as VMs usando [AZ VM DEALLOCATE](/cli/azure/vm). O exemplo a seguir Desaloca a VM denominada `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converta o conjunto de disponibilidade usando [AZ VM Availability-Set Convert](/cli/azure/vm/availability-set). O exemplo a seguir converte o conjunto de disponibilidade chamado `myAvailabilitySet` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Converta todas as VMs em Managed disks usando [AZ VM Convert](/cli/azure/vm). O processo a seguir converte a VM chamada `myVM`, incluindo o disco do sistema operacional e os discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Inicie todas as VMs após a conversão em Managed disks usando [AZ VM Start](/cli/azure/vm). O exemplo a seguir inicia a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Converter usando o portal do Azure

Você também pode converter discos não gerenciados em discos gerenciados usando o portal do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione a VM na lista de VMs no Portal.
3. Na folha da VM, selecione **discos** no menu.
4. Na parte superior da folha **discos** , selecione **migrar para Managed disks**.
5. Se sua VM estiver em um conjunto de disponibilidade, haverá um aviso na folha **migrar para discos gerenciados** que você precisa para converter o conjunto de disponibilidade primeiro. O aviso deve ter um link no qual você pode clicar para converter o conjunto de disponibilidade. Depois que o conjunto de disponibilidade for convertido ou a VM não estiver em um conjunto de disponibilidade, clique em **migrar** para iniciar o processo de migração de seus discos para o Managed disks.

A VM será interrompida e reiniciada após a conclusão da migração.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre opções de armazenamento, consulte [visão geral do Azure Managed disks](../windows/managed-disks-overview.md).
