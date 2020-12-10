---
title: 'CLI: Monitorize uma aplicação com registos de servidores web'
description: Saiba como utilizar o Azure CLI para automatizar a implementação e gestão da sua aplicação De Serviço de Aplicações. Esta amostra mostra como monitorizar uma aplicação com registos de servidores web.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 9881a5f37c32849fc4f10acda8346510977e6b7b
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005792"
---
# <a name="monitor-an-app-service-app-with-web-server-logs-using-azure-cli"></a>Monitorize uma aplicação de Serviço de Aplicações com registos de servidores web usando O Azure CLI

Este script de amostra cria um grupo de recursos, plano de Serviço de Aplicações e app, e configura a aplicação para ativar registos de servidores web. Em seguida, transfere os ficheiros de registo para revisão.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "Monitor Logs")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, app App Service e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Cria uma aplicação de Serviço de Aplicações. |
| [`az webapp log config`](/cli/azure/webapp/log#az-webapp-log-config) | As configurações que registam uma aplicação do Serviço de Aplicações persistem. |
| [`az webapp log download`](/cli/azure/webapp/log#az-webapp-log-download) | Descarrega os registos de uma aplicação do Serviço de Aplicações para a sua máquina local. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).
