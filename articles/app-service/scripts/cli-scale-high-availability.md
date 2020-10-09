---
title: 'CLI: App de escala com Gestor de Tráfego'
description: Saiba como utilizar o Azure CLI para automatizar a implementação e gestão da sua aplicação De Serviço de Aplicações. Esta amostra mostra como escalar um mundo inteiro com o Traffic Manager.
author: msangapu-msft
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: c36d42a3d3daa9f7a2dccabe60b6319602fef017
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88960399"
---
# <a name="scale-an-app-service-app-worldwide-with-a-high-availability-architecture-using-azure-cli"></a>Dimensione uma aplicação de Serviço de Aplicações em todo o mundo com uma arquitetura de alta disponibilidade usando O Azure CLI

Este script de amostra cria um grupo de recursos, dois planos de Serviço de Aplicações, duas aplicações, um perfil de gestor de tráfego e dois pontos finais de gestor de tráfego. Uma vez que o exercício esteja concluído, você tem uma arquitetura altamente disponível, que fornece disponibilidade global da sua app com base na latência de rede mais baixa.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, precisa da versão 2.0 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, app App Service, perfil de gestor de tráfego e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Cria uma aplicação de Serviço de Aplicações. |
| [`az network traffic-manager profile create`](/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) | Cria um perfil do Gestor de Tráfego do Azure. |
| [`az network traffic-manager endpoint create`](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) | Adiciona um ponto final a um Perfil do Gestor de Tráfego do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).