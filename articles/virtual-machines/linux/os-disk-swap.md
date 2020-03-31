---
title: Troque o disco OS por um VM Azure utilizando cli '
description: Altere o disco do sistema operativo utilizado por uma máquina virtual Azure utilizando o CLI.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035336"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Alterar o disco OS utilizado por um VM Azure utilizando o CLI


Se tiver um VM existente, mas pretender trocar o disco por um disco de reserva ou outro disco OPERATIVO, pode utilizar o Azure CLI para trocar os discos OS. Não é preciso apagar e recriar o VM. Pode até utilizar um disco gerido noutro grupo de recursos, desde que ainda não esteja em uso.

O VM precisa de ser interrompido\deallocated, então o ID de recursos do disco gerido pode ser substituído com o ID de recurso de um disco gerido diferente. 

Certifique-se de que o tamanho VM e o tipo de armazenamento são compatíveis com o disco que pretende anexar. Por exemplo, se o disco que pretende utilizar estiver no Armazenamento Premium, então o VM precisa de ser capaz de armazenamento premium (como um tamanho da série DS).

Este artigo requer a versão Azure CLI 2.0.25 ou superior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli). 


Utilize a lista de [discos az](/cli/azure/disk) para obter uma lista dos discos do seu grupo de recursos.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Utilize a [z vm stop](/cli/azure/vm) para parar\desalocar o VM antes de trocar os discos.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Utilize a [atualização az vm](/cli/azure/vm#az-vm-update) com a `--osdisk` identificação completa do novo disco para o parâmetro 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Reiniciar o VM utilizando [az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Passos seguintes**

Para criar uma cópia de um disco, consulte [snapshot um disco](snapshot-copy-managed-disk.md).
