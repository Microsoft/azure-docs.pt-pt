---
title: Acessar logs de consulta lentos no banco de dados do Azure para MySQL usando CLI do Azure
description: Este artigo descreve como acessar os logs de consulta lenta no banco de dados do Azure para MySQL usando o CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 0ab4162d11642ec7df53040bd744711002227497
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030632"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Configurar e acessar logs de consulta lentos usando CLI do Azure
Você pode baixar os logs de consulta lenta do banco de dados do Azure para MySQL usando CLI do Azure, o utilitário de linha de comando do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- [Banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- O [CLI do Azure](/cli/azure/install-azure-cli) ou Azure cloud Shell no navegador

## <a name="configure-logging"></a>Configurar registro em log
Você pode configurar o servidor para acessar o log de consultas lentas do MySQL executando as seguintes etapas:
1. Ative o log de consultas lentas definindo a **consulta de\_lenta\_** parâmetro de log como on.
2. Ajuste outros parâmetros, como **longo\_consulta\_tempo** e **log\_instruções de\_do administrador de\_lentas**.

Para saber como definir o valor desses parâmetros por meio de CLI do Azure, consulte [como configurar parâmetros de servidor](howto-configure-server-parameters-using-cli.md).

Por exemplo, o comando da CLI a seguir ativa o log de consultas lentas, define o tempo de consulta longa como 10 segundos e, em seguida, desativa o registro em log da instrução de administrador lenta. Por fim, ele lista as opções de configuração para sua análise.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Listar logs para o servidor de banco de dados do Azure para MySQL
Para listar os arquivos de log de consulta lenta disponíveis para seu servidor, execute o comando [AZ MySQL Server-logs List](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) .

Você pode listar os arquivos de log para o servidor **mydemoserver.mysql.Database.Azure.com** no grupo de recursos **MyResource**Group. Em seguida, direcione a lista de arquivos de log para um arquivo de texto chamado **log\_arquivos\_List. txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Baixar logs do servidor
Com o comando [AZ MySQL Server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) , você pode baixar arquivos de log individuais para seu servidor. 

Use o exemplo a seguir para baixar o arquivo de log específico para o servidor **mydemoserver.mysql.Database.Azure.com** no grupo de recursos **MyResource** Group para seu ambiente local.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [logs de consulta lentos no banco de dados do Azure para MySQL](concepts-server-logs.md).
