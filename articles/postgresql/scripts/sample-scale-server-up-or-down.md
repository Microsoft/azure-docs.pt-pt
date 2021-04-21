---
title: Script Azure CLI - Escalar e monitorizar base de dados de Azure para PostgreSQL
description: Script de Exemplo da CLI do Azure - Dimensionar um servidor da Base de Dados do Azure para PostgreSQL para um nível de desempenho diferente depois de consultar as métricas.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 56fd88ab658e59cccb14a35559d1793bc3ad1aa0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778450"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitorizar e dimensionar um único servidor PostgreSQL com a CLI do Azure
Esta amostra de script CLI escala o cálculo e armazenamento para uma única base de dados Azure para servidor PostgreSQL após consulta das métricas. O cálculo pode escalar para cima ou para baixo. O armazenamento só pode aumentar. 

> [!IMPORTANT] 
> O armazenamento só pode ser aumentado, não para baixo.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

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
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Cria um servidor PostgreSQL que aloja as bases de dados. |
| [az postgres servidor atualização](/cli/azure/postgres/server#az_postgres_server_update) | Atualiza as propriedades do servidor PostgreSQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Liste o valor métrico dos recursos. |
| [az group delete](/cli/azure/group) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a Azure Database para computação e armazenamento pósgresQL](../concepts-pricing-tiers.md)
- Experimente scripts adicionais: [Amostras da CLI do Azure para a Base de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- Saiba mais sobre o [Azure CLI](/cli/azure)
