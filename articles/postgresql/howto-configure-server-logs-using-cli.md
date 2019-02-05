---
title: Configurar e aceder aos registos de servidor para o PostgreSQL com a CLI do Azure
description: Este artigo descreve como configurar e aceder os registos do servidor na base de dados do Azure para PostgreSQL com a linha de comando da CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 3ca77c1ffa5f5a3f384009299701f4aa674baf59
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700201"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurar e aceder aos registos de servidor com a CLI do Azure
Pode transferir os registos de erros de servidor PostgreSQL, utilizando a interface de linha de comandos (CLI do Azure). No entanto, o acesso aos logs de transação não é suportado. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- [Base de dados do Azure para o servidor PostgreSQL](quickstart-create-server-database-azure-cli.md)
- O [CLI do Azure](/cli/azure/install-azure-cli) utilitário da linha de comandos ou o Azure Cloud Shell no browser

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configurar o registo da base de dados do Azure para PostgreSQL
Pode configurar o servidor para aceder a registos de consulta e registos de erros. Registos de erros podem ter informações aspirador automática, ligação e ponto de verificação.
1. Ative o registo.
2. Para ativar o registo de consulta, atualize **log\_instrução** e **log\_min\_duração\_instrução**.
3. Período de retenção de atualização.

Para obter mais informações, consulte [personalizar os parâmetros de configuração de servidor](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Lista de registos da base de dados do Azure para o servidor PostgreSQL
Para listar os ficheiros de registo disponíveis para o seu servidor, execute o [lista do az postgres server-logs](/cli/azure/postgres/server-logs) comando.

Pode listar os ficheiros de registo para o servidor **mydemoserver.postgres.database.azure.com** sob o grupo de recursos **myresourcegroup**. Em seguida, direcionar a lista de ficheiros de registo para um arquivo de texto chamado **log\_arquivos\_txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Transferir localmente os registos do servidor
Com o [az postgres server-logs download](/cli/azure/postgres/server-logs) de comando, pode transferir ficheiros de registo individuais para o seu servidor. 

Utilize o exemplo seguinte para transferir o ficheiro de registo específico para o servidor **mydemoserver.postgres.database.azure.com** sob o grupo de recursos **myresourcegroup** ao seu ambiente local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Passos Seguintes
- Para saber mais sobre os registos do servidor, consulte [registos do servidor na base de dados do Azure para PostgreSQL](concepts-server-logs.md).
- Para obter mais informações sobre parâmetros do servidor, consulte [personalizar os parâmetros de configuração de servidor com a CLI do Azure](howto-configure-server-parameters-using-cli.md).
