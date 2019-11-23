---
title: Acessar logs de servidor no banco de dados do Azure para MariaDB usando CLI do Azure
description: Este artigo descreve como acessar os logs de servidor no banco de dados do Azure para MariaDB usando o utilitário de linha de comando CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: ffc724ef5133ee25643a966d2b6d8448a4c3a920
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023609"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurar e acessar logs de servidor usando CLI do Azure
Você pode baixar o banco de dados do Azure para logs do MariaDB Server usando CLI do Azure, o utilitário de linha de comando do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- [Banco de dados do Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- O [CLI do Azure](/cli/azure/install-azure-cli) ou Azure cloud Shell no navegador

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Configurar o log do banco de dados do Azure para MariaDB
Você pode configurar o servidor para acessar o log de consulta lenta do MariaDB executando as seguintes etapas:
1. Ative o registro em log definindo a **consulta de\_lenta\_** parâmetro de log como ativado.
2. Ajuste outros parâmetros, como **longo\_consulta\_tempo** e **log\_instruções de\_do administrador de\_lentas**.

Para saber como definir o valor desses parâmetros por meio de CLI do Azure, consulte [como configurar parâmetros de servidor](howto-configure-server-parameters-cli.md).

Por exemplo, o comando da CLI a seguir ativa o log de consultas lentas, define o tempo de consulta longa como 10 segundos e, em seguida, desativa o registro em log da instrução de administrador lenta. Por fim, ele lista as opções de configuração para sua análise.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Listar logs do banco de dados do Azure para o servidor MariaDB
Para listar os arquivos de log de consulta lenta disponíveis para seu servidor, execute o comando [AZ MariaDB Server-logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

Você pode listar os arquivos de log para o servidor **mydemoserver.MariaDB.Database.Azure.com** no grupo de recursos **MyResource**Group. Em seguida, direcione a lista de arquivos de log para um arquivo de texto chamado **log\_arquivos\_List. txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Baixar logs do servidor
Com o comando [AZ MariaDB Server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) , você pode baixar arquivos de log individuais para seu servidor.

Use o exemplo a seguir para baixar o arquivo de log específico para o servidor **mydemoserver.MariaDB.Database.Azure.com** no grupo de recursos **MyResource** Group para seu ambiente local.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [logs de consulta lentos no banco de dados do Azure para MariaDB](concepts-server-logs.md).
