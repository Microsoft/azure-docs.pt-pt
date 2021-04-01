---
title: Troca entre discos de SO utilizando CLI '
description: Mude o disco do sistema operativo utilizado por uma máquina virtual Azure utilizando o CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: adcbc5b0ba0056c79576e8d4c8ded2150b206513
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87371877"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Alterar o disco DE utilizado por um VM Azure utilizando o CLI


Se tiver um VM existente, mas pretender trocar o disco por um disco de backup ou outro disco DE, pode utilizar o CLI Azure para trocar os discos OS. Não é preciso apagar e recriar o VM. Pode até utilizar um disco gerido noutro grupo de recursos, desde que ainda não esteja a ser utilizado.

O VM precisa de ser interrompido\deallocated, em seguida, o ID de recurso do disco gerido pode ser substituído pelo ID de recurso de um disco gerido diferente. 

Certifique-se de que o tamanho e o tipo de armazenamento VM são compatíveis com o disco que pretende anexar. Por exemplo, se o disco que pretende utilizar estiver no Armazenamento Premium, então o VM tem de ser capaz de armazenamento Premium (como um tamanho da série DS).

Este artigo requer a versão 2.0.25 ou superior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli). 


Utilize [a lista de discos az](/cli/azure/disk) para obter uma lista dos discos do seu grupo de recursos.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Utilize [a paragem az vm](/cli/azure/vm) para parar\deallocate o VM antes de trocar os discos.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Utilize [a atualização az vm](/cli/azure/vm#az-vm-update) com o ID completo do novo disco para o `--osdisk` parâmetro 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Reinicie o VM utilizando [o arranque az vm](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Próximos passos**

Para criar uma cópia de um disco, consulte [Snapshot um disco](snapshot-copy-managed-disk.md).
