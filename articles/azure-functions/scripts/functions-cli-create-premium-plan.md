---
title: Criar uma aplicação de função num plano Premium - Azure CLI
description: Criar uma aplicação de função num plano Premium escalável em Azure utilizando o Azure CLI
ms.service: azure-functions
ms.topic: sample
ms.date: 11/23/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7d9f72fa433364f8d71ba44207d570bb827cd243
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786219"
---
# <a name="create-a-function-app-in-a-premium-plan---azure-cli"></a>Criar uma aplicação de função num plano Premium - Azure CLI

Este script de exemplo das Funções do Azure cria uma aplicação de funções, que é um contentor para as suas funções. A aplicação de função criada utiliza um [plano Premium escalável.](../functions-premium-plan.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo

Este script cria uma aplicação de função utilizando um [plano Premium.](../functions-premium-plan.md)

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-premium-plan/create-function-app-premium-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Cada comando na tabela liga à documentação específica do comando. Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Cria uma Conta de armazenamento do Azure. |
| [plano az functionapp criar](/cli/azure/functionapp/plan#az_functionapp_plan_create) | Cria um plano Premium num [SKU específico.](../functions-premium-plan.md#available-instance-skus) |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Cria uma aplicação de funções no plano do Serviço de Aplicações. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
