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
ms.openlocfilehash: 50a7d5e4a33762b5ae0725165e6062a83fec9110
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006286"
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
| [`az group create`](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Cria uma aplicação de Serviço de Aplicações. |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment#az-webapp-deployment-list-publishing-profiles) | Obtenha os detalhes para os perfis de implementação de aplicações disponíveis. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).
