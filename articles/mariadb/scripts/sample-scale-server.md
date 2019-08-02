---
title: CLI do Azure o script em escala de um banco de dados do Azure para servidor MariaDB
description: Este script de exemplo da CLI dimensiona o banco de dados do Azure para o servidor MariaDB para um nível de desempenho diferente depois de consultar as métricas.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 08/01/2019
ms.openlocfilehash: 99f85abe0c8f08bedb3e9808d2b740d4b7842257
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720367"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Monitorar e dimensionar um banco de dados do Azure para o servidor MariaDB usando CLI do Azure
Este script de exemplo da CLI dimensiona a computação e o armazenamento para um único banco de dados do Azure para o servidor MariaDB depois de consultar as métricas.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se optar por executar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Verifique a versão ao executar `az --version`. Veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar a sua versão da CLI do Azure. 

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas realçadas para atualizar o nome de utilizador administrador e a palavra-passe com os seus. Substitua a ID da assinatura usada nos `az monitor` comandos com sua própria ID de assinatura.  
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh?highlight=15-16 "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Utilize o comando seguinte para remover o grupo de recursos e todos os recursos associados ao mesmo, depois de executar o script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os comandos descritos na tabela seguinte:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Cria um servidor MariaDB que hospeda os bancos de dados. |
| [atualização do servidor AZ MariaDB](/cli/azure/mariadb/server#az-mariadb-server-update) | Atualiza as propriedades do servidor MariaDB. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Liste o valor métrico dos recursos. |
| [az group delete](/cli/azure/group#az-group-delete) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o [banco de dados do Azure para computação e armazenamento MariaDB](../concepts-pricing-tiers.md)
- Experimente scripts adicionais: [Exemplos de CLI do Azure para o banco de dados do Azure para MariaDB](../sample-scripts-azure-cli.md)
- Saiba mais sobre o [CLI do Azure](/cli/azure)
