---
title: Acesso registos do servidor na base de dados do Azure para MariaDB com CLI do Azure
description: Este artigo descreve como aceder os registos do servidor na base de dados do Azure para MariaDB com o utilitário de linha de comandos da CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/10/2018
ms.openlocfilehash: 403f111882b1bd151b26af56fd614355e7ba88e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61040763"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurar e aceder aos registos de servidor com a CLI do Azure
Pode baixar a base de dados do Azure para os registos de servidor da MariaDB com a CLI do Azure, o utilitário de linha de comandos do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- [Base de dados do Azure para MariaDB server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- O [CLI do Azure](/cli/azure/install-azure-cli) ou o Azure Cloud Shell no browser

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Configure o registo da base de dados do Azure para MariaDB
Pode configurar o servidor de acesso ao registo de consulta lenta de MariaDB, efetuando os seguintes passos:
1. Ativar o registo, definindo a **lenta\_consulta\_log** parâmetro on.
2. Ajustar a outros parâmetros, tal como **longa\_consulta\_tempo** e **log\_lenta\_administrador\_instruções**.

Para saber como definir o valor desses parâmetros através da CLI do Azure, veja [como configurar parâmetros de servidor](howto-configure-server-parameters-cli.md).

Por exemplo, o seguinte comando da CLI ativa o registo de consulta lenta, define o tempo de consulta longo para 10 segundos e, em seguida, desativa o registo da instrução administrador lenta. Por fim, ele apresenta uma lista de opções de configuração para sua revisão.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Registos de lista para a base de dados do Azure para MariaDB server
Para listar os ficheiros de registo disponíveis para o seu servidor, execute o [lista de registos do servidor de mariadb az](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) comando.

Pode listar os ficheiros de registo para o servidor **mydemoserver.mariadb.database.azure.com** sob o grupo de recursos **myresourcegroup**. Em seguida, direcionar a lista de ficheiros de registo para um arquivo de texto chamado **log\_arquivos\_txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Transferir registos do servidor
Com o [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) de comando, pode transferir ficheiros de registo individuais para o seu servidor.

Utilize o exemplo seguinte para transferir o ficheiro de registo específico para o servidor **mydemoserver.mariadb.database.azure.com** sob o grupo de recursos **myresourcegroup** ao seu ambiente local.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [registos do servidor na base de dados do Azure para MariaDB](concepts-server-logs.md).
