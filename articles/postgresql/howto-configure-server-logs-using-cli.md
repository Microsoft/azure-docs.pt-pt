---
title: Gerir registos - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como configurar e aceder aos registos do servidor (.ficheiros de registo) na Base de Dados Azure para PostgreSQL - Servidor Único utilizando o ClI Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: be679be91d49516bd2f6c672eb53640cfad2ae2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763577"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configure e aceda aos registos dos servidores utilizando o Azure CLI
Pode descarregar os registos de erros do servidor PostgreSQL utilizando a interface da linha de comando (Azure CLI). No entanto, o acesso aos registos de transações não é suportado. 

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como guiar, você precisa:
- [Base de Dados Azure para servidor PostgreSQL](quickstart-create-server-database-azure-cli.md)
- O utilitário de linha de comando [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell no navegador

## <a name="configure-logging"></a>Configurar a exploração madeireira
Pode configurar o servidor para aceder a registos de consultas e registos de erros. Os registos de erros podem ter informações de auto-vácuo, ligação e checkpoint.
1. Ligue a exploração madeireira.
2. Para permitir a consulta de registo, atualize a **declaração de registo\_** e a declaração de **duração do\_\_\_log min**.
3. Atualizar o período de retenção.

Para mais informações, consulte os parâmetros de [configuração do servidor personalizados](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Registos de listas
Para listar os ficheiros de registo disponíveis para o seu servidor, execute o comando da lista de [registos de registos do servidor az postgres.](/cli/azure/postgres/server-logs)

Pode listar os ficheiros de registo para **servidor mydemoserver.postgres.database.azure.com** sob o grupo de recursos **myresourcegroup**. Em seguida, direcione a lista de ficheiros de registo para um ficheiro de texto chamado **lista de ficheiros\_\_de registo.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Descarregue registos localmente a partir do servidor
Com o comando [de descarregamento de registos de registos de registos de servidores az postgres,](/cli/azure/postgres/server-logs) pode descarregar ficheiros de registo individuais para o seu servidor. 

Utilize o seguinte exemplo para descarregar o ficheiro de registo específico para o servidor **mydemoserver.postgres.database.azure.com** sob o grupo de recursos **myresourcegroup** para o seu ambiente local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre os registos do servidor, consulte os registos do Servidor na Base de [Dados Azure para PostgreSQL](concepts-server-logs.md).
- Para obter mais informações sobre os parâmetros do servidor, consulte Personalizar os parâmetros de configuração do [servidor utilizando o Azure CLI](howto-configure-server-parameters-using-cli.md).
