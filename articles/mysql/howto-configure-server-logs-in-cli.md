---
title: Consulta lenta de acesso registos na base de dados do Azure para MySQL com a CLI do Azure
description: Este artigo descreve como aceder os registos de consulta lenta na base de dados do Azure para MySQL com a CLI do Azure.
author: andrela
ms.author: ajlam
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/219
ms.openlocfilehash: 740dbce579fba6347b1a7f2cfc6bcae40d3503ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052704"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Configurar e aceder aos registos de consulta lenta com CLI do Azure
Pode baixar a base de dados do Azure para os registos de consulta lenta MySQL com a CLI do Azure, o utilitário de linha de comandos do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- [Base de dados do Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- O [CLI do Azure](/cli/azure/install-azure-cli) ou o Azure Cloud Shell no browser

## <a name="configure-logging"></a>Configurar o registo
Pode configurar o servidor para acessar o log de consulta lenta MySQL, efetuando os seguintes passos:
1. Ativar o registo de consulta lenta, definindo a **lenta\_consulta\_log** parâmetro on.
2. Ajustar a outros parâmetros, tal como **longa\_consulta\_tempo** e **log\_lenta\_administrador\_instruções**.

Para saber como definir o valor desses parâmetros através da CLI do Azure, veja [como configurar parâmetros de servidor](howto-configure-server-parameters-using-cli.md).

Por exemplo, o seguinte comando da CLI ativa o registo de consulta lenta, define o tempo de consulta longo para 10 segundos e, em seguida, desativa o registo da instrução administrador lenta. Por fim, ele apresenta uma lista de opções de configuração para sua revisão.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista de registos da base de dados do Azure para o servidor MySQL
Para listar os ficheiros de registo de consulta lenta disponíveis para o seu servidor, execute o [lista do az mysql server-logs](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) comando.

Pode listar os ficheiros de registo para o servidor **mydemoserver.mysql.database.azure.com** sob o grupo de recursos **myresourcegroup**. Em seguida, direcionar a lista de ficheiros de registo para um arquivo de texto chamado **log\_arquivos\_txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Transferir registos do servidor
Com o [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) de comando, pode transferir ficheiros de registo individuais para o seu servidor. 

Utilize o exemplo seguinte para transferir o ficheiro de registo específico para o servidor **mydemoserver.mysql.database.azure.com** sob o grupo de recursos **myresourcegroup** ao seu ambiente local.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [registos de consulta lenta na base de dados do Azure para MySQL](concepts-server-logs.md).
