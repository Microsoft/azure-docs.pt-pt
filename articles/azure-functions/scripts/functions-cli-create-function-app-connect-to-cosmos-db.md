---
title: Criar um aplicativo de funções com Azure Cosmos DB-CLI do Azure
description: Exemplo do Script da CLI do Azure - Criar uma Função do Azure que se liga a um Azure Cosmos DB
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc
ms.openlocfilehash: d881a5eb6132f95239418f032da51d002af01905
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532870"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Criar uma Função do Azure que se liga a um Azure Cosmos DB

Este script de exemplo das Funções do Azure cria uma aplicação de funções e liga a função a uma base de dados do Azure Cosmos DB. A definição da aplicação criada que contém a ligação pode ser utilizada com um [acionador ou enlace do Azure Cosmos DB](../functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se utilizar a CLI localmente, certifique-se de que a versão em execução é a 2.0 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

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
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria uma aplicação de função no [plano de consumo](../functions-scale.md#consumption-plan) sem servidor. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Crie uma base de dados do Azure Cosmos DB. |
| [AZ cosmosdb show](/cli/azure/cosmosdb#az-cosmosdb-show)| Obtém a conexão da conta do banco de dados. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| Obtém as chaves para o banco de dados. |
| [AZ functionapp config appSettings Set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Define a cadeia de conexão como uma configuração de aplicativo no aplicativo de funções. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).




