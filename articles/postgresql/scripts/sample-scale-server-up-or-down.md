---
title: CLI do Azure o script-dimensionar e monitorar o banco de dados do Azure para PostgreSQL
description: Script de Exemplo da CLI do Azure - Dimensionar um servidor da Base de Dados do Azure para PostgreSQL para um nível de desempenho diferente depois de consultar as métricas.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 24aaf461576e6e043979660f9de968358763e003
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882993"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitorizar e dimensionar um único servidor PostgreSQL com a CLI do Azure
Este script de exemplo da CLI dimensiona a computação e o armazenamento para um único banco de dados do Azure para o servidor PostgreSQL depois de consultar as métricas. A computação pode escalar ou reduzir verticalmente. O armazenamento só pode escalar verticalmente. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por executar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Verifique a versão ao executar `az --version`. Veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar a sua versão da CLI do Azure.

## <a name="sample-script"></a>Script de exemplo
Atualize o script com sua ID de assinatura.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Utilize o comando seguinte para remover o grupo de recursos e todos os recursos associados ao mesmo, depois de executar o script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os comandos descritos na tabela seguinte:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Cria um servidor PostgreSQL que aloja as bases de dados. |
| [atualização do servidor AZ postgres](/cli/azure/postgres/server#az-postgres-server-update) | Atualiza as propriedades do servidor PostgreSQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Liste o valor métrico dos recursos. |
| [az group delete](/cli/azure/group) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [a computação e o armazenamento do banco de dados do Azure para PostgreSQL](../concepts-pricing-tiers.md)
- Experimente scripts adicionais: [Amostras de CLI do Azure para a Base de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- Saiba mais sobre o [CLI do Azure](/cli/azure)
