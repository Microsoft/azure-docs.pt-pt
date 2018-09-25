---
title: Acesso registos do servidor na base de dados do Azure para MySQL com a CLI do Azure
description: Este artigo descreve como aceder os registos do servidor na base de dados do Azure para MySQL com o utilitário de linha de comandos da CLI do Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e3940f07ab97eb2ad03c347c39bf2412a8497f5e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978459"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurar e aceder aos registos de servidor com a CLI do Azure
Pode baixar a base de dados do Azure para os registos do servidor MySQL com a CLI do Azure, o utilitário de linha de comandos do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- [Base de dados do Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- O [CLI do Azure](/cli/azure/install-azure-cli) ou o Azure Cloud Shell no browser

## <a name="configure-logging-for-azure-database-for-mysql"></a>Configurar o registo da base de dados do Azure para MySQL
Pode configurar o servidor para acessar o log de consulta lenta MySQL, efetuando os seguintes passos:
1. Ativar o registo, definindo a **lenta\_consulta\_log** parâmetro on.
2. Ajustar a outros parâmetros, tal como **longa\_consulta\_tempo** e **log\_lenta\_administrador\_instruções**.

Para saber como definir o valor desses parâmetros através da CLI do Azure, veja [como configurar parâmetros de servidor](howto-configure-server-parameters-using-cli.md). 

Por exemplo, o seguinte comando da CLI ativa o registo de consulta lenta, define o tempo de consulta longo para 10 segundos e, em seguida, desativa o registo da instrução administrador lenta. Por fim, ele apresenta uma lista de opções de configuração para sua revisão.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Lista de registos da base de dados do Azure para o servidor MySQL
Para listar os ficheiros de registo disponíveis para o seu servidor, execute o [lista do az mysql server-logs](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) comando.

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
- Saiba mais sobre [registos do servidor na base de dados do Azure para MySQL](concepts-server-logs.md).
