---
title: Criar uma app de função com armazenamento conectado - Azure CLI
description: Exemplo do Script da CLI do Azure - Criar uma Função do Azure que se liga a um Armazenamento do Azure
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 539c3a7dd95045b2e569dbb339be0e5a0c845902
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786293"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Criar uma aplicação de função com uma conexão de conta de armazenamento chamada 

Este script de exemplo das Funções do Azure cria uma aplicação de funções e liga a função a uma conta de Armazenamento do Azure. A definição da aplicação criada que contém a ligação pode ser utilizada com um [acionador ou enlace de armazenamento](../functions-bindings-storage-blob.md). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria uma aplicação de Funções do Azure e adiciona a cadeia de ligação de armazenamento a uma definição da aplicação.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Criar um grupo de recursos com localização. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Criar uma conta de armazenamento. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Cria uma aplicação de função no [plano de consumo](../consumption-plan.md)sem servidor. |
| [az conta de armazenamento show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string) | Obtenha a cadeia de ligação para a conta. |
| [az functionapp config conjunto de appsettings](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | Define a cadeia de ligação como uma definição de aplicação na aplicação de função. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
