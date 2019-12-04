---
title: Script da CLI – alterar parâmetros do servidor-banco de dados do Azure para MariaDB
description: Este script de exemplo da CLI lista todas as configurações de servidor disponíveis e atualiza o valor de innodb_lock_wait_timeout.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 515eb7523c5a08d52ad5eb4f7bd261f3f4e03fc4
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74771825"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Listar e atualizar as configurações de um banco de dados do Azure para servidor MariaDB usando CLI do Azure
Este script de exemplo da CLI lista todos os parâmetros de configuração disponíveis, bem como seus valores permitidos para o banco de dados do Azure para MariaDB Server, e define o *innodb_lock_wait_timeout* com um valor diferente do padrão.

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
| [AZ MariaDB Server CREATE](/cli/azure/mariadb/server#az-mariadb-server-create) | Cria um servidor MariaDB que hospeda os bancos de dados. |
| [lista de configuração do servidor AZ MariaDB](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Listar as configurações de um banco de dados do Azure para o servidor MariaDB. |
| [conjunto de configuração de servidor AZ MariaDB](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Atualize a configuração de um banco de dados do Azure para o servidor MariaDB. |
| [AZ MariaDB Server Configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Mostra a configuração de um banco de dados do Azure para o servidor MariaDB. |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [exemplos de CLI do Azure para o banco de dados do Azure para MariaDB](../sample-scripts-azure-cli.md)
- Para obter mais informações sobre parâmetros de servidor, consulte [como configurar parâmetros de servidor no banco de dados do Azure para MariaDB](../howto-server-parameters.md).
