---
title: Gerenciar réplicas de leitura-CLI do Azure, API REST-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL-servidor único do CLI do Azure e da API REST
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: fb0803987428ced688e83a37fae36c61b63a28a8
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770123"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Criar e gerenciar réplicas de leitura do CLI do Azure, API REST

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL usando o CLI do Azure e a API REST. Para saber mais sobre réplicas de leitura, consulte a [visão geral](concepts-read-replicas.md).

## <a name="azure-cli"></a>CLI do Azure
Você pode criar e gerenciar réplicas de leitura usando o CLI do Azure.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Um [banco de dados do Azure para o servidor PostgreSQL](quickstart-create-server-up-azure-cli.md) ser o servidor mestre.


### <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Essas etapas devem ser usadas para preparar um servidor mestre nas camadas de Uso Geral ou com otimização de memória.

O parâmetro `azure.replication_support` deve ser definido como **réplica** no servidor mestre. Quando esse parâmetro estático é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor.

1. Defina `azure.replication_support` como réplica.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Reinicie o servidor para aplicar a alteração.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

O comando [AZ postgres Server Replica Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group | myResourceGroup |  O grupo de recursos em que o servidor de réplica será criado.  |
| nome | mydemoserver-réplica | O nome do novo servidor de réplica que é criado. |
| source-server | mydemoserver | O nome ou a ID de recurso do servidor mestre existente do qual replicar. |

No exemplo de CLI abaixo, a réplica é criada na mesma região que o mestre.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Para criar uma réplica de leitura entre regiões, use o parâmetro `--location`. O exemplo de CLI abaixo cria a réplica no oeste dos EUA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo conceitos de leitura de réplica](concepts-read-replicas.md). 

Se você não tiver definido o parâmetro `azure.replication_support` como **réplica** em um servidor mestre com otimização de uso geral ou memória e reiniciado o servidor, você receberá um erro. Conclua essas duas etapas antes de criar uma réplica.

Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: geração de computação, vCores, armazenamento e período de retenção de backup. O tipo de preço também pode ser alterado de forma independente, exceto para ou da camada básica.

> [!IMPORTANT]
> Antes que uma configuração de servidor mestre seja atualizada para um novo valor, atualize a configuração de réplica para um valor igual ou maior. Essa ação ajuda a réplica a acompanhar as alterações feitas no mestre.

### <a name="list-replicas"></a>Listar réplicas
Você pode exibir a lista de réplicas de um servidor mestre usando o comando [AZ postgres Server da lista de réplicas](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação em um servidor de réplica
Você pode interromper a replicação entre um servidor mestre e uma réplica de leitura usando o comando [AZ postgres Server Replica Stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

Depois de parar a replicação em um servidor mestre e em uma réplica de leitura, ela não pode ser desfeita. A réplica de leitura torna-se um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode ser tornado novamente em uma réplica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Excluir um servidor mestre ou de réplica
Para excluir um servidor mestre ou de réplica, use o comando [AZ postgres Server Delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

Quando você exclui um servidor mestre, a replicação para todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores autônomos que agora dão suporte a leituras e gravações.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>API REST
Você pode criar e gerenciar réplicas de leitura usando a [API REST do Azure](/rest/api/azure/).

### <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Essas etapas devem ser usadas para preparar um servidor mestre nas camadas de Uso Geral ou com otimização de memória.

O parâmetro `azure.replication_support` deve ser definido como **réplica** no servidor mestre. Quando esse parâmetro estático é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor.

1. Defina `azure.replication_support` como réplica.

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

2. [Reinicie o servidor](/rest/api/postgresql/servers/restart) para aplicar a alteração.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Você pode criar uma réplica de leitura usando a [API criar](/rest/api/postgresql/servers/create):

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
> Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo conceitos de leitura de réplica](concepts-read-replicas.md). 

Se você não tiver definido o parâmetro `azure.replication_support` como **réplica** em um servidor mestre com otimização de uso geral ou memória e reiniciado o servidor, você receberá um erro. Conclua essas duas etapas antes de criar uma réplica.

Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: geração de computação, vCores, armazenamento e período de retenção de backup. O tipo de preço também pode ser alterado de forma independente, exceto para ou da camada básica.


> [!IMPORTANT]
> Antes que uma configuração de servidor mestre seja atualizada para um novo valor, atualize a configuração de réplica para um valor igual ou maior. Essa ação ajuda a réplica a acompanhar as alterações feitas no mestre.

### <a name="list-replicas"></a>Listar réplicas
Você pode exibir a lista de réplicas de um servidor mestre usando a [API da lista de réplicas](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação em um servidor de réplica
Você pode interromper a replicação entre um servidor mestre e uma réplica de leitura usando a [API de atualização](/rest/api/postgresql/servers/update).

Depois de parar a replicação em um servidor mestre e em uma réplica de leitura, ela não pode ser desfeita. A réplica de leitura torna-se um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode ser tornado novamente em uma réplica.

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

### <a name="delete-a-master-or-replica-server"></a>Excluir um servidor mestre ou de réplica
Para excluir um servidor mestre ou de réplica, use a [API de exclusão](/rest/api/postgresql/servers/delete):

Quando você exclui um servidor mestre, a replicação para todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores autônomos que agora dão suporte a leituras e gravações.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [réplicas de leitura no banco de dados do Azure para PostgreSQL](concepts-read-replicas.md).
* Saiba como [criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).