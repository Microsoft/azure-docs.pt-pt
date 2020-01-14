---
title: Criar um aplicativo de funções com armazenamento conectado-CLI do Azure
description: Exemplo do Script da CLI do Azure - Criar uma Função do Azure que se liga a um Armazenamento do Azure
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc
ms.openlocfilehash: 833b9223d473c8bfc62485e9e47ba662a4f0e154
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922676"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Criar um aplicativo de funções com uma conexão de conta de armazenamento nomeada 

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
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria um aplicativo de funções no plano de [consumo](../functions-scale.md#consumption-plan)sem servidor. |
| [AZ Storage Account show-Connection-String](/cli/azure/storage/account#az-storage-account-show-connection-string) | Obtenha a cadeia de ligação para a conta. |
| [AZ functionapp config appSettings Set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Define a cadeia de conexão como uma configuração de aplicativo no aplicativo de funções. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
