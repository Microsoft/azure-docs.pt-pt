---
title: Aceder a registos de consulta lenta - Azure CLI - Azure Database for MariaDB
description: Este artigo descreve como aceder aos registos lentos na Base de Dados Azure para MariaDB utilizando o utilitário da linha de comando Azure CLI.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9c8f69f00ed4314fbe8b3fd1958b52c82ce55d99
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662394"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Configurar e aceder a Base de Dados Azure para registos de consulta lenta de Maria DB utilizando O Azure CLI

Você pode baixar a Base de Dados Azure para registos de consulta lenta MariaDB usando Azure CLI, o utilitário da linha de comando Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [O Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell no navegador

## <a name="configure-logging"></a>Confiria a exploração madeireira
Pode configurar o servidor para aceder ao registo de consulta lenta MySQL, tomando os seguintes passos:
1. Ligue a consulta lenta fazendo o registo de consultas definindo o parâmetro **de \_ registo de \_ consulta lenta** para ON.
2. Selecione onde descodundo os registos para utilizar a **\_ saída de registo**. Para enviar registos para armazenamento local e registos de diagnóstico do Monitor Azure, selecione **Ficheiro**. Para enviar registos apenas para registos do Monitor Azure, **selecione Nenhum**
3. Ajuste outros parâmetros, tais como **\_ o \_ tempo de consulta longo** e as **\_ declarações de \_ \_ administração lentas**.

Para aprender a definir o valor destes parâmetros através do Azure CLI, consulte [Como configurar os parâmetros do servidor](howto-configure-server-parameters-cli.md).

Por exemplo, o seguinte comando CLI liga o registo de consulta lenta, define o tempo de consulta longo para 10 segundos e, em seguida, desliga o registo da declaração de administração lenta. Finalmente, lista as opções de configuração para a sua revisão.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Registos de listas para Azure Database para servidor MariaDB
Se **log_output** estiver configurado para "Ficheiro", pode aceder diretamente aos registos a partir do armazenamento local do servidor. Para listar os ficheiros de registo de consulta lenta disponíveis para o seu servidor, execute o comando [da lista de registos de servidores az mariadb.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)

Pode listar os ficheiros de registo **para mydemoserver.mariadb.database.azure.com** de servidor no grupo de recursos **myresourcegroup**. Em seguida, direcione a lista de ficheiros de registo para um ficheiro de texto chamado **\_ ficheiros \_ de registolist.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Baixar registos a partir do servidor
Se **log_output** estiver configurado para "Ficheiro", pode transferir ficheiros de registo individual do seu servidor com o comando [de descarregamento de registos de servidores az mariadb.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download)

Utilize o exemplo seguinte para descarregar o ficheiro de registo específico para o **servidor mydemoserver.mariadb.database.azure.com** no grupo de recursos **myresourcegroup** para o seu ambiente local.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [registos de consultas lentas na Base de Dados Azure para MariaDB](concepts-server-logs.md).
