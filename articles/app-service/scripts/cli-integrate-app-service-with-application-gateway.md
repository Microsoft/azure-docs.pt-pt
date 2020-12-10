---
title: Amostra de script do Azure CLI - Integrar o Serviço de Aplicações com Gateway de Aplicação Microsoft Docs
description: Amostra de script Azure CLI - Integrar o Serviço de Aplicações com Gateway de Aplicação
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3820e7bf00f99a846dd2be0edeaf4248e0dfd8ad
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006091"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>Integrar o Serviço de Aplicações com o Gateway de Aplicações utilizando o CLI

Este script de amostra cria uma aplicação web Azure App Service, uma Rede Virtual Azure e um Gateway de Aplicações. Em seguida, restringe o tráfego para a aplicação web apenas originar da sub-rede Application Gateway.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.74 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, app App Service, Cosmos DB e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az network vnet create`](/cli/azure/network/vnet#az-network-vnet-create) | Cria uma rede virtual. |
| [`az network public-ip create`](/cli/azure/network/public-ip#az-network-public-ip-create) | Cria um endereço IP público. |
| [`az network public-ip show`](/cli/azure/network/public-ip#az-network-public-ip-show) | Mostrar detalhes de um endereço IP público. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Cria uma aplicação web do Serviço de Aplicações. |
| [`az webapp show`](/cli/azure/webapp#az-webapp-show) | Mostrar detalhes de uma aplicação web do Serviço de Aplicações. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az-webapp-config-access-restriction-add) | Adiciona uma restrição de acesso à aplicação web do Serviço de Aplicações. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az-network-application-gateway-create) | Cria um Gateway de Aplicação. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az-network-application-gateway-http-settings-update) | Atualizações Aplicações Gateway HTTP definições. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).
