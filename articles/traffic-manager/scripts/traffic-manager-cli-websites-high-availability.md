---
title: Rotear o tráfego para HA de aplicativos-CLI do Azure-Gerenciador de tráfego
description: CLI do Azure amostra de script – rotear o tráfego para alta disponibilidade de aplicativos
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: twooley
editor: tysonn
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: allensu
ms.openlocfilehash: 02807d3773b5d27d59ab6b03a22f7637bae95aca
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006400"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Rotear o tráfego para alta disponibilidade de aplicativos usando CLI do Azure

Esse script cria um grupo de recursos, dois planos do serviço de aplicativo, dois aplicativos Web, um perfil do Gerenciador de tráfego e dois pontos de extremidade do Gerenciador de tráfego. O Gerenciador de tráfego direciona o tráfego para o aplicativo em uma região como a região primária e para a região secundária quando o aplicativo na região primária está indisponível. Antes de executar o script, você deve alterar os valores de myWebApp, MyWebAppL1 e MyWebAppL2 para valores exclusivos no Azure. Depois de executar o script, você pode acessar o aplicativo na região primária com a URL mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Limpar a implementação 

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo do serviço de aplicativo e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma aplicação Web, um perfil do gestor de tráfego e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Cria um plano do Serviço de Aplicações. Isso é como um farm de servidores para seu aplicativo Web do Azure. |
| [AZ webapp Web Create](https://docs.microsoft.com/cli/azure/webapp#az-webapp-create) | Cria um aplicativo Web do Azure dentro do plano do serviço de aplicativo. |
| [AZ Network Traffic-Manager Profile Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Cria um perfil do Gestor de Tráfego do Azure. |
| [AZ Network Traffic-Manager Endpoint Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Adiciona um ponto final a um Perfil do Gestor de Tráfego do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de script da CLI do serviço de aplicativo podem ser encontrados na [documentação de rede do Azure](../cli-samples.md).
