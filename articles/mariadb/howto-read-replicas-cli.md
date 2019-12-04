---
title: Gerenciar réplicas de leitura-CLI do Azure, API REST-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar e gerenciar réplicas de leitura no banco de dados do Azure para MariaDB usando o CLI do Azure e a API REST.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e9353bb5d472cc8dc798e7e09aed2183e48124ed
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765839"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MariaDB usando o CLI do Azure e a API REST

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no banco de dados do Azure para o serviço MariaDB usando o CLI do Azure e a API REST.

## <a name="azure-cli"></a>CLI do Azure
Você pode criar e gerenciar réplicas de leitura usando o CLI do Azure.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Um [banco de dados do Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) que será usado como o servidor mestre. 

> [!IMPORTANT]
> O recurso ler réplica só está disponível para o banco de dados do Azure para servidores MariaDB nos tipos de preço Uso Geral ou com otimização de memória. Verifique se o servidor mestre está em um desses tipos de preço.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

O comando `az mariadb server replica create` requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos para o qual o servidor de réplica será criado.  |
| nome | mydemoreplicaserver | O nome do novo servidor de réplica que é criado. |
| source-server | mydemoserver | O nome ou a ID do servidor mestre existente do qual replicar. |

Para criar uma réplica de leitura entre regiões, use o parâmetro `--location`. 

> [!NOTE]
> A replicação entre regiões está em versão prévia.

O exemplo de CLI abaixo cria a réplica no oeste dos EUA.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo conceitos de leitura de réplica](concepts-read-replicas.md). 

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração de servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de ser criada. É recomendável que a configuração do servidor de réplica seja mantida em valores iguais ou maiores que o mestre para garantir que a réplica seja capaz de acompanhar o mestre.

### <a name="list-replicas-for-a-master-server"></a>Listar réplicas para um servidor mestre

Para exibir todas as réplicas de um determinado servidor mestre, execute o seguinte comando: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

O comando `az mariadb server replica list` requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos para o qual o servidor de réplica será criado.  |
| server-name | mydemoserver | O nome ou a ID do servidor mestre. |

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação em um servidor de réplica

> [!IMPORTANT]
> Parar a replicação em um servidor é irreversível. Depois que a replicação for interrompida entre um mestre e uma réplica, ela não poderá ser desfeita. O servidor de réplica se torna um servidor autônomo e agora dá suporte à leitura e às gravações. Este servidor não pode ser tornado novamente em uma réplica.

A replicação para um servidor de réplica de leitura pode ser interrompida usando o seguinte comando:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

O comando `az mariadb server replica stop` requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos no qual o servidor de réplica existe.  |
| nome | mydemoreplicaserver | O nome do servidor de réplica no qual interromper a replicação. |

### <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A exclusão de um servidor de réplica de leitura pode ser feita executando o comando **[AZ MariaDB Server Delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Excluir um servidor mestre

> [!IMPORTANT]
> Eliminar um servidor mestre interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor mestre. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para excluir um servidor mestre, você pode executar o comando **[AZ MariaDB Server Delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>API REST
Você pode criar e gerenciar réplicas de leitura usando a [API REST do Azure](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Você pode criar uma réplica de leitura usando a [API criar](/rest/api/mariadb/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
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
Você pode exibir a lista de réplicas de um servidor mestre usando a [API da lista de réplicas](/rest/api/mariadb/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação em um servidor de réplica
Você pode interromper a replicação entre um servidor mestre e uma réplica de leitura usando a [API de atualização](/rest/api/mariadb/servers/update).

Depois de parar a replicação em um servidor mestre e em uma réplica de leitura, ela não pode ser desfeita. A réplica de leitura torna-se um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode ser tornado novamente em uma réplica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Excluir um servidor mestre ou de réplica
Para excluir um servidor mestre ou de réplica, use a [API de exclusão](/rest/api/mariadb/servers/delete):

Quando você exclui um servidor mestre, a replicação para todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores autônomos que agora dão suporte a leituras e gravações.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [réplicas de leitura](concepts-read-replicas.md)
