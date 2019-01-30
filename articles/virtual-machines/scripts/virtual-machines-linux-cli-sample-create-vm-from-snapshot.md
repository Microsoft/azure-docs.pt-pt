---
title: Exemplo do Script da CLI do Azure - Criar uma VM a partir de um instantâneo | Microsoft Docs
description: Exemplo do Script da CLI do Azure - Criar uma VM a partir de um instantâneo
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: db0aa1781c3e35b68a59082cf7a1760f7e9a34b4
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239575"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Criar uma máquina virtual a partir de um instantâneo com a CLI

Este script cria uma máquina virtual a partir de um instantâneo de um disco do SO.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um disco gerido, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Obtém o instantâneo com o nome do instantâneo e nome do grupo de recursos. A propriedade do ID do objeto devolvido é utilizada para criar um disco gerido.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Cria discos geridos a partir de um instantâneo com o ID do instantâneo, o nome do disco, o tipo de armazenamento e o tamanho  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Cria uma VM com um disco do SO gerido |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
