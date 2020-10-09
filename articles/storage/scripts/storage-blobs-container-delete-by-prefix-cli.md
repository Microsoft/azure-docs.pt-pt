---
title: Exemplo do Script da CLI do Azure - Eliminar contentores pelo prefixo | Microsoft Docs
description: Elimine os recipientes de bolhas de armazenamento Azure com base num prefixo do nome do recipiente e, em seguida, limpe a colocação. Consulte links de ajuda para comandos utilizados na amostra de script.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: aeccf255004cd4512fbc591942324341504b20f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87901900"
---
# <a name="use-an-azure-cli-script-to-delete-containers-based-on-container-name-prefix"></a>Utilize um script Azure CLI para apagar contentores com base no prefixo do nome do recipiente

Este script cria primeiro alguns contentores de exemplo no armazenamento do Blob do Azure e, em seguida, elimina alguns dos contentores com base num prefixo no nome do contentor.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, os restantes contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para eliminar contentores com base no prefixo do nome do contentor. Cada item na tabela liga para documentação de comandos específicos.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account) | Cria uma conta de Armazenamento do Azure no grupo de recursos especificado. |
| [Criação de contentores de armazenamento az](/cli/azure/storage/container) | Cria um contentor no armazenamento de Blobs do Azure. |
| [az storage container list](/cli/azure/storage/container) | Lista os contentores numa conta de Armazenamento do Azure. |
| [Eliminação de contentores de armazenamento az](/cli/azure/storage/container) | Elimina contentores numa conta de Armazenamento do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI de armazenamento nos [Exemplos da CLI do Azure para Armazenamento do Azure](../blobs/storage-samples-blobs-cli.md).
