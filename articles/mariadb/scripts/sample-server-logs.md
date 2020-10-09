---
title: Script CLI - Descarregue registos de consulta lenta - Base de Dados Azure para MariaDB
description: Esta amostra O script Azure CLI mostra como ativar e descarregar os registos de consulta lenta de uma Base de Dados Azure para servidor MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 5212f4eb17ef7b4ed1b89604fc12a1e13bae78f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502193"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Ative e descarregue registos de consulta lenta do servidor de uma Base de Dados Azure para servidor MariaDB utilizando O Azure CLI
Esta amostra de script CLI permite e descarrega os registos de consulta lenta de uma única Base de Dados Azure para o servidor MariaDB.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por executar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Verifique a versão ao executar `az --version`. Veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar a sua versão da CLI do Azure. 

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas realçadas para atualizar o nome de utilizador administrador e a palavra-passe com os seus. Substitua a &lt; log_file_name &gt; nos `az monitor` comandos pelo nome do ficheiro de registo do seu próprio servidor.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Utilize o comando seguinte para remover o grupo de recursos e todos os recursos associados ao mesmo, depois de executar o script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os comandos descritos na tabela seguinte:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [servidor mariadb az criar](/cli/azure/mariadb/server#az-mariadb-server-create) | Cria um servidor MariaDB que acolhe as bases de dados. |
| [lista de configuração do servidor mariadb az](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Liste os valores de configuração para um servidor. |
| [conjunto de configuração do servidor mariadb az](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Atualize a configuração de um servidor. |
| [lista de registos de servidores az mariadb](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) | Liste os ficheiros de registo para um servidor. |
| [download de servidores mariadb az](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) | Descarregue ficheiros de registo. |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [amostras de Azure CLI para Azure Database for MariaDB](../sample-scripts-azure-cli.md)
