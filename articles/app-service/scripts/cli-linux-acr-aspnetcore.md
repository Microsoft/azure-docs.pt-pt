---
title: CLI do Azure exemplo do Script - Criar aplicação ASP.NET Core no Azure Container Registry | Documentos da Microsoft
description: Script da CLI do Azure de exemplo - criar uma aplicação ASP.NET Core num contentor do Docker no serviço de aplicações do Azure Container Registry
services: appservice
documentationcenter: appservice
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/13/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c05572d4962eca042a807be3eec94462d0830e1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835741"
---
# <a name="create-an-aspnet-core-app-in-a-docker-container-in-app-service-from-azure-container-registry"></a>Criar uma aplicação ASP.NET Core num contentor do Docker no serviço de aplicações do Azure Container Registry

Este script de exemplo cria um grupo de recursos, um plano de serviço de aplicações do Linux e uma aplicação. Em seguida, implementa uma aplicação ASP.NET Core com um Contentor do Docker a partir do Azure Container Registry.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Terá de CLI do Azure versão 2.0.52 ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh "Linux Azure Container Registry")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, a aplicação de serviço de aplicações e a todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Cria uma aplicação de serviço de aplicações. |
| [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) | Define o contentor do Docker para a aplicação de serviço de aplicações. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).
