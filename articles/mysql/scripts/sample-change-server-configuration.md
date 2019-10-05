---
title: CLI do Azure script – alterar as configurações do servidor (MySQL)
description: Este script de exemplo da CLI lista todas as configurações de servidor disponíveis e atualiza o valor de innodb_lock_wait_timeout.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: faeebc2b4c963b46a0cbb805269078dee562a3f4
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970630"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Listar e atualizar configurações do servidor da Base de Dados do Azure para MySQL com a CLI do Azure
Este script de exemplo da CLI lista todos os parâmetros de configuração disponíveis, bem como os respetivos valores permitidos para o servidor da Base de Dados do Azure para MySQL e define *innodb_lock_wait_timeout* para um valor diferente do que está predefinido.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por executar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Verifique a versão ao executar `az --version`. Veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar a sua versão da CLI do Azure. 

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas realçadas para atualizar o nome de utilizador administrador e a palavra-passe com os seus.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Utilize o comando seguinte para remover o grupo de recursos e todos os recursos associados ao mesmo, depois de executar o script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os comandos descritos na tabela seguinte:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Cria um servidor MySQL que aloja as bases de dados. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) | Lista as configurações do servidor da Base de Dados do Azure para MySQL. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) | Atualiza a configuração do servidor da Base de Dados do Azure para MySQL. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) | Mostra a configuração do servidor da Base de Dados do Azure para MySQL. |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre o CLI do Azure: [CLI do Azure documentação](/cli/azure).
- Experimente scripts adicionais: [Exemplos de CLI do Azure para o banco de dados do Azure para MySQL](../sample-scripts-azure-cli.md)
- Para obter mais informações sobre os parâmetros de servidor, veja [Como Configurar Parâmetros de Servidor na Base de Dados do Azure para MySQL](../howto-server-parameters.md).
