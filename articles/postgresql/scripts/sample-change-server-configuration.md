---
title: Script Azure CLI - Alterar configurações de servidor (PostgreSQL)
description: Este script da CLI de exemplo lista todas as opções de configuração de servidor disponíveis e atualiza o valor de uma das opções.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 1c3433c1f96dc32d91f1c07882f231202383e1c3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607359"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Lista e atualiza configurações do servidor da Base de Dados do Azure para PostgreSQL com a CLI do Azure
Este script da CLI de exemplo lista todos os parâmetros de configuração disponíveis, bem como os respetivos valores permitidos para o servidor da Base de Dados do Azure para PostgreSQL e define *log_retention_days* para um valor diferente do que está predefinido.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas realçadas para atualizar o nome de utilizador administrador e a palavra-passe com os seus.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpar a implementação
Utilize o comando seguinte para remover o grupo de recursos e todos os recursos associados ao mesmo, depois de executar o script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação do script
Este script utiliza os comandos descritos na tabela seguinte:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgres server create](/cli/azure/postgres/server) | Cria um servidor PostgreSQL que aloja as bases de dados. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Lista as configurações de um servidor da Base de Dados do Azure para PostgreSQL. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Atualiza a configuração de um servidor da Base de Dados do Azure para PostgreSQL. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration) | Mostra a configuração de um servidor da Base de Dados do Azure para PostgreSQL. |
| [az group delete](/cli/azure/group) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes
- Leia mais informações sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [Amostras da CLI do Azure para a Base de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- Para obter mais informações sobre os parâmetros de servidor, veja [Como Configurar parâmetros de servidor no portal do Azure](../howto-configure-server-parameters-using-portal.md).
