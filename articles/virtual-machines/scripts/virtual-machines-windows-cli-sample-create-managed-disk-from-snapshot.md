---
title: Criar disco gerido a partir de snapshot (Windows) - Amostra CLI
description: Exemplo do Script da CLI do Azure – Criar um disco gerido a partir de um instantâneo
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 68435af52a9593d6b8c9fef0de66e867048919de
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028503"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli-windows"></a>Criar um disco gerido a partir de um instantâneo com CLI (Windows)

Este script cria um disco gerido a partir de um instantâneo. Utiliza-o para restaurar uma máquina virtual a partir de instantâneos do SO e discos de dados. Crie discos do SO e discos geridos de dados a partir dos respetivos instantâneos e, em seguida, crie uma nova máquina virtual ao anexar discos geridos. Também pode restaurar discos de dados de uma VM existente ao anexar discos de dados criados a partir de instantâneos.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um instantâneo. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Obtém todas as propriedades de um instantâneo através do nome e das propriedades do grupo de recursos do instantâneo. A propriedade do ID é utilizada para criar o disco gerido.  |
| [az disk create](/cli/azure/disk) | Cria um disco gerido com o ID de um instantâneo gerido |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Máquinas virtuais adicionais e discos geridos Amostras de script CLI podem ser encontradas na [documentação VM do Azure Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
