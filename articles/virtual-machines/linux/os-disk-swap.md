---
title: Trocar o disco do sistema operacional para uma VM do Azure usando a CLI '
description: Altere o disco do sistema operacional usado por uma máquina virtual do Azure usando a CLI.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: bfbe47fb68ffe7cee1ee2f9f7b94b418d8da2a1d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035336"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Alterar o disco do sistema operacional usado por uma VM do Azure usando a CLI


Se você tiver uma VM existente, mas quiser trocar o disco por um disco de backup ou outro disco do sistema operacional, poderá usar o CLI do Azure para trocar os discos do sistema operacional. Você não precisa excluir e recriar a VM. Você pode até mesmo usar um disco gerenciado em outro grupo de recursos, desde que ele ainda não esteja em uso.

A VM precisa ser stopped\deallocated, então a ID de recurso do disco gerenciado pode ser substituída pela ID de recurso de um disco gerenciado diferente. 

Verifique se o tamanho da VM e o tipo de armazenamento são compatíveis com o disco que você deseja anexar. Por exemplo, se o disco que você deseja usar estiver no armazenamento Premium, a VM precisará ser capaz de armazenar o armazenamento Premium (como um tamanho da série DS).

Este artigo requer CLI do Azure versão 2.0.25 ou superior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 


Use [AZ Disk List](/cli/azure/disk) para obter uma lista dos discos em seu grupo de recursos.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Use [AZ VM Stop](/cli/azure/vm) para STOP\DEALLOCATE a VM antes de trocar os discos.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Use [AZ VM Update](/cli/azure/vm#az-vm-update) com a ID de recurso completa do novo disco para o parâmetro `--osdisk` 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Reinicie a VM usando [AZ VM Start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Passos seguintes**

Para criar uma cópia de um disco, consulte [instantâneo de um disco](snapshot-copy-managed-disk.md).
