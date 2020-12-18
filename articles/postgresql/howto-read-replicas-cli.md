---
title: Gerir réplicas de leitura - Azure CLI, REST API - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como gerir réplicas de leitura na Base de Dados Azure para PostgreSQL - Servidor Único a partir do Azure CLI e REST API
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7e74a58a14bdcc2a6fe1e9f86305aae415c6abf7
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674519"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Criar e gerir réplicas de leitura do Azure CLI, REST API

Neste artigo, aprende-se a criar e gerir réplicas de leitura na Base de Dados Azure para PostgreSQL utilizando o ALI ELI e a API REST. Para saber mais sobre as réplicas lidas, consulte a [visão geral.](concepts-read-replicas.md)

## <a name="azure-replication-support"></a>Suporte de replicação do Azure
[Leia réplicas](concepts-read-replicas.md) e [descodões lógicas](concepts-logical.md) ambos dependem do postgres escrever antecipadamente log (WAL) para obter informações. Estas duas funcionalidades precisam de diferentes níveis de registo de postgres. A descodão lógica precisa de um nível mais elevado de registo do que réplicas lidas.

Para configurar o nível certo de registo, utilize o parâmetro de suporte de replicação Azure. O suporte de replicação Azure tem três opções de definição:

* **Off** - Coloca a menor informação no WAL. Esta definição não está disponível na maioria dos servidores Azure Database para servidores PostgreSQL.  
* **Réplica** - Mais verboso do que **desligado**. Este é o nível mínimo de exploração madeireira necessário para [que as réplicas de leitura](concepts-read-replicas.md) funcionem. Esta definição é o padrão na maioria dos servidores.
* **Lógico** - Mais verboso do que **réplica.** Este é o nível mínimo de exploração madeireira para a descodão lógica para funcionar. As réplicas de leitura também funcionam neste cenário.


> [!NOTE]
> Ao implementar réplicas de leitura para cargas primárias intensivas persistentes, o lag de replicação pode continuar a crescer e pode nunca ser capaz de recuperar com as primárias. Isto também pode aumentar o uso de armazenamento na primária, uma vez que os ficheiros WAL não são eliminados até que sejam recebidos na réplica.

## <a name="azure-cli"></a>CLI do Azure
Pode criar e gerir réplicas de leitura utilizando o Azure CLI.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli)
- Uma [base de dados Azure para o servidor PostgreSQL](quickstart-create-server-up-azure-cli.md) ser o servidor primário.


### <a name="prepare-the-primary-server"></a>Preparar o servidor primário

1. Verifique o valor do servidor `azure.replication_support` primário. Deve ser pelo menos RÉPLICA para réplicas de leitura para funcionar.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Se `azure.replication_support` não for pelo menos RÉPLICA, desa estale-o. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Reinicie o servidor para aplicar a alteração.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

A [réplica do servidor az postgres criar](/cli/azure/postgres/server/replica#az-postgres-server-replica-create) o comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group | myResourceGroup |  O grupo de recursos onde o servidor de réplica será criado.  |
| name | réplica mydemoserver | O nome do novo servidor de réplica que é criado. |
| source-server | mydemoserver | O nome ou identificação de recursos do servidor primário existente para se replicar. Utilize o ID do recurso se quiser que os grupos de recursos da réplica e do mestre sejam diferentes. |

No exemplo do CLI abaixo, a réplica é criada na mesma região que o mestre.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Para criar uma réplica de leitura de região cruzada, use o `--location` parâmetro. O exemplo do CLI abaixo cria a réplica nos EUA Ocidentais.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Para saber mais sobre em que regiões pode criar uma réplica, visite o [artigo conceitos de réplica lido.](concepts-read-replicas.md) 

Se não definiu o `azure.replication_support` parâmetro para **REPLICA** num servidor primário otimizado para fins gerais ou memória e reiniciou o servidor, receberá um erro. Complete estes dois passos antes de criar uma réplica.

> [!IMPORTANT]
> Reveja a [secção de considerações da visão geral da Réplica de Leitura.](concepts-read-replicas.md#considerations)
>
> Antes de uma definição de servidor primário ser atualizada para um novo valor, atualize a definição de réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

### <a name="list-replicas"></a>Lista réplicas
Pode ver a lista de réplicas de um servidor primário utilizando o comando da [lista de réplicas do servidor az postgres.](/cli/azure/postgres/server/replica#az-postgres-server-replica-list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Pode parar a replicação entre um servidor primário e uma réplica de leitura utilizando o comando [de stop de réplica do servidor az postgres.](/cli/azure/postgres/server/replica#az-postgres-server-replica-stop)

Depois de parar a replicação num servidor primário e uma réplica de leitura, não pode ser desfeita. A réplica de leitura torna-se um servidor autónomo que suporta tanto as leituras como as escritas. O servidor autónomo não pode ser transformado numa réplica novamente.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Eliminar um servidor primário ou de réplica
Para eliminar um servidor primário ou de réplica, utilize o [comando de eliminação do servidor az postgres.](/cli/azure/postgres/server#az-postgres-server-delete)

Quando elimina um servidor primário, a replicação de todas as réplicas lidas é interrompida. As réplicas de leitura tornam-se servidores autónomos que agora suportam tanto as leituras como as escritas.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>API REST
Pode criar e gerir réplicas de leitura utilizando a [API Azure REST](/rest/api/azure/).

### <a name="prepare-the-primary-server"></a>Preparar o servidor primário

1. Verifique o valor do servidor `azure.replication_support` primário. Deve ser pelo menos RÉPLICA para réplicas de leitura para funcionar.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Se `azure.replication_support` não for pelo menos RÉPLICA, desa estale-o.

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
Pode criar uma réplica de leitura utilizando a [API de criação:](/rest/api/postgresql/servers/create)

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
> Para saber mais sobre em que regiões pode criar uma réplica, visite o [artigo conceitos de réplica lido.](concepts-read-replicas.md) 

Se não definiu o `azure.replication_support` parâmetro para **REPLICA** num servidor primário otimizado para fins gerais ou memória e reiniciou o servidor, receberá um erro. Complete estes dois passos antes de criar uma réplica.

Uma réplica é criada utilizando as mesmas definições de computação e armazenamento que o mestre. Após a criação de uma réplica, várias configurações podem ser alteradas independentemente do servidor primário: geração de computação, vCores, armazenamento e período de retenção de back-up. O nível de preços também pode ser alterado de forma independente, exceto de ou para o nível básico.


> [!IMPORTANT]
> Antes de uma definição de servidor primário ser atualizada para um novo valor, atualize a definição de réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

### <a name="list-replicas"></a>Lista réplicas
Pode ver a lista de réplicas de um servidor primário utilizando a [lista de réplicas API](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Pode parar a replicação entre um servidor primário e uma réplica de leitura utilizando a API de [atualização](/rest/api/postgresql/servers/update).

Depois de parar a replicação num servidor primário e uma réplica de leitura, não pode ser desfeita. A réplica de leitura torna-se um servidor autónomo que suporta tanto as leituras como as escritas. O servidor autónomo não pode ser transformado numa réplica novamente.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>Eliminar um servidor primário ou de réplica
Para eliminar um servidor primário ou de réplica, utilize a [API de exclusão](/rest/api/postgresql/servers/delete):

Quando elimina um servidor primário, a replicação de todas as réplicas lidas é interrompida. As réplicas de leitura tornam-se servidores autónomos que agora suportam tanto as leituras como as escritas.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [as réplicas lidas na Base de Dados Azure para PostgreSQL](concepts-read-replicas.md).
* Saiba como [criar e gerir réplicas de leitura no portal Azure.](howto-read-replicas-portal.md)