---
title: Como redimensionar uma VM Linux com o CLI do Azure
description: Como escalar ou reduzir verticalmente uma máquina virtual do Linux alterando o tamanho da VM.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: gwallace
editor: ''
tags: ''
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6f3f0a937b33b1c5dd1a68d86f80f870a7311d0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036884"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Redimensionar uma máquina virtual do Linux usando CLI do Azure 

Depois de provisionar uma VM (máquina virtual), você pode escalar ou reduzir verticalmente a VM alterando o [tamanho da VM][vm-sizes]. Em alguns casos, você deve desalocar a VM primeiro. Você precisa desalocar a VM se o tamanho desejado não estiver disponível no cluster de hardware que está hospedando a VM. Este artigo fornece detalhes sobre como redimensionar uma VM Linux com o CLI do Azure. 

## <a name="resize-a-vm"></a>Redimensionar uma VM
Para redimensionar uma VM, você precisa das [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e conectadas a uma conta do Azure usando [AZ login](/cli/azure/reference-index).

1. Exiba a lista de tamanhos de VM disponíveis no cluster de hardware em que a VM está hospedada com [AZ VM List-VM-redimension-Options](/cli/azure/vm). O exemplo a seguir lista os tamanhos de VM para a VM denominada `myVM` no grupo de recursos `myResourceGroup` região:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Se o tamanho de VM desejado estiver listado, redimensione a VM com [AZ VM Resize](/cli/azure/vm). O exemplo a seguir redimensiona a VM chamada `myVM` para o tamanho do `Standard_DS3_v2`:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    A VM é reiniciada durante esse processo. Após a reinicialização, o sistema operacional e os discos de dados existentes serão remapeados. Qualquer coisa no disco temporário é perdida.

3. Se o tamanho de VM desejado não estiver listado, você precisará primeiro desalocar a VM com [AZ VM DEALLOCATE](/cli/azure/vm). Esse processo permite que a VM seja redimensionada para qualquer tamanho disponível para o qual a região dá suporte e, em seguida, iniciada. As etapas a seguir desalocam, redimensionam e iniciam a VM denominada `myVM` no grupo de recursos denominado `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > A desalocação da VM também libera todos os endereços IP dinâmicos atribuídos à VM. O sistema operacional e os discos de dados não são afetados.

## <a name="next-steps"></a>Passos seguintes
Para obter escalabilidade adicional, execute várias instâncias de VM e expanda horizontalmente. Para obter mais informações, consulte [dimensionar automaticamente computadores Linux em um conjunto de dimensionamento de máquinas virtuais][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
