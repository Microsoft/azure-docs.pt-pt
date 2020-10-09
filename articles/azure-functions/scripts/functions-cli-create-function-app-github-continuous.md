---
title: Criar uma aplicação de função com implantação GitHub - Azure CLI
description: Criar uma aplicação de funções e implementar o código de função a partir de um repositório do GitHub com as Funções do Azure.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f7c272fa95d2697d41cb751e39ef72b0da86a12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87498529"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Criar uma aplicação de funções no Azure que é implementada a partir do GitHub

Este script de amostra de Azure Functions cria uma aplicação de função utilizando o [plano de Consumo,](../functions-scale.md#consumption-plan)juntamente com os seus recursos relacionados. O script também configura o seu código de função para a implementação contínua de um repositório do GitHub. 

Neste exemplo, precisa de:

* Um repositório do GitHub com o código de funções, para o qual tem permissões administrativas.
* Um [Token de Acesso Pessoal (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) para a sua conta do GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se, em vez disso, utilizar a CLI do Azure localmente, terá de instalar e utilizar a versão 2.0 ou uma versão posterior. Para determinar a versão da CLI do Azure, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria uma Aplicação de Funções e implementa código de função a partir do GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Cada comando na tabela liga à documentação específica do comando. Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria a conta de armazenamento necessária para a aplicação de funções. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria uma aplicação de função no [plano de consumo](../functions-scale.md#consumption-plan) sem servidor e associa-a a um repositório Git ou Mercurial. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
