---
title: Criar uma app de função com armazenamento conectado - Azure CLI
description: Exemplo do Script da CLI do Azure - Criar uma Função do Azure que se liga a um Armazenamento do Azure
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3eec4e22f80d1efd180dc6770a781ee14db680be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87498553"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Criar uma aplicação de função com uma conexão de conta de armazenamento chamada 

Este script de exemplo das Funções do Azure cria uma aplicação de funções e liga a função a uma conta de Armazenamento do Azure. A definição da aplicação criada que contém a ligação pode ser utilizada com um [acionador ou enlace de armazenamento](../functions-bindings-storage-blob.md). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se utilizar a CLI localmente, certifique-se de que a versão em execução é a 2.0 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria uma aplicação de Funções do Azure e adiciona a cadeia de ligação de armazenamento a uma definição da aplicação.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Criar um grupo de recursos com localização. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Criar uma conta de armazenamento. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria uma aplicação de função no [plano de consumo](../functions-scale.md#consumption-plan)sem servidor. |
| [az conta de armazenamento show-connection-string](/cli/azure/storage/account#az-storage-account-show-connection-string) | Obtenha a cadeia de ligação para a conta. |
| [az functionapp config conjunto de appsettings](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Define a cadeia de ligação como uma definição de aplicação na aplicação de função. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
