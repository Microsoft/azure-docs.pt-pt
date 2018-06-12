---
title: Acesso registos do servidor na base de dados do Azure para MySQL, utilizando a CLI do Azure
description: Este artigo descreve como aceder os registos do servidor na base de dados do Azure para MySQL, utilizando o utilitário de linha de comandos da CLI do Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 85c7840c0e919e77e807e6114c4d0c65601ff334
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265831"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurar e iniciar sessão no servidor de acesso utilizando a CLI do Azure
Pode transferir o MySQL registos do servidor da base de dados do Azure utilizando a CLI do Azure, o utilitário da linha de comandos do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, tem de:
- [Base de dados do Azure para o servidor de MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- O [Azure CLI 2.0](/cli/azure/install-azure-cli) ou a Shell de nuvem do Azure no browser

## <a name="configure-logging-for-azure-database-for-mysql"></a>Configurar o registo da base de dados do Azure para MySQL
Pode configurar o servidor para aceder ao registo de consulta lenta de MySQL, efetuando os seguintes passos:
1. Ativar o registo, definindo o **lenta\_consulta\_registo** parâmetro como ON.
2. Ajustar a outros parâmetros, tais como **longo\_consulta\_tempo** e **registo\_lenta\_admin\_instruções**.

Para saber como definir o valor destes parâmetros através da CLI do Azure, consulte [como configurar parâmetros de servidor](howto-configure-server-parameters-using-cli.md). 

Por exemplo, o seguinte comando da CLI ativa o registo de consultas lenta, define a hora de consulta longo para 10 segundos e, em seguida, desativa o registo da instrução admin lentos. Por fim, lista as opções de configuração para a revisão.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista de registos da base de dados do Azure para o servidor de MySQL
Para listar os ficheiros de registo disponível para o servidor, execute o [lista de registos do servidor de mysql az](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) comando.

Pode listar os ficheiros de registo para o servidor **mydemoserver.mysql.database.azure.com** sob o grupo de recursos **myresourcegroup**. Em seguida, direcionar a lista de ficheiros de registo para um ficheiro de texto chamado **registo\_ficheiros\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Transferir os registos do servidor
Com o [az mysql-registos do servidor transferir](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) comando, pode transferir os ficheiros de registo individuais para o servidor. 

Utilize o seguinte exemplo para transferir o ficheiro de registo específicos para o servidor **mydemoserver.mysql.database.azure.com** sob o grupo de recursos **myresourcegroup** ao seu ambiente local.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [registos do servidor na base de dados do Azure para MySQL](concepts-server-logs.md).
