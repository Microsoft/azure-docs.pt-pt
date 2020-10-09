---
title: 'CLI: Criar uma cópia de segurança programada'
description: Saiba como utilizar o Azure CLI para automatizar a implementação e gestão da sua aplicação De Serviço de Aplicações. Esta amostra mostra como criar uma cópia de segurança programada para uma aplicação.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18
ms.openlocfilehash: cc87438d785b7c45506c638ee80e10ae4021674f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962762"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>Crie uma cópia de segurança programada para uma aplicação do Serviço de Aplicações utilizando o CLI

Este script de amostra cria uma aplicação no Serviço de Aplicações com os seus recursos relacionados e, em seguida, cria uma cópia de segurança programada para o mesmo. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, precisa da versão 2.0 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-scheduled/backup-scheduled.sh?highlight=3-7 "Create a scheduled backup for an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) | Cria uma conta de armazenamento. |
| [`az storage container create`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) | Cria um contentor de armazenamento do Azure. |
| [`az storage container generate-sas`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-generate-sas) | Gera um token SAS para um contentor de armazenamento do Azure.  |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Cria uma aplicação de Serviço de Aplicações. |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-update) | Configura um novo horário de backup para uma aplicação do Serviço de Aplicações. |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-show) | Mostra a agenda de backup para uma aplicação do Serviço de Aplicações. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | Obtém uma lista de backups para uma aplicação do Serviço de Aplicações. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).