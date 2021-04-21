---
title: Script CLI - Alterar parâmetros do servidor - Azure Database for MariaDB
description: Esta amostra de script CLI lista todas as configurações e atualizações disponíveis do servidor de uma Base de Dados Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 3504f1221c501b997b04d9c81c721aba2903fba6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777064"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Configurações de lista e atualização de uma Base de Dados Azure para servidor MariaDB usando O Azure CLI
Esta amostra de script CLI lista todos os parâmetros de configuração disponíveis, bem como os seus valores admissíveis para Azure Database para servidor MariaDB, e define o *innodb_lock_wait_timeout* para um valor que não o padrão.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas realçadas para atualizar o nome de utilizador administrador e a palavra-passe com os seus.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Utilize o comando seguinte para remover o grupo de recursos e todos os recursos associados ao mesmo, depois de executar o script.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os comandos descritos na tabela seguinte:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [servidor mariadb az criar](/cli/azure/mariadb/server#az_mariadb_server_create) | Cria um servidor MariaDB que acolhe as bases de dados. |
| [lista de configuração do servidor mariadb az](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_list) | Listar as configurações de uma Base de Dados Azure para o servidor MariaDB. |
| [conjunto de configuração do servidor mariadb az](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) | Atualize a configuração de uma Base de Dados Azure para o servidor MariaDB. |
| [az mariadb servidor configuração show](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_show) | Mostrar a configuração de uma Base de Dados Azure para o servidor MariaDB. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [amostras de Azure CLI para Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Para obter mais informações sobre os parâmetros do servidor, consulte [Como Configurar os Parâmetros do Servidor na Base de Dados Azure para MariaDB](../howto-server-parameters.md).
