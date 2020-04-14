---
title: Acesso a registos de consultas lentas - Azure CLI - Base de Dados Azure para MySQL
description: Este artigo descreve como aceder aos registos de consulta lenta na Base de Dados Azure para MySQL utilizando o Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 87db1a2af0bfdc854c909ef4221a3d97f9bf10d5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270677"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Configure e aceda a registos de consultas lentas utilizando o Azure CLI
Pode descarregar a Base de Dados Azure para registos de consultas lentas MySQL utilizando o Azure CLI, o utilitário da linha de comando Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar, você precisa:
- [Base de dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- O [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell no navegador

## <a name="configure-logging"></a>Configurar a exploração madeireira
Pode configurar o servidor para aceder ao registo de consulta lenta MySQL, tomando os seguintes passos:
1. Ligue a marcação lenta da consulta, definindo o parâmetro de **\_\_registo** de consulta lenta para ON.
2. Selecione onde fazer a saída dos registos para utilizar a **saída de registo\_**. Para enviar registos tanto para o armazenamento local como para os registos de diagnóstico do Monitor Azure, selecione **File**. Para enviar registos apenas para registos do Monitor Azure, selecione **Nenhum**
3. Ajuste outros parâmetros, tais como tempo de **consulta\_\_longa** e sessão **\_de declarações de administração\_\_lentas**.

Para aprender a definir o valor destes parâmetros através do Azure CLI, consulte [como configurar os parâmetros](howto-configure-server-parameters-using-cli.md)do servidor .

Por exemplo, o comando CLI seguinte liga o registo de consulta lenta, define o tempo de consulta longa para 10 segundos e, em seguida, desliga o registo da declaração de administração lenta. Finalmente, ele lista as opções de configuração para a sua revisão.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Registos de listas para base de dados Azure para servidor MySQL
Se **log_output** estiver configurado para "File", pode aceder aos registos diretamente do armazenamento local do servidor. Para listar os ficheiros de registo de consulta lenta disponíveis para o seu servidor, execute o comando da lista de [registos de registos do servidor az mysql.](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)

Pode listar os ficheiros de registo para **mydemoserver.mysql.database.azure.com** do servidor no grupo de recursos **myresourcegroup**. Em seguida, direcione a lista de ficheiros de registo para um ficheiro de texto chamado **lista de ficheiros\_\_de registo.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Descarregue os registos do servidor
Se **log_output** estiver configurado para "File", pode descarregar ficheiros de registo individuais a partir do seu servidor com o comando [de descarregamento de registos de registos de servidora az mysql.](/cli/azure/mysql/server-logs#az-mysql-server-logs-download)

Utilize o seguinte exemplo para descarregar o ficheiro de registo específico para o servidor **mydemoserver.mysql.database.azure.com** sob o grupo de recursos **myresourcegroup** para o seu ambiente local.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os registos de [consultas lentas na Base de Dados Azure para MySQL](concepts-server-logs.md).
