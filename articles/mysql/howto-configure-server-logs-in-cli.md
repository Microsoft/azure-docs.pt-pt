---
title: Aceder a registos de consulta lenta - Azure CLI - Azure Database for MySQL
description: Este artigo descreve como aceder aos registos de consulta lenta na Base de Dados Azure para o MySQL utilizando o Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a662fe9300d78591cc7ae1fcff67f9b3f93ab8c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501223"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Configurar e aceder a registos de consulta lenta utilizando o Azure CLI
Pode baixar a Base de Dados Azure para registos de consulta lenta mySQL utilizando o Azure CLI, o utilitário da linha de comando Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- [Base de Dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [O Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell no navegador

## <a name="configure-logging"></a>Confiria a exploração madeireira
Pode configurar o servidor para aceder ao registo de consulta lenta MySQL, tomando os seguintes passos:
1. Ligue a consulta lenta fazendo o registo de consultas definindo o parâmetro **de \_ registo de \_ consulta lenta** para ON.
2. Selecione onde descodundo os registos para utilizar a ** \_ saída de registo**. Para enviar registos para armazenamento local e registos de diagnóstico do Monitor Azure, selecione **Ficheiro**. Para enviar registos apenas para registos do Monitor Azure, **selecione Nenhum**
3. Ajuste outros parâmetros, tais como ** \_ o \_ tempo de consulta longo** e as ** \_ declarações de \_ \_ administração lentas**.

Para aprender a definir o valor destes parâmetros através do Azure CLI, consulte [Como configurar os parâmetros do servidor](howto-configure-server-parameters-using-cli.md).

Por exemplo, o seguinte comando CLI liga o registo de consulta lenta, define o tempo de consulta longo para 10 segundos e, em seguida, desliga o registo da declaração de administração lenta. Finalmente, lista as opções de configuração para a sua revisão.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Registos de listas para Azure Database para servidor MySQL
Se **log_output** estiver configurado para "Ficheiro", pode aceder diretamente aos registos a partir do armazenamento local do servidor. Para listar os ficheiros de registo de consulta lenta disponíveis para o seu servidor, execute o comando [da lista de registos de servidores az mysql.](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)

Pode listar os ficheiros de registo **para mydemoserver.mysql.database.azure.com** de servidor no grupo de recursos **myresourcegroup**. Em seguida, direcione a lista de ficheiros de registo para um ficheiro de texto chamado ** \_ ficheiros \_ de registolist.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Baixar registos a partir do servidor
Se **log_output** estiver configurado para "Ficheiro", pode transferir ficheiros de registo individual do seu servidor com o comando [de descarregamento de registos de servidores az mysql.](/cli/azure/mysql/server-logs#az-mysql-server-logs-download)

Utilize o exemplo seguinte para descarregar o ficheiro de registo específico para o **servidor mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup** para o seu ambiente local.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [registos de consulta lenta na Base de Dados Azure para o MySQL](concepts-server-logs.md).
