---
title: Exemplo do Script da CLI do Azure - Criar uma aplicação Web e implementar ficheiros com FTP | Microsoft Docs
description: Exemplo do Script da CLI do Azure - Criar uma aplicação Web e implementar ficheiros com FTP
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: sample
ms.topic: sample
ms.date: 12/12/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 416b6573a7cec85442ca725583f8c53fa5a6c697
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53585133"
---
# <a name="create-a-web-app-and-deploy-files-with-ftp-using-azure-cli"></a>Criar uma aplicação web e implementar ficheiros com FTP com a CLI do Azure

Este script de exemplo cria uma aplicação Web no Serviço de Aplicações com os respetivos recursos relacionados e, em seguida, implementa uma página HTML com o FTP. Para o carregamento no FTP, o script utiliza o [cURL](https://en.wikipedia.org/wiki/CURL) como exemplo. Pode utilizar qualquer ferramenta FTP para carregar os ficheiros.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, precisa da versão 2.0 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-ftp/deploy-ftp.sh "Create a web app and deploy files with FTP")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script 

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Cria uma aplicação Web do Azure. |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment?view=azure-cli-latest#az-webapp-deployment-list-publishing-profiles) | Obtenha os detalhes para os perfis de implementação da aplicação Web disponível. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).
