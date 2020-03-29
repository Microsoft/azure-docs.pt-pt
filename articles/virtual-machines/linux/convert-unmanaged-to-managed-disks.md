---
title: Converter um VM Linux de discos não geridos para discos geridos
description: Como converter um VM Linux de discos não geridos para discos geridos utilizando o Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2774dcbd5fc5b01627b965c2c02d870412c8bf77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969697"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual Linux de discos não geridos para discos geridos

Se tiver máquinas virtuais Linux existentes (VMs) que utilizam discos não geridos, pode converter os VMs para utilizar [discos geridos pelo Azure](../linux/managed-disks-overview.md). Este processo converte tanto o disco OS como quaisquer discos de dados anexados.

Este artigo mostra-lhe como converter VMs utilizando o Azure CLI. Se precisar de instalá-lo ou atualizá-lo, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de começar
* Reveja [as FAQ sobre a migração para Discos Geridos](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Os VHDs originais e a conta de armazenamento utilizada pela VM antes da conversão não são eliminados. Continuam a incorrer em custos. Para evitar a cobrança destes artefactos, elimine os blobs de VHD originais depois de verificar que a conversão está concluída. Se precisar de encontrar estes discos não ligados para os eliminar, consulte o nosso artigo [Encontre e elimine discos não ligados geridos e não geridos](find-unattached-disks.md).

## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta secção cobre a forma de converter VMs Azure de uma única instância de discos não geridos para discos geridos. (Se os seus VMs estiverem num conjunto de disponibilidade, consulte a secção seguinte.) Pode utilizar este processo para converter os VMs de discos não geridos premium (SSD) para discos geridos premium, ou de discos não geridos standard (HDD) para discos geridos padrão.

1. Deslocar o VM utilizando [adesalações az vm](/cli/azure/vm). O exemplo seguinte desafeta `myVM` o VM `myResourceGroup`nomeado no grupo de recursos denominado:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Converta o VM em discos geridos utilizando [a z vm converter](/cli/azure/vm). O seguinte processo converte `myVM`o VM nomeado, incluindo o disco OS e quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Inicie o VM após a conversão para discos geridos utilizando [az vm start](/cli/azure/vm). O exemplo seguinte inicia `myVM` o VM `myResourceGroup`nomeado no grupo de recursos chamado .

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Converter VMs num conjunto de disponibilidade

Se os VMs que pretende converter para discos geridos estiverem num conjunto de disponibilidade, primeiro precisa converter o conjunto de disponibilidade para um conjunto de disponibilidade gerido.

Todos os VMs no conjunto de disponibilidade devem ser deallocalizados antes de converter o conjunto de disponibilidade. Planeie converter todos os VMs em discos geridos depois de o conjunto de disponibilidade em si ter sido convertido para um conjunto de disponibilidade gerido. Em seguida, inicie todos os VMs e continue a funcionar normalmente.

1. Enumerar todos os VMs num conjunto de disponibilidade utilizando a lista de [disponibilidade az vm](/cli/azure/vm/availability-set). O exemplo seguinte lista todos os VMs no conjunto de disponibilidade nomeado `myAvailabilitySet` no grupo de recursos denominado: `myResourceGroup`

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Deslocar todos os VMs utilizando [adesalações az vm](/cli/azure/vm). O exemplo seguinte desafeta `myVM` o VM `myResourceGroup`nomeado no grupo de recursos denominado:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converta a disponibilidade definida utilizando a conversão [az vm de disponibilidade](/cli/azure/vm/availability-set). O exemplo seguinte converte o `myAvailabilitySet` conjunto de `myResourceGroup`disponibilidade nomeado no grupo de recursos denominado:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Converta todos os VMs em discos geridos utilizando [a z vm converte .](/cli/azure/vm) O seguinte processo converte `myVM`o VM nomeado, incluindo o disco OS e quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Inicie todos os VMs após a conversão para discos geridos utilizando [az vm start](/cli/azure/vm). O exemplo seguinte inicia `myVM` o VM `myResourceGroup`nomeado no grupo de recursos denominado:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Converter usando o portal Azure

Também pode converter discos não geridos para discos geridos utilizando o portal Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione o VM da lista de VMs no portal.
3. Na lâmina para o VM, selecione **Discos** do menu.
4. Na parte superior da lâmina **dos Discos,** selecione **Migrar para discos geridos**.
5. Se o seu VM estiver num conjunto de disponibilidade, haverá um aviso na lâmina de **discos gerido seletiva** que precisa para converter primeiro o conjunto de disponibilidade. O aviso deve ter um link que pode clicar para converter o conjunto de disponibilidade. Uma vez que o conjunto de disponibilidade é convertido ou se o seu VM não estiver num conjunto de disponibilidade, clique em **Migrar** para iniciar o processo de migração dos seus discos para discos geridos.

O VM será parado e reiniciado após a migração estar completa.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre opções de armazenamento, consulte a [visão geral dos Discos Geridos do Azure](../windows/managed-disks-overview.md).
