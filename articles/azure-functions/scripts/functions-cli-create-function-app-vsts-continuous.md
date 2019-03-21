---
title: Criar uma função no Azure que é implementada a partir do Azure DevOps | Microsoft Docs
description: Criar uma Function App e implementar código de função a partir do Azure DevOps
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: azure-functions
ms.custom: mvc
ms.openlocfilehash: 7fe68090773902248dbcdd63fbbdbbdb06b307cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852394"
---
# <a name="create-a-function-app-and-deploy-function-code-from-azure-devops"></a>Criar uma Function App e implementar código de função a partir do Azure DevOps

Este tópico mostra como utilizar as Funções do Azure para criar uma aplicação de funções [sem servidor](https://azure.microsoft.com/solutions/serverless/) através do [plano de consumo](../functions-scale.md#consumption-plan). A aplicação de função, o que é um contentor para as suas funções, é continuamente implementada a partir de um repositório de DevOps do Azure. 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

Para concluir este tópico, tem de ter:

* Um repositório do Azure DevOps que contém o projeto da aplicação de funções e para o qual tem permissões administrativas.
* Um [token de acesso pessoal (PAT)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) para aceder ao seu repositório do Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se, em vez disso, utilizar a CLI do Azure localmente, terá de instalar e utilizar a versão 2.0 ou uma versão posterior. Para determinar a versão da CLI do Azure, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria uma Function App do Azure e implementa código de função a partir do Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma conta de armazenamento, uma aplicação de funções e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Cria a conta de armazenamento necessária para a aplicação de funções. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Cria uma aplicação de função no [plano de consumo](../functions-scale.md#consumption-plan) sem servidor. |
| [az functionapp deployment source config](https://docs.microsoft.com/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Associa uma aplicação de funções a um repositório do Git ou do Mercurial. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
