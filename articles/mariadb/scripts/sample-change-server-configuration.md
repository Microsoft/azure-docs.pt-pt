---
title: Script CLI - Alterar parâmetros do servidor - Azure Database for MariaDB
description: Esta amostra de script CLI lista todas as configurações e atualizações disponíveis do servidor de uma Base de Dados Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: ce27dc3008803faf548c10f4fc2ab3b579af2ba0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90525036"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Configurações de lista e atualização de uma Base de Dados Azure para servidor MariaDB usando O Azure CLI
Esta amostra de script CLI lista todos os parâmetros de configuração disponíveis, bem como os seus valores admissíveis para Azure Database para servidor MariaDB, e define o *innodb_lock_wait_timeout* para um valor que não o padrão.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por executar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Verifique a versão ao executar `az --version`. Veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar a sua versão da CLI do Azure. 

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
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [servidor mariadb az criar](/cli/azure/mariadb/server#az-mariadb-server-create) | Cria um servidor MariaDB que acolhe as bases de dados. |
| [lista de configuração do servidor mariadb az](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Listar as configurações de uma Base de Dados Azure para o servidor MariaDB. |
| [conjunto de configuração do servidor mariadb az](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Atualize a configuração de uma Base de Dados Azure para o servidor MariaDB. |
| [az mariadb servidor configuração show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Mostrar a configuração de uma Base de Dados Azure para o servidor MariaDB. |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [amostras de Azure CLI para Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Para obter mais informações sobre os parâmetros do servidor, consulte [Como Configurar os Parâmetros do Servidor na Base de Dados Azure para MariaDB](../howto-server-parameters.md).
