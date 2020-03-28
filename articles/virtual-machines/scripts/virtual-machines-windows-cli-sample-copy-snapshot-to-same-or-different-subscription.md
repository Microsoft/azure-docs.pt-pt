---
title: Foto de imagem de um disco gerido para uma subscrição - Amostra CLI
description: Exemplo do Script da CLI do Azure – Copiar (mover) o instantâneo de um disco gerido para uma subscrição idêntica ou diferente com a CLI
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
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
ms.openlocfilehash: ddcf585af86a265410f9c2d424c85acf5abbc8fd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75375924"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Copiar o instantâneo de um disco gerido para uma subscrição idêntica ou diferente com a CLI

Este script copia um instantâneo de um disco gerido para uma subscrição idêntica ou diferente. Utilize este guião para os seguintes cenários:

1. Migrar uma foto no armazenamento Premium (Premium_LRS) para o armazenamento Standard (Standard_LRS ou Standard_ZRS) para reduzir o seu custo.
1. Migrar um instantâneo do armazenamento localmente redundante (Premium_LRS, Standard_LRS) para armazenamento redundante de zona (Standard_ZRS) para beneficiar da maior fiabilidade do armazenamento zRS.
1. Mova um instantâneo para uma subscrição diferente na mesma região para uma retenção mais longa.

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

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Máquina virtual adicional e amostras de script seletivas cli podem ser encontradas na [documentação Azure Windows VM](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
