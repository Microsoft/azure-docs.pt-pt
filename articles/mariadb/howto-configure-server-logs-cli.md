---
title: Acesso a registos de consultas lentas - Azure CLI - Base de Dados Azure para MariaDB
description: Este artigo descreve como aceder aos registos lentos na Base de Dados Azure para MariaDB utilizando o utilitário de linha de comando Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 75efdd8ed855fe78651fce5828aacb2384052ae5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81270541"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Configure e aceda a registos de consultas lentas utilizando o Azure CLI
Pode baixar a Base de Dados Azure para registos de consultas lentas MariaDB utilizando o Azure CLI, o utilitário da linha de comando Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar, você precisa:
- [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- O [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell no navegador

## <a name="configure-logging"></a>Configurar a exploração madeireira
Pode configurar o servidor para aceder ao registo de consulta lenta MySQL, tomando os seguintes passos:
1. Ligue a marcação lenta da consulta, definindo o parâmetro de **\_\_registo** de consulta lenta para ON.
2. Selecione onde fazer a saída dos registos para utilizar a **saída de registo\_**. Para enviar registos tanto para o armazenamento local como para os registos de diagnóstico do Monitor Azure, selecione **File**. Para enviar registos apenas para registos do Monitor Azure, selecione **Nenhum**
3. Ajuste outros parâmetros, tais como tempo de **consulta\_\_longa** e sessão **\_de declarações de administração\_\_lentas**.

Para aprender a definir o valor destes parâmetros através do Azure CLI, consulte [como configurar os parâmetros](howto-configure-server-parameters-cli.md)do servidor .

Por exemplo, o comando CLI seguinte liga o registo de consulta lenta, define o tempo de consulta longa para 10 segundos e, em seguida, desliga o registo da declaração de administração lenta. Finalmente, ele lista as opções de configuração para a sua revisão.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Lista de registos para Base de Dados Azure para servidor MariaDB
Se **log_output** estiver configurado para "File", pode aceder aos registos diretamente do armazenamento local do servidor. Para listar os ficheiros de registo de consulta lenta disponíveis para o seu servidor, execute o comando da lista de [registos de servidores az mariadb.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)

Pode listar os ficheiros de registo para **mydemoserver.mariadb.database.azure.com** do servidor no grupo de recursos **myresourcegroup**. Em seguida, direcione a lista de ficheiros de registo para um ficheiro de texto chamado **lista de ficheiros\_\_de registo.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Descarregue os registos do servidor
Se **log_output** estiver configurado para "File", pode descarregar ficheiros de registo individuais a partir do seu servidor com o comando [de descarregamento de registos de registos de servidora az mariadb.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download)

Utilize o seguinte exemplo para descarregar o ficheiro de registo específico para o servidor **mydemoserver.mariadb.database.azure.com** sob o grupo de recursos **myresourcegroup** para o seu ambiente local.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os registos de [consultas lentas na Base de Dados Azure para MariaDB](concepts-server-logs.md).
