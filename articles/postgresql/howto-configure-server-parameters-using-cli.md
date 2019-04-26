---
title: Configure os parâmetros de serviço na base de dados do Azure para PostgreSQL
description: Este artigo descreve como configurar os parâmetros de serviço na base de dados do Azure para PostgreSQL com a linha de comandos da CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: c88518749129abed1cf43a70b9165035626a780f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421219"
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personalizar os parâmetros de configuração de servidor com a CLI do Azure
Pode listar, mostrar e atualizar os parâmetros de configuração para um servidor PostgreSQL do Azure utilizando a Interface de linha de comandos (CLI do Azure). Um subconjunto de configurações de mecanismos é exposto ao nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- Criar uma base de dados do Azure para o servidor PostgreSQL e base de dados seguindo [criar uma base de dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Instale [CLI do Azure](/cli/azure/install-azure-cli) interface de linha de comandos em sua máquina ou utilize o [Azure Cloud Shell](../cloud-shell/overview.md) no portal do Azure utilizando o seu browser.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Lista de parâmetros de configuração do servidor da base de dados do Azure para o servidor PostgreSQL
Para listar todos os parâmetros modificáveis num servidor e os respetivos valores, execute o [lista de configuração do az postgres server](/cli/azure/postgres/server/configuration) comando.

Pode listar os parâmetros de configuração de servidor para o servidor **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes de parâmetro de configuração do servidor
Para mostrar os detalhes sobre um parâmetro de configuração específica para um servidor, execute o [show do az postgres server configuration](/cli/azure/postgres/server/configuration) comando.

Este exemplo mostra detalhes sobre o **log\_min\_mensagens** parâmetro de configuração de servidor para servidor **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modificar o valor de parâmetro de configuração do servidor
Também pode modificar o valor de um determinado servidor parâmetro de configuração, que atualiza o valor de configuração subjacente para o mecanismo de servidor PostgreSQL. Para atualizar a configuração, utilize o [az postgres server configuration set](/cli/azure/postgres/server/configuration) comando. 

Para atualizar o **log\_min\_mensagens** parâmetro de configuração do servidor do server **mydemoserver.postgres.database.azure.com** no grupo de recursos  **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Se pretender repor o valor de um parâmetro de configuração, simplesmente optar por omitir o opcional `--value` parâmetro e o serviço aplica-se o valor predefinido. No acima de exemplo, teria o seguinte aspeto:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Este comando repõe a **log\_min\_mensagens** configuração para o valor predefinido **aviso**. Para obter mais informações sobre a configuração do servidor e os valores permitidos, consulte a documentação do PostgreSQL no [configuração do servidor](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Passos Seguintes
- Para configurar e aceder aos registos de servidor, consulte [os registos do servidor na base de dados do Azure para PostgreSQL](concepts-server-logs.md)
