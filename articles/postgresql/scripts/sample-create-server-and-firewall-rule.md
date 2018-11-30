---
title: Script da CLI do Azure - Criar uma Base de Dados do Azure para PostgreSQL
description: Script de Exemplo da CLI do Azure - Cria um servidor da Base de Dados do Azure para PostgreSQL e configura uma regra de firewall ao nível do servidor.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: sample
ms.date: 02/28/2018
ms.openlocfilehash: 4d14ca7ee70dc7d24f0fa95a2139d88231d36a7d
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52580564"
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Criar um servidor da Base de Dados do Azure para PostgreSQL e configurar uma regra de firewall com a CLI do Azure
Este script de exemplo da CLI cria um servidor da Base de Dados do Azure para PostgreSQL e configura uma regra de firewall ao nível do servidor. Assim que o script tiver sido executado com êxito, o servidor PostgreSQL pode ser acedido a partir de todos os serviços do Azure e do endereço IP configurado.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por executar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Verifique a versão ao executar `az --version`. Veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar a sua versão da CLI do Azure.

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas realçadas para atualizar o nome de utilizador administrador e a palavra-passe com os seus.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for PostgreSQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Utilize o comando seguinte para remover o grupo de recursos e todos os recursos associados ao mesmo, depois de executar o script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os comandos descritos na tabela seguinte:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Cria um servidor PostgreSQL que aloja as bases de dados. |
| [az postgres server firewall create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) | Cria uma regra de firewall para permitir o acesso ao servidor e às bases de dados incluídas, a partir do intervalo de endereços IP introduzido. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos Seguintes
- Ler mais informações sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure)
- Experimente scripts adicionais: [Amostras da CLI do Azure para a Base de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
