---
title: Script CLI - Alterar parâmetros do servidor - Base de Dados Azure para MariaDB
description: Este script de exemplo da CLI lista todas as configurações de servidor disponíveis e atualiza o valor de innodb_lock_wait_timeout.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 515eb7523c5a08d52ad5eb4f7bd261f3f4e03fc4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74771825"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Lista e configurações de atualização de uma Base de Dados Azure para servidor MariaDB utilizando o Azure CLI
Esta amostra de script CLI lista todos os parâmetros de configuração disponíveis, bem como os seus valores admissíveis para a Base de Dados Azure para o servidor MariaDB, e define o *innodb_lock_wait_timeout* para um valor que não seja o padrão.

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
| [az mariadb servidor criar](/cli/azure/mariadb/server#az-mariadb-server-create) | Cria um servidor MariaDB que acolhe as bases de dados. |
| [lista de configuração do servidor az mariadb](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Enumera rindo as configurações de uma Base de Dados Azure para o servidor MariaDB. |
| [conjunto de configuração de servidor az mariadb](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Atualize a configuração de uma Base de Dados Azure para o servidor MariaDB. |
| [az mariadb programa de configuração servidor](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Mostre a configuração de uma Base de Dados Azure para o servidor MariaDB. |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [Amostras azure CLI para Base de Dados Azure para MariaDB](../sample-scripts-azure-cli.md)
- Para obter mais informações sobre os parâmetros do servidor, consulte Como Configurar parâmetros de servidor na Base de [Dados Azure para MariaDB](../howto-server-parameters.md).
