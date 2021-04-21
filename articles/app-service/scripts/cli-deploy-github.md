---
title: 'CLI: Implementar uma aplicação do GitHub'
description: Saiba como utilizar o Azure CLI para automatizar a implementação e gestão da sua aplicação De Serviço de Aplicações. Esta amostra mostra como implementar uma aplicação do GitHub.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 655c98e5249b66b114dfbe1d88cf951001d07af2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787880"
---
# <a name="create-an-app-service-app-with-deployment-from-github-using-azure-cli"></a>Criar uma aplicação de Serviço de Aplicações com implementação do GitHub usando o Azure CLI

Este script de amostra cria uma aplicação no Serviço de Aplicações com os seus recursos relacionados. Em seguida, implementa o seu código de aplicação a partir de um repositório público do GitHub (sem implantação contínua). Para a implementação do GitHub com implementação contínua, consulte [Criar uma aplicação com implementação contínua a partir do GitHub](cli-continuous-deployment-github.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Create an app with deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script 

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Cria uma aplicação de Serviço de Aplicações. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Associa uma aplicação de Serviço de Aplicações a um repositório Git ou Mercurial. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).
