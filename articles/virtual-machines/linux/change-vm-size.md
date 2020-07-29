---
title: Como redimensionar um Linux VM com o Azure CLI
description: Como escalar ou escalar uma máquina virtual Linux, alterando o tamanho VM.
author: DavidCBerry13
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/10/2017
ms.author: daberry
ms.openlocfilehash: a93b1dcfe803fdead0d741553c45894f1675372b
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372268"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Redimensionar uma máquina virtual do Linux com a CLI do Azure 

Depois de providenciar uma máquina virtual (VM), pode escalar o VM para cima ou para baixo alterando o [tamanho VM][vm-sizes]. Em alguns casos, primeiro tem de negociar com o VM. Você precisa de negociar o VM se o tamanho desejado não estiver disponível no cluster de hardware que está hospedado no VM. Este artigo detalha como redimensionar um Linux VM com o Azure CLI. 

## <a name="resize-a-vm"></a>Redimensionar uma VM
Para redimensionar um VM, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index).

1. Veja a lista de tamanhos VM disponíveis no cluster de hardware onde o VM está hospedado com [opções de redimensionação az vm list-vm- resize](/cli/azure/vm). O exemplo a seguir enumera os tamanhos de VM para o VM nomeado `myVM` na região do grupo de `myResourceGroup` recursos:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Se o tamanho VM desejado for listado, redimensione o VM com [redimensionado az vm](/cli/azure/vm). O exemplo a seguir redimensiona o VM nomeado `myVM` para o `Standard_DS3_v2` tamanho:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    O VM reinicia durante este processo. Após o reinício, o seu sistema operativo e discos de dados existentes são rempeitados. Tudo no disco temporário está perdido.

3. Se o tamanho VM desejado não estiver listado, você precisa primeiro de negociar o VM com [az vm deallocate](/cli/azure/vm). Este processo permite que o VM seja então redimensionado para qualquer tamanho disponível que a região suporta e depois iniciado. Os passos seguintes negoceiam, redimensionam e, em seguida, iniciam o VM nomeado `myVM` no grupo de recursos `myResourceGroup` denominado:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > A deallocação do VM também lança quaisquer endereços IP dinâmicos atribuídos ao VM. O SO e os discos de dados não são afetados.

## <a name="next-steps"></a>Passos seguintes
Para uma escalabilidade adicional, executar vários casos de VM e escalar para fora. Para obter mais informações, consulte [automaticamente as máquinas Linux num conjunto de balanças de máquinas virtuais.][scale-set] 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/tutorial-autoscale-cli.md 
[vm-sizes]:sizes.md
