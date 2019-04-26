---
title: Exemplo do Script da CLI do Azure - Criar uma Aplicação de Funções num plano do Serviço de Aplicações | Microsoft Docs
description: Exemplo do Script da CLI do Azure - Criar uma Aplicação de Funções num plano do Serviço de Aplicações
services: functions
documentationcenter: functions
author: ggailey777
manager: jeconnoc
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/03/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 85fe8e567cd00decbefe79c77c11f777820080ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325685"
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Criar uma Aplicação de Funções num plano do Serviço de Aplicações

Este script de exemplo das Funções do Azure cria uma aplicação de funções, que é um contentor para as suas funções. A aplicação de funções que é criada utiliza um plano do Serviço de Aplicações dedicado, o que significa que os recursos do servidor estão sempre ativos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este script cria uma aplicação das Funções do Azure com o [plano do Serviço de Aplicações](../functions-scale.md#app-service-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Cada comando na tabela liga à documentação específica do comando. Este script utiliza os seguintes comandos:

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Cria uma Conta de armazenamento do Azure. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Cria um plano do Serviço de Aplicações. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Cria uma aplicação de funções no plano do Serviço de Aplicações. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI das Funções do Azure adicionais na [Documentação das Funções do Azure](../functions-cli-samples.md).
