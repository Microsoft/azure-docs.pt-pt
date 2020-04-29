---
title: Exemplo do Script da CLI do Azure - Calcular o tamanho do contentor do blob | Microsoft Docs
description: Calcule o tamanho de um contentor no armazenamento de Blobs do Azure ao calcular o tamanho total dos blobs no contentor.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: 83ec8c7b3bf5ba9d23d50d8fa1bce563dc56c135
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067114"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcular o tamanho de um contentor de armazenamento de Blobs

Este script calcula o tamanho de um contentor no armazenamento do Blobs do Azure ao calcular o tamanho total dos blobs no contentor.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Este script da CLI indica um tamanho estimado do contentor e não deve ser utilizado para cálculos de faturação.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, o contentor e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para calcular o tamanho do contentor de armazenamento de Blobs. Cada item na tabela liga para documentação de comandos específicos.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage blob upload](/cli/azure/storage/account) | Carrega ficheiros locais para um contentor de armazenamento de Blobs do Azure. |
| [az storage blob list](/cli/azure/storage/account/keys) | Lista os blobs num contentor de armazenamento de Blobs do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI de armazenamento nos [Exemplos da CLI do Azure para armazenamento de Blobs do Azure](../blobs/storage-samples-blobs-cli.md).
