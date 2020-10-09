---
title: 'CLI: Conecte uma aplicação à Base de Dados SQL'
description: Saiba como utilizar o Azure CLI para automatizar a implementação e gestão da sua aplicação De Serviço de Aplicações. Esta amostra mostra como ligar uma aplicação à BASE de Dados SQL.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 5bdb09acf9adeaf0165512b144de0348ad4df743
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88959696"
---
# <a name="connect-an-app-service-app-to-sql-database-using-cli"></a>Conecte uma aplicação de Serviço de Aplicações à Base de Dados SQL utilizando o CLI

Este script de amostra cria uma base de dados na Base de Dados Azure SQL e numa aplicação de Serviço de Aplicações. Em seguida, liga a base de dados à aplicação utilizando as definições de aplicações.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, precisa da versão 2.0 ou posterior da CLI do Azure. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "SQL Database")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, app App Service, SQL Database e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Cria um plano do Serviço de Aplicações. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Cria uma aplicação de Serviço de Aplicações. |
| [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) | Cria um servidor.  |
| [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) | Cria uma nova base de dados. |
| [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string) | Gera uma cadeia de ligação para uma base de dados. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) | Cria ou atualiza uma configuração de aplicação para uma aplicação do Serviço de Aplicações. As definições da aplicação são expostas como variáveis de ambiente para a sua aplicação. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos do script da CLI do Serviço de Aplicações adicionais na [documentação do Serviço de Aplicações do Azure](../samples-cli.md).