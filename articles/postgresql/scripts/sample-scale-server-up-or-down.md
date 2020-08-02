---
title: Script Azure CLI - Escalar e monitorizar base de dados de Azure para PostgreSQL
description: Script de Exemplo da CLI do Azure - Dimensionar um servidor da Base de Dados do Azure para PostgreSQL para um nível de desempenho diferente depois de consultar as métricas.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 23dae705f10ecca9119932640383c93ab80d1d21
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496496"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitorizar e dimensionar um único servidor PostgreSQL com a CLI do Azure
Esta amostra de script CLI escala o cálculo e armazenamento para uma única base de dados Azure para servidor PostgreSQL após consulta das métricas. O cálculo pode escalar para cima ou para baixo. O armazenamento só pode aumentar. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por executar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Verifique a versão ao executar `az --version`. Veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar a sua versão da CLI do Azure.

## <a name="sample-script"></a>Script de exemplo
Atualize o script com o seu ID de subscrição.
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
| [az postgres servidor atualização](/cli/azure/postgres/server#az-postgres-server-update) | Atualiza as propriedades do servidor PostgreSQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Liste o valor métrico dos recursos. |
| [az group delete](/cli/azure/group) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a Azure Database para computação e armazenamento pósgresQL](../concepts-pricing-tiers.md)
- Experimente scripts adicionais: [Amostras da CLI do Azure para a Base de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- Saiba mais sobre o [Azure CLI](/cli/azure)
