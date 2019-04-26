---
title: Criar uma Função do Azure que se liga ao Armazenamento do Azure | Microsoft Docs
description: Exemplo do Script da CLI do Azure - Criar uma Função do Azure que se liga a um Armazenamento do Azure
services: functions
documentationcenter: functions
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 48f6124373d80d22766d319ec58ed4ad7adaf6f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325668"
---
# <a name="create-a-function-app-that-connects-to-an-azure-storage-account"></a>Criar uma aplicação de funções que se liga a uma conta de Armazenamento do Azure

Este script de exemplo das Funções do Azure cria uma aplicação de funções e liga a função a uma conta de Armazenamento do Azure. A definição da aplicação criada que contém a ligação pode ser utilizada com um [acionador ou enlace de armazenamento](../functions-bindings-storage-blob.md). 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se utilizar a CLI localmente, certifique-se de que a versão em execução é a 2.0 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria uma aplicação de Funções do Azure e adiciona a cadeia de ligação de armazenamento a uma definição da aplicação.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]


## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, execute o seguinte comando para remover o grupo de recursos e todos os recursos relacionados:

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Criar um grupo de recursos com localização. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Criar uma conta de armazenamento. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Cria uma aplicação de função no [plano de consumo](../functions-scale.md#consumption-plan) sem servidor. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
