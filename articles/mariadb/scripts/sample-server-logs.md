---
title: Script da CLI – baixar logs de consulta lentos-banco de dados do Azure para MariaDB
description: Este script de CLI do Azure de exemplo mostra como habilitar e baixar os logs de consultas lentas de um banco de dados do Azure para o servidor MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: a6d4ed70418f39b24a68362d5006f3f374693cb6
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74777366"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Habilitar e baixar logs de consulta lenta do servidor de um banco de dados do Azure para servidor MariaDB usando o CLI do Azure
Este script de exemplo da CLI habilita e baixa os logs de consulta lentos de um único banco de dados do Azure para o servidor MariaDB.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por executar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Verifique a versão ao executar `az --version`. Veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar a sua versão da CLI do Azure. 

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas realçadas para atualizar o nome de utilizador administrador e a palavra-passe com os seus. Substitua o &lt;log_file_name&gt; nos comandos do `az monitor` pelo nome do arquivo de log do seu próprio servidor.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Utilize o comando seguinte para remover o grupo de recursos e todos os recursos associados ao mesmo, depois de executar o script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os comandos descritos na tabela seguinte:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [AZ MariaDB Server CREATE](/cli/azure/mariadb/server#az-mariadb-server-create) | Cria um servidor MariaDB que hospeda os bancos de dados. |
| [lista de configuração do servidor AZ MariaDB](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Liste os valores de configuração para um servidor. |
| [conjunto de configuração de servidor AZ MariaDB](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Atualize a configuração de um servidor. |
| [AZ MariaDB Server – lista de logs](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) | Liste os ficheiros de registo para um servidor. |
| [AZ MariaDB Server – Download de logs](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) | Descarregue ficheiros de registo. |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [exemplos de CLI do Azure para o banco de dados do Azure para MariaDB](../sample-scripts-azure-cli.md)
