---
title: Gerir réplicas de leitura - Azure CLI, REST API - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como gerir réplicas de leitura na Base de Dados Azure para PostgreSQL - Servidor Único do Azure CLI e REST API
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: bb2c83757bd86d02a93c52bacdd03ce89186614e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719777"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Criar e gerir réplicas de leitura do Azure CLI, REST API

Neste artigo, aprende-se a criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL utilizando a API Azure CLI e REST. Para saber mais sobre as réplicas de leitura, consulte a [visão geral.](concepts-read-replicas.md)

## <a name="azure-cli"></a>CLI do Azure
Pode criar e gerir réplicas de leitura utilizando o Azure CLI.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Uma [Base de Dados Azure para o servidor PostgreSQL](quickstart-create-server-up-azure-cli.md) para ser o servidor principal.


### <a name="prepare-the-master-server"></a>Preparar o servidor principal
Estes passos devem ser utilizados para preparar um servidor principal nos níveis Geral de Propósito ou Memória Otimizados.

O parâmetro `azure.replication_support` deve ser definido para **RÉPLICA** no servidor principal. Quando este parâmetro estático é alterado, é necessário reiniciar o servidor para que a alteração faça efeito.

1. Coloque `azure.replication_support` para A RÉPLICA.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Se obtém o erro "Valor Inválido dado" ao tentar definir o azure.replication_support a partir do Azure CLI, é provável que o seu servidor já tenha RÉPLICA definida por padrão. Um bug está a impedir que esta definição seja corretamente refletida em servidores mais recentes onde a REPLICA é a falha interna.
> Pode saltar os passos principais de preparação e ir criar a réplica.
> Se quiser confirmar que o seu servidor está nesta categoria, visite a página de replicação do servidor no portal Azure. A "replicação desativação" será cinzenta e "Adicionar réplica" estará ativa na barra de ferramentas.

2. Reinicie o servidor para aplicar a alteração.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

A réplica do [servidor az postgres criar](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group | myResourceGroup |  O grupo de recursos onde o servidor de réplica será criado.  |
| nome | mydemoserver-replica | O nome do novo servidor de réplica, que é criado. |
| source-server | mydemoserver | O nome ou identificação de recursos do servidor principal existente para replicar. |

No exemplo cli abaixo, a réplica é criada na mesma região que o mestre.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Para criar uma réplica de leitura de região transversal, use o parâmetro `--location`. O exemplo cli abaixo cria a réplica nos EUA Ocidentais.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Para saber mais sobre quais as regiões em que pode criar uma réplica, visite o artigo da [réplica de leitura.](concepts-read-replicas.md) 

Se não tiver definido o parâmetro `azure.replication_support` para **REPLICAR** num servidor master otimizado de Propósito Geral ou Memória e reiniciado o servidor, recebe um erro. Complete esses dois passos antes de criar uma réplica.

Uma réplica é criada usando as mesmas definições de cálculo e armazenamento que o mestre. Após a criação de uma réplica, várias definições podem ser alteradas independentemente do servidor principal: geração de computação, vCores, armazenamento e período de retenção de back-up. O tipo de preço também pode ser alterado de forma independente, exceto para ou da camada básica.

> [!IMPORTANT]
> Antes de uma definição de servidor principal ser atualizada para um novo valor, atualize a definição de réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

### <a name="list-replicas"></a>Réplicas de listas
Pode ver a lista de réplicas de um servidor principal utilizando o comando da lista de réplicas do [servidor az postgres.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Pode parar a replicação entre um servidor principal e uma réplica de leitura utilizando o comando de paragem de [réplica do servidor az postgres.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)

Depois de parar a replicação a um servidor principal e de uma réplica de leitura, não pode ser desfeita. A réplica de leitura torna-se um servidor autónomo que suporta tanto as leituras como as escritas. O servidor autônomo não pode ser tornado novamente em uma réplica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Eliminar um servidor de mestre ou réplica
Para eliminar um servidor de master ou réplica, utiliza o [servidor az postgres a eliminar](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) o comando.

Quando se apaga um servidor principal, a replicação a todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores autónomos que agora suportam tanto as leituras como as escritas.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>API REST
Pode criar e gerir réplicas de leitura utilizando a [API Azure REST](/rest/api/azure/).

### <a name="prepare-the-master-server"></a>Preparar o servidor principal
Estes passos devem ser utilizados para preparar um servidor principal nos níveis Geral de Propósito ou Memória Otimizados.

O parâmetro `azure.replication_support` deve ser definido para **RÉPLICA** no servidor principal. Quando este parâmetro estático é alterado, é necessário reiniciar o servidor para que a alteração faça efeito.

1. Coloque `azure.replication_support` para A RÉPLICA.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Reiniciar o servidor](/rest/api/postgresql/servers/restart) para aplicar a alteração.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Pode criar uma réplica de leitura utilizando a [Create API:](/rest/api/postgresql/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Para saber mais sobre quais as regiões em que pode criar uma réplica, visite o artigo da [réplica de leitura.](concepts-read-replicas.md) 

Se não tiver definido o parâmetro `azure.replication_support` para **REPLICAR** num servidor master otimizado de Propósito Geral ou Memória e reiniciado o servidor, recebe um erro. Complete esses dois passos antes de criar uma réplica.

Uma réplica é criada usando as mesmas definições de cálculo e armazenamento que o mestre. Após a criação de uma réplica, várias definições podem ser alteradas independentemente do servidor principal: geração de computação, vCores, armazenamento e período de retenção de back-up. O tipo de preço também pode ser alterado de forma independente, exceto para ou da camada básica.


> [!IMPORTANT]
> Antes de uma definição de servidor principal ser atualizada para um novo valor, atualize a definição de réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

### <a name="list-replicas"></a>Réplicas de listas
Pode ver a lista de réplicas de um servidor principal utilizando a [lista de réplicas API:](/rest/api/postgresql/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Pode parar a replicação entre um servidor principal e uma réplica de leitura utilizando a [atualização API](/rest/api/postgresql/servers/update).

Depois de parar a replicação a um servidor principal e de uma réplica de leitura, não pode ser desfeita. A réplica de leitura torna-se um servidor autónomo que suporta tanto as leituras como as escritas. O servidor autônomo não pode ser tornado novamente em uma réplica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Eliminar um servidor de mestre ou réplica
Para eliminar um servidor de mestrado ou réplica, utiliza a [API de exclusão:](/rest/api/postgresql/servers/delete)

Quando se apaga um servidor principal, a replicação a todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores autónomos que agora suportam tanto as leituras como as escritas.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [réplicas de leitura na Base de Dados Azure para PostgreSQL](concepts-read-replicas.md).
* Aprenda a [criar e gerir réplicas de leitura no portal Azure.](howto-read-replicas-portal.md)
