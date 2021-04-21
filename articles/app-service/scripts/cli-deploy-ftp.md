---
title: 'CLI: Implementar ficheiros de aplicações com FTP'
description: Saiba como utilizar o Azure CLI para automatizar a implementação e gestão da sua aplicação De Serviço de Aplicações. Esta amostra mostra como criar uma aplicação e implementar ficheiros com FTP.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/12/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 875192b5da0c034f4ac92c74dd617ded79df7f45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788012"
---
# <a name="create-an-app-service-app-and-deploy-files-with-ftp-using-azure-cli"></a>Crie uma aplicação de Serviço de Aplicações e implemente ficheiros com FTP utilizando o Azure CLI

Este script de amostra cria uma aplicação no Serviço de Aplicações com os seus recursos relacionados e, em seguida, implementa uma página html estática usando FTP. Para o carregamento no FTP, o script utiliza o [cURL](https://en.wikipedia.org/wiki/CURL) como exemplo. Pode utilizar qualquer ferramenta FTP para carregar os ficheiros.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-ftp/deploy-ftp.sh "Create an app and deploy files with FTP")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script 

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Cria uma aplicação de Serviço de Aplicações. |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) | Obtenha os detalhes para os perfis de implementação de aplicações disponíveis. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).
