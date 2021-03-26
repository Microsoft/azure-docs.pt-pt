---
title: Script da CLI do Azure - Criar uma Base de Dados do Azure para PostgreSQL
description: Script de Exemplo da CLI do Azure - Cria um servidor da Base de Dados do Azure para PostgreSQL e configura uma regra de firewall ao nível do servidor.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/28/2018
ms.openlocfilehash: e20e9481c86db639a1e68b3e3d7d5c2146605afb
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608481"
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Criar um servidor da Base de Dados do Azure para PostgreSQL e configurar uma regra de firewall com a CLI do Azure
Este script de exemplo da CLI cria um servidor da Base de Dados do Azure para PostgreSQL e configura uma regra de firewall ao nível do servidor. Assim que o script tiver sido executado com êxito, o servidor PostgreSQL pode ser acedido a partir de todos os serviços do Azure e do endereço IP configurado.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

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
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgres server create](/cli/azure/postgres/server) | Cria um servidor PostgreSQL que aloja as bases de dados. |
| [az postgres server firewall create](/cli/azure/postgres/server/firewall-rule) | Cria uma regra de firewall para permitir o acesso ao servidor e às bases de dados incluídas, a partir do intervalo de endereços IP introduzido. |
| [az group delete](/cli/azure/group) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre o Azure CLI: [Documentação do Azure CLI](/cli/azure)
- Experimente scripts adicionais: [Amostras da CLI do Azure para a Base de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
