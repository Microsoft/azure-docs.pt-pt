---
title: Disco de SO de troca para uma VM do Azure com a CLI | Documentos da Microsoft
description: Altere o disco do sistema operativo utilizado por uma máquina virtual do Azure com a CLI.
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
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 970f3409cc46fa6cf96fff3e6944ebeaeadcdcce
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667268"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Alterar o disco do SO utilizado por uma VM do Azure com a CLI


Se tiver uma VM existente, mas pretende trocar o disco para um disco de cópia de segurança ou de outro disco do SO, pode utilizar a CLI do Azure ao trocar os discos de SO. Não tem de eliminar e recriar a VM. Pode usar um disco gerido outro grupo de recursos,, desde que ainda não estiver em utilização.

A VM tem de ser stopped\deallocated, em seguida, o ID de recurso do disco gerido pode ser substituído com o ID de recurso de um disco gerido diferente. 

Certifique-se de que o tipo de armazenamento e tamanho VM são compatíveis com o disco que pretende anexar. Por exemplo, se o disco que pretende utilizar no armazenamento Premium, em seguida, a VM tem de ter a capacidade de armazenamento Premium (como um tamanho de séries de DS).

Este artigo requer a CLI do Azure versão 2.0.25 ou superior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 


Uso [lista de disco de az](/cli/azure/disk) para obter uma lista dos discos no grupo de recursos.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Uso [stop do az vm](/cli/azure/vm) para stop\deallocate a VM antes de passar os discos.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Uso [atualização do az vm](/cli/azure/vm#az-vm-update) com o ID de recurso completo do novo disco para o `--osdisk` parâmetro 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Reinicie a VM com [início de vm de az](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Passos seguintes?**

Para criar uma cópia de um disco, consulte [instantâneo de um disco](snapshot-copy-managed-disk.md).
