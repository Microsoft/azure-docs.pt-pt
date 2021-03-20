---
title: Criar uma app de função com Azure Cosmos DB - Azure CLI
description: Exemplo do Script da CLI do Azure - Criar uma Função do Azure que se liga a um Azure Cosmos DB
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9ec4d3cb9d47608aa98075ba98aacfde51f341cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934429"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Criar uma Função do Azure que se liga a um Azure Cosmos DB

Este script de exemplo das Funções do Azure cria uma aplicação de funções e liga a função a uma base de dados do Azure Cosmos DB. A definição da aplicação criada que contém a ligação pode ser utilizada com um [acionador ou enlace do Azure Cosmos DB](../functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria uma aplicação de Funções do Azure e adiciona uma chave de acesso e um ponto final do Cosmos DB às definições da aplicação.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: cada comando na tabela está ligado a documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Criar um grupo de recursos com localização |
| [az storage accounts create](/cli/azure/storage/account#az-storage-account-create) | Criar uma conta de armazenamento |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria uma aplicação de função no [plano de consumo](../consumption-plan.md)sem servidor. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Crie uma base de dados do Azure Cosmos DB. |
| [az cosmosdb show](/cli/azure/cosmosdb#az-cosmosdb-show)| Obtém a ligação da conta de base de dados. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| As chaves da base de dados. |
| [az functionapp config conjunto de appsettings](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Define a cadeia de ligação como uma definição de aplicação na aplicação de função. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).




