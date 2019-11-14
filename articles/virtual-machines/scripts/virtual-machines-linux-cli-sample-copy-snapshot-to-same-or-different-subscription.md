---
title: Exemplo do Script da CLI do Azure – Copiar (mover) o instantâneo de um disco gerido para uma subscrição idêntica ou diferente com a CLI
description: Exemplo do Script da CLI do Azure – Copiar (mover) o instantâneo de um disco gerido para uma subscrição idêntica ou diferente com a CLI
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 23886256e405f57c5a581356b4212498779ae6e5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038246"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Copiar o instantâneo de um disco gerido para uma subscrição idêntica ou diferente com a CLI

Este script copia um instantâneo de um disco gerido para uma subscrição idêntica ou diferente. Use este script para os seguintes cenários:

1. Migre um instantâneo no armazenamento Premium (Premium_LRS) para o armazenamento Standard (Standard_LRS ou Standard_ZRS) para reduzir seu custo.
1. Migre um instantâneo do armazenamento com redundância local (Premium_LRS, Standard_LRS) para o armazenamento com redundância de zona (Standard_ZRS) para se beneficiar da maior confiabilidade do armazenamento ZRS.
1. Mova um instantâneo para uma assinatura diferente na mesma região para maior retenção.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um instantâneo na subscrição de destino com o ID do instantâneo de origem. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Obtém todas as propriedades de um instantâneo através do nome e das propriedades do grupo de recursos do instantâneo. A propriedade do ID é utilizada para copiar o instantâneo para uma subscrição diferente.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Copia um instantâneo através da criação de um instantâneo numa subscrição diferente com o ID e o nome do instantâneo principal.  |

## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual a partir de um instantâneo](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual e discos geridos adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
