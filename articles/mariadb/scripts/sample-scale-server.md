---
title: Script CLI - Servidor de escala - Base de Dados Azure para MariaDB
description: Esta amostra de script CLI escala a Base de Dados Azure para o servidor MariaDB para um nível de desempenho diferente após consulta das métricas.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: f058431c29a33c5824aa637a54394045e6269a88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502227"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Monitorize e escale uma base de dados Azure para servidor MariaDB usando O Azure CLI
Esta amostra de script CLI escala o cálculo e armazenamento para uma única base de dados Azure para o servidor MariaDB após consulta das métricas. O cálculo pode escalar para cima ou para baixo. O armazenamento só pode aumentar.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por executar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Verifique a versão ao executar `az --version`. Veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar a sua versão da CLI do Azure. 

## <a name="sample-script"></a>Script de exemplo
Atualize o script com o seu ID de subscrição.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Utilize o comando seguinte para remover o grupo de recursos e todos os recursos associados ao mesmo, depois de executar o script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os comandos descritos na tabela seguinte:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [servidor mariadb az criar](/cli/azure/mariadb/server#az-mariadb-server-create) | Cria um servidor MariaDB que acolhe as bases de dados. |
| [atualização do servidor az mariadb](/cli/azure/mariadb/server#az-mariadb-server-update) | Atualiza as propriedades do servidor MariaDB. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Liste o valor métrico dos recursos. |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a Azure Database para computação e armazenamento MariaDB](../concepts-pricing-tiers.md)
- Experimente scripts adicionais: [amostras de Azure CLI para Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Saiba mais sobre o [Azure CLI](/cli/azure)
