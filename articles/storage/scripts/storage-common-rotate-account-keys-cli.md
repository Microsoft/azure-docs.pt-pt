---
title: Exemplo do Script da CLI do Azure - Rodar chaves de acesso da conta de armazenamento | Microsoft Docs
description: Crie uma conta de armazenamento do Azure e, em seguida, obtenha e rode as chaves de acesso da conta.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 10/20/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 08e1b3837863b197f8463a0d969e78afab2b9858
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370411"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Criar uma conta de armazenamento e rodar as chaves de acesso da conta

Este script cria uma conta de Armazenamento do Azure, apresenta as novas chaves de acesso da conta de armazenamento e, em seguida, renova (roda) as chaves.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, a conta de armazenamento e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar a conta de armazenamento e obter e rodar as respetivas chaves de acesso. Cada item na tabela liga para documentação de comandos específicos.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account) | Cria uma conta de Armazenamento do Azure no grupo de recursos especificado. |
| [az storage account keys list](/cli/azure/storage/account/keys) | Apresenta as chaves de acesso da conta de armazenamento especificada. |
| [az storage account keys renew](/cli/azure/storage/account/keys) | Regenera a chave de acesso da conta de armazenamento primária ou secundária. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI de armazenamento nos [Exemplos da CLI do Azure para armazenamento de Blobs do Azure](../blobs/storage-samples-blobs-cli.md).
