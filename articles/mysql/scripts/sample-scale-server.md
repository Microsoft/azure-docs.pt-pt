---
title: Script CLI - Servidor de escala - Base de Dados Azure para MySQL
description: Este script de exemplo da CLI dimensiona o servidor da Base de Dados do Azure para MySQL para um nível de desempenho diferente depois de consultar as métricas.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 31cf6d1f3bfcc44887ec939cb2f27df77f12cea3
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539265"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Monitorizar e dimensionar um servidor da Base de Dados do Azure para MySQL com a CLI do Azure
Esta amostra de script CLI escala o cálculo e armazenamento para uma única base de dados Azure para o servidor MySQL após consulta das métricas. O cálculo pode escalar para cima ou para baixo. O armazenamento só pode aumentar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="sample-script"></a>Script de exemplo
Atualize o script com o seu ID de subscrição.
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
| [az mysql servidor atualização](/cli/azure/mysql/server#az-mysql-server-update) | Atualiza as propriedades do servidor MySQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Liste o valor métrico dos recursos. |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a Azure Database para computação e armazenamento mySQL](../concepts-pricing-tiers.md)
- Experimente scripts adicionais: [Exemplos da CLI do Azure para a Base de Dados do Azure para MySQL](../sample-scripts-azure-cli.md)
- Saiba mais sobre o [Azure CLI](/cli/azure)
