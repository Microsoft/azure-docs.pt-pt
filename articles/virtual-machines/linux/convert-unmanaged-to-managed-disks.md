---
title: Converter uma máquina virtual do Linux no Azure de discos não geridos para managed disks – Managed Disks do Azure | Documentos da Microsoft
description: Como converter uma VM do Linux de discos não geridos para discos geridos com a CLI do Azure no modelo de implementação do Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d1783890ba1b304ab658e827bfd24091e208fda4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699257"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual Linux de discos não geridos para managed disks

Se tiver existentes máquinas de virtuais de Linux (VMs) que utilizam discos não geridos, pode converter as VMs a utilizar [Managed Disks do Azure](../linux/managed-disks-overview.md). Este processo converte o disco do SO e qualquer discos de dados anexados.

Este artigo mostra-lhe como converter VMs com a CLI do Azure. Se precisar de instalar ou atualizá-lo, veja [instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de começar
* Revisão [as FAQ sobre a migração para os Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta secção abrange como converter VMs do Azure de instância única de discos não geridos para discos geridos. (Se forem as suas VMs num conjunto de disponibilidade, consulte a secção seguinte.) Pode utilizar este processo para converter as VMs de discos de discos de discos geridos premium, ou para standard (HDD) não geridos premium (SSD) não gerido para discos geridos standard.

1. Desaloque a VM ao utilizar [az vm deallocate](/cli/azure/vm). O exemplo seguinte desaloca a VM com o nome `myVM` no grupo de recursos com o nome `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Converter VM para managed disks, utilizando [az vm converter](/cli/azure/vm). O processo a seguir converte a VM com o nome `myVM`, incluindo o disco do SO e de quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Iniciar a VM após a conversão para discos geridos, utilizando [início de vm de az](/cli/azure/vm). O exemplo seguinte inicia a VM com o nome `myVM` no grupo de recursos com o nome `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Converter VMs num conjunto de disponibilidade

Se as VMs que pretende converter para gerido discos estão num conjunto de disponibilidade, tem primeiro de converter o conjunto de disponibilidade para um conjunto de disponibilidade gerido.

Todas as VMs no conjunto de disponibilidade tem de ser desalocadas antes de converter o conjunto de disponibilidade. Plano para converter todas as VMs em discos geridos depois do conjunto próprio de disponibilidade foi convertida num conjunto de disponibilidade gerido. Em seguida, iniciar todas as VMs e continuar a funcionar normalmente.

1. Listar todas as VMs num conjunto através de disponibilidade [lista de conjunto de disponibilidade de VMS de az](/cli/azure/vm/availability-set). O exemplo seguinte lista todas as VMs no conjunto de disponibilidade `myAvailabilitySet` no grupo de recursos com o nome `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Desalocar todas as VMs ao utilizar [az vm deallocate](/cli/azure/vm). O exemplo seguinte desaloca a VM com o nome `myVM` no grupo de recursos com o nome `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converter a conjunto de disponibilidade, utilizando [converter o conjunto de disponibilidade az vm](/cli/azure/vm/availability-set). O exemplo a seguir converte a conjunto de disponibilidade designado `myAvailabilitySet` no grupo de recursos com o nome `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Converter todas as VMs em discos geridos, utilizando [az vm converter](/cli/azure/vm). O processo a seguir converte a VM com o nome `myVM`, incluindo o disco do SO e de quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Iniciar todas as VMs após a conversão para discos geridos, utilizando [início de vm de az](/cli/azure/vm). O exemplo seguinte inicia a VM com o nome `myVM` no grupo de recursos com o nome `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre as opções de armazenamento, consulte [descrição geral do Managed Disks do Azure](../windows/managed-disks-overview.md).
