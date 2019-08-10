---
title: Script da CLI do Azure - Dimensionar um servidor da Base de Dados do Azure para MySQL
description: Este script de exemplo da CLI dimensiona o servidor da Base de Dados do Azure para MySQL para um nível de desempenho diferente depois de consultar as métricas.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 08/07/2019
ms.openlocfilehash: f54b3f6fa8bb37f57479d6a9e7bc05340e411a48
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882928"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Monitorizar e dimensionar um servidor da Base de Dados do Azure para MySQL com a CLI do Azure
Este script de exemplo da CLI dimensiona a computação e o armazenamento para um único banco de dados do Azure para o servidor MySQL depois de consultar as métricas. A computação pode escalar ou reduzir verticalmente. O armazenamento só pode escalar verticalmente.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por executar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Verifique a versão ao executar `az --version`. Veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar a sua versão da CLI do Azure. 

## <a name="sample-script"></a>Script de exemplo
Atualize o script com sua ID de assinatura.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Utilize o comando seguinte para remover o grupo de recursos e todos os recursos associados ao mesmo, depois de executar o script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os comandos descritos na tabela seguinte:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Cria um servidor MySQL que aloja as bases de dados. |
| [atualização do servidor AZ MySQL](/cli/azure/mysql/server#az-mysql-server-update) | Atualiza as propriedades do servidor MySQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Liste o valor métrico dos recursos. |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [computação e armazenamento do banco de dados do Azure para MySQL](../concepts-pricing-tiers.md)
- Experimente scripts adicionais: [Exemplos de CLI do Azure para o banco de dados do Azure para MySQL](../sample-scripts-azure-cli.md)
- Saiba mais sobre o [CLI do Azure](/cli/azure)