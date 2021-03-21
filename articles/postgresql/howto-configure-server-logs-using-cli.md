---
title: Gerir registos - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como configurar e aceder aos registos do servidor (.log ficheiros) na Base de Dados Azure para PostgreSQL - Servidor Único utilizando o CLI Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71dac0b6ea2202e712280607a73e860ae68bdb73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005593"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurar e aceder aos registos do servidor utilizando o Azure CLI
Pode descarregar os registos de erro do servidor PostgreSQL utilizando a interface de linha de comando (Azure CLI). No entanto, o acesso aos registos de transações não é suportado. 

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa:
- [Base de Dados Azure para servidor PostgreSQL](quickstart-create-server-database-azure-cli.md)
- O utilitário da linha de comando [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell no navegador

## <a name="configure-logging"></a>Confiria a exploração madeireira
Pode configurar o servidor para aceder a registos de consulta e registos de erros. Os registos de erro podem ter informações de aspiração automática, ligação e verificação de pontos de verificação.
1. Ligue o registo.
2. Para ativar o registo de consultas, atualize a **\_ declaração de registo** e a **\_ \_ \_ declaração de duração do registo** min .
3. Período de retenção de atualização.

Para obter mais informações, consulte [personalizar os parâmetros de configuração do servidor](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Registos de listas
Para listar os ficheiros de registo disponíveis para o seu servidor, execute o comando [da lista de registos de registos de registos de postais az.](/cli/azure/postgres/server-logs)

Pode listar os ficheiros de registo **para mydemoserver.postgres.database.azure.com** de servidor no grupo de recursos **myresourcegroup**. Em seguida, direcione a lista de ficheiros de registo para um ficheiro de texto chamado **\_ ficheiros \_ de registolist.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Faça o download de registos localmente a partir do servidor
Com o comando [de descarregamento de registos de registos de registos de postais az,](/cli/azure/postgres/server-logs) pode descarregar ficheiros de registo individual para o seu servidor. 

Utilize o exemplo seguinte para descarregar o ficheiro de registo específico para o **servidor mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup** para o seu ambiente local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre registos de servidores, consulte [os registos do Servidor na Base de Dados Azure para PostgreSQL](concepts-server-logs.md).
- Para obter mais informações sobre os parâmetros do servidor, consulte [personalizar os parâmetros de configuração do servidor utilizando o Azure CLI](howto-configure-server-parameters-using-cli.md).
