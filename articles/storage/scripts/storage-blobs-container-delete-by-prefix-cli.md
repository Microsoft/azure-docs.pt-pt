---
title: Exemplo do Script da CLI do Azure - Eliminar contentores pelo prefixo | Microsoft Docs
description: Elimine contentores de blobs do Armazenamento do Azure com base num prefixo do nome do contentor.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: 85ee6505adafab9587f3583cd4c7182efcc43c11
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853736"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Eliminar contentores com base no prefixo do nome do contentor

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
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Cria uma conta de Armazenamento do Azure no grupo de recursos especificado. |
| [az storage container create](/cli/azure/storage/container#az_storage_container_create) | Cria um contentor no armazenamento de Blobs do Azure. |
| [az storage container list](/cli/azure/storage/container) | Lista os contentores numa conta de Armazenamento do Azure. |
| [az storage container delete](/cli/azure/storage/container#az_storage_container_delete) | Elimina contentores numa conta de Armazenamento do Azure. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI de armazenamento nos [Exemplos da CLI do Azure para Armazenamento do Azure](../blobs/storage-samples-blobs-cli.md).
