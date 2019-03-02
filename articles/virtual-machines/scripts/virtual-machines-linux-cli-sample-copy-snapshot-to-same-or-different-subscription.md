---
title: Exemplo do Script da CLI do Azure – Copiar (mover) o instantâneo de um disco gerido para uma subscrição idêntica ou diferente com a CLI | Microsoft Docs
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
ms.openlocfilehash: d52e710cadbc37b8ad6c1de61f75df3b96704daa
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243912"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Copiar o instantâneo de um disco gerido para uma subscrição idêntica ou diferente com a CLI

Este script copia um instantâneo de um disco gerido para uma subscrição idêntica ou diferente. Utilize este script para os seguintes cenários:

1. Migre um instantâneo no armazenamento Premium (Premium_LRS) para o armazenamento Standard (Standard_LRS ou Standard_ZRS) para reduzir o custo.
1. Migre um instantâneo do armazenamento localmente redundante (Premium_LRS, Standard_LRS) para o armazenamento com redundância de zona (Standard_ZRS) para beneficiar a maior fiabilidade de armazenamento ZRS.
1. Mova um instantâneo para uma subscrição diferente na mesma região para retenção mais longa.

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

## <a name="next-steps"></a>Passos Seguintes

[Criar uma máquina virtual a partir de um instantâneo](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual e discos geridos adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
