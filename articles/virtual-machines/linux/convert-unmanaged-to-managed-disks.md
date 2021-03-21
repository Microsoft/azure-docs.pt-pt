---
title: Converter um Linux VM de discos não geridos para discos geridos
description: Como converter um Linux VM de discos não geridos para discos geridos utilizando o Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1f3b62f8c05edffa1b55bf3d8cd24494b1c918bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558496"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual Linux de discos não geridos para discos geridos

Se tiver máquinas virtuais Linux existentes (VMs) que utilizam discos não geridos, pode converter os VMs para utilizar [discos geridos Azure](../managed-disks-overview.md). Este processo converte tanto o disco de SO como quaisquer discos de dados anexados.

Este artigo mostra-lhe como converter VMs utilizando o Azure CLI. Se precisar de o instalar ou atualizar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de começar
* [Reveja as FAQ sobre a migração para Discos Geridos.](../faq-for-disks.md#migrate-to-managed-disks)

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Os VHDs originais e a conta de armazenamento utilizada pela VM antes da conversão não são eliminados. Continuam a incorrer em custos. Para evitar a cobrança destes artefactos, elimine os blobs de VHD originais depois de verificar que a conversão está concluída. Se precisar de encontrar estes discos não ligados para os eliminar, consulte o nosso artigo [Encontre e elimine discos geridos e não geridos do Azure](find-unattached-disks.md)não ligados .

## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta secção abrange como converter VMs Azure de instância única de discos não geridos para discos geridos. (Se os seus VMs estiverem num conjunto de disponibilidade, consulte a secção seguinte.) Pode utilizar este processo para converter os VMs de discos não geridos premium (SSD) para discos geridos premium, ou de discos não geridos padrão (HDD) para discos geridos padrão.

1. Deallocate o VM usando [az vm deallocate](/cli/azure/vm). O exemplo a seguir é o VM nomeado `myVM` no grupo de recursos `myResourceGroup` denominado:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Converter o VM em discos geridos utilizando [az vm converter](/cli/azure/vm). O processo que se segue converte o VM `myVM` nomeado, incluindo o disco de oss e quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Inicie o VM após a conversão para discos geridos utilizando [o arranque az vm](/cli/azure/vm). Segue-se o VM nomeado `myVM` no grupo de recursos denominado `myResourceGroup` .

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Converter VMs em um conjunto de disponibilidade

Se os VMs que pretende converter para discos geridos estiverem num conjunto de disponibilidade, primeiro tem de converter o conjunto de disponibilidade para um conjunto de disponibilidade gerida.

Todos os VMs no conjunto de disponibilidade devem ser transabilitados antes de converter o conjunto de disponibilidade. Planeia converter todos os VMs em discos geridos depois de o conjunto de disponibilidade ter sido convertido para um conjunto de disponibilidade gerido. Em seguida, inicie todos os VMs e continue a funcionar normalmente.

1. Listar todos os VMs numa disponibilidade definida utilizando [a lista de disponibilidades az vm](/cli/azure/vm/availability-set). O exemplo a seguir enumera todos os VMs no conjunto de disponibilidades `myAvailabilitySet` nomeados no grupo de recursos `myResourceGroup` denominado:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Translocar todos os VMs utilizando [o deallocato az vm](/cli/azure/vm). O exemplo a seguir é o VM nomeado `myVM` no grupo de recursos `myResourceGroup` denominado:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converta a disponibilidade definida utilizando [o conjunto de disponibilidade az vm](/cli/azure/vm/availability-set). O exemplo a seguir converte o conjunto de disponibilidade nomeado `myAvailabilitySet` no grupo de recursos `myResourceGroup` denominado:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Converta todos os VMs em discos geridos utilizando [az vm converter](/cli/azure/vm). O processo que se segue converte o VM `myVM` nomeado, incluindo o disco de oss e quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Inicie todos os VMs após a conversão para discos geridos utilizando [o arranque az vm](/cli/azure/vm). Segue-se o VM nomeado `myVM` no grupo de recursos `myResourceGroup` denominado:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Converter usando o portal Azure

Também pode converter discos não geridos para discos geridos utilizando o portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o VM da lista de VMs no portal.
3. Na lâmina para o VM, selecione **Discos** do menu.
4. Na parte superior da lâmina **dos Discos,** selecione **Migrar para discos geridos**.
5. Se o seu VM estiver num conjunto de disponibilidade, haverá um aviso no Migrar para a lâmina **de discos geridos** que precisa para converter primeiro o conjunto de disponibilidade. O aviso deve ter um link que pode clicar para converter o conjunto de disponibilidade. Uma vez que o conjunto de disponibilidade é convertido ou se o seu VM não estiver num conjunto de disponibilidade, clique em **Migrar** para iniciar o processo de migração dos seus discos para discos geridos.

O VM será interrompido e reiniciado após a conclusão da migração.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as opções de armazenamento, consulte [a visão geral dos Discos Geridos Azure](../managed-disks-overview.md).
