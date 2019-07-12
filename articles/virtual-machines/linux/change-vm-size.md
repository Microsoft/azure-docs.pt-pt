---
title: Como redimensionar uma VM do Linux com a CLI do Azure | Documentos da Microsoft
description: Como aumentar ou reduzir verticalmente uma máquina virtual do Linux, ao alterar o tamanho da VM.
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
ms.openlocfilehash: 46baa3d4dbcd466944d7a91e446e380c89f53f2b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671742"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Redimensionar a máquina virtual do Linux com a CLI do Azure 

Depois de aprovisionar uma máquina virtual (VM), pode aumentar ou reduzir verticalmente a VM ao alterar o [tamanho da VM][vm-sizes]. Em alguns casos, deve desalocar a VM pela primeira vez. Terá de desalocar a VM se o tamanho pretendido não está disponível no cluster de hardware que está a alojar a VM. Este artigo detalha como redimensionar uma VM do Linux com a CLI do Azure. 

## <a name="resize-a-vm"></a>Redimensionar uma VM
Redimensionar uma VM, terá da versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index).

1. Ver a lista de tamanhos VM disponíveis no cluster de hardware em que a VM está alojada com [az vm lista-vm-resize-options](/cli/azure/vm). O exemplo seguinte lista os tamanhos de VM para VM com o nome `myVM` no grupo de recursos `myResourceGroup` região:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Se o tamanho desejado da VM estiver listado, redimensione a VM com [az vm redimensionar](/cli/azure/vm). O exemplo seguinte redimensiona a VM com o nome `myVM` para o `Standard_DS3_v2` tamanho:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    A VM é reiniciada durante este processo. Após o reinício, o seu sistema operacional atual e os discos de dados são remapeados. Qualquer coisa no disco temporário é perdida.

3. Se o tamanho da VM pretendido não estiver listado, tem de primeiro desaloque a VM com [az vm deallocate](/cli/azure/vm). Este processo permite que a VM, em seguida, ser redimensionada para qualquer tamanho disponível que a região suporta e, em seguida, é iniciado. Os seguintes passos desalocar, redimensionar e, em seguida, inicie a VM com o nome `myVM` no grupo de recursos com o nome `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > A desalocar a VM também libera a qualquer endereços IP dinâmicos atribuídos à VM. Os discos de SO e dados não são afetados.

## <a name="next-steps"></a>Passos seguintes
Para escalabilidade adicional, execute várias instâncias VM e aumentar horizontalmente. Para obter mais informações, consulte [Dimensionar automaticamente máquinas do Linux num conjunto de dimensionamento de Máquina Virtual][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
