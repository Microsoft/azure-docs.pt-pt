---
title: Parâmetros de configuração - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Este artigo descreve como configurar os parâmetros postgres na Base de Dados Azure para PostgreSQL - Servidor Flexível utilizando o Azure CLI.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 315e4439ca66a3fbadac228c013797f516bc2940
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605769"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>Personalize os parâmetros do servidor para Azure Database para PostgreSQL - Servidor Flexível usando O Azure CLI

Pode listar, mostrar e atualizar parâmetros de configuração para um servidor Azure PostgreSQL utilizando a Interface da Linha de Comando (Azure CLI). Um subconjunto de parâmetros do motor é exposto ao nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia, precisa:
- Criar uma base de dados Azure para servidor e base de dados PostgreSQL seguindo [criar uma base de dados de Azure para PostgreSQL](quickstart-create-server-cli.md)
- Instale a interface de linha de comando [Azure CLI](/cli/azure/install-azure-cli) na sua máquina ou utilize o [Azure Cloud Shell](../../cloud-shell/overview.md) no portal Azure utilizando o seu navegador.

## <a name="list-server-parameters-for-a-flexible-server"></a>Listar os parâmetros do servidor para um servidor flexível

Para listar todos os parâmetros modificáveis num servidor e os seus valores, executar o comando da [lista de parâmetros flexíveis do servidor az postgres.](/cli/azure/postgres/flexible-server/parameter)

Pode listar os parâmetros do servidor para o **servidor mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup**.

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>Mostrar detalhes do parâmetro do servidor

Para mostrar detalhes sobre um parâmetro específico para um servidor, executar o comando [de indicação de parâmetros flexíveis do servidor az postgres.](/cli/azure/postgres/flexible-server/parameter)

Este exemplo mostra detalhes do parâmetro do servidor de **\_ \_ mensagens de registo min** para **servidor mydemoserver.postgres.database.azure.com** no âmbito do grupo de recursos **myresourcegroup.**

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>Modificar o valor do parâmetro do servidor

Também pode modificar o valor de um determinado parâmetro do servidor, que atualiza o valor de configuração subjacente ao motor do servidor PostgreSQL. Para atualizar o parâmetro, utilize o comando [de parâmetros de conjunto de parâmetros flexíveis do servidor az postgres.](/cli/azure/postgres/flexible-server/parameter) 

Para atualizar o parâmetro do servidor de **\_ \_ mensagens de registo min** de **mydemoserver.postgres.database.azure.com** no grupo de recursos **myresourcegroup.**

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

Se pretender redefinir o valor de um parâmetro, simplesmente optar por deixar de fora o `--value` parâmetro opcional, e o serviço aplica o valor predefinido. Acima de exemplo, pareceria:

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

Este comando reinicia o parâmetro de **\_ \_ mensagens de log min** ao valor predefinido **AVISO**. Para obter mais informações sobre os parâmetros do servidor e valores admissíveis, consulte a documentação PostgreSQL sobre [parâmetros de definição](https://www.postgresql.org/docs/12/config-setting.html).

## <a name="next-steps"></a>Passos seguintes

- Para configurar e aceder aos registos de servidores, consulte [registos de servidores na Base de Dados de Azure para PostgreSQL](concepts-logging.md)
