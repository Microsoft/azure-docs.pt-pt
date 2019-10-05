---
title: Criar & gerenciar réplicas de leitura-banco de dados do Azure para MySQL
description: Saiba como configurar e gerenciar réplicas de leitura no banco de dados do Azure para MySQL usando o CLI do Azure ou a API REST.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/14/2019
ms.openlocfilehash: 741b50bdb2ec9c8d29a9f759e46209856de3a49c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970316"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MySQL usando o CLI do Azure e a API REST

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no serviço de banco de dados do Azure para MySQL usando o CLI do Azure e a API REST. Para saber mais sobre réplicas de leitura, consulte a [visão geral](concepts-read-replicas.md).

## <a name="azure-cli"></a>CLI do Azure
Você pode criar e gerenciar réplicas de leitura usando o CLI do Azure.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Uma [base de dados do Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que será utilizado como o servidor mestre. 

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a base de dados do Azure para MySQL servidores nos escalões de preços para fins gerais ou com otimização de memória. Certifique-se de que o servidor mestre está em um destes escalões de preço.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado com o seguinte comando:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

O `az mysql server replica create` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos em que o servidor de réplica será criado para.  |
| nome | mydemoreplicaserver | O nome do novo servidor de réplica, que é criado. |
| source-server | mydemoserver | O nome ou ID do servidor mestre existente para replicar a partir do. |

Para criar uma réplica de leitura entre regiões, use o parâmetro `--location`. O exemplo de CLI abaixo cria a réplica no oeste dos EUA.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo conceitos de leitura de réplica](concepts-read-replicas.md). 

> [!NOTE]
> Réplicas de leitura são criadas com a mesma configuração de servidor como o modelo. A configuração do servidor de réplica pode ser alterada depois de este ter sido criado. Recomenda-se que a configuração do servidor de réplica deve ser mantida em valores iguais ou maiores do que o principal para garantir que a réplica é capaz de acompanhar o mestre.


### <a name="list-replicas-for-a-master-server"></a>Lista de réplicas para um servidor principal

Para ver todas as réplicas para um determinado servidor mestre, execute o seguinte comando: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

O `az mysql server replica list` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos em que o servidor de réplica será criado para.  |
| server-name | mydemoserver | O nome ou ID do servidor mestre. |

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> A parar a replicação para um servidor é irreversível. Assim que a replicação foi parado entre um mestre e de réplica, não pode ser anulada. O servidor de réplica, em seguida, torna-se um servidor autónomo e agora oferece suporte a leitura e escrita. Este servidor não pode se transformar numa réplica novamente.

Pode ser parada a replicação para um servidor de réplica de leitura com o seguinte comando:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

O `az mysql server replica stop` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos onde existe o servidor de réplica.  |
| nome | mydemoreplicaserver | O nome do servidor de réplicas para parar a replicação. |

### <a name="delete-a-replica-server"></a>Eliminar um servidor de réplica

A eliminar um servidor de réplica de leitura pode ser feita ao executar o **[delete do az mysql server](/cli/azure/mysql/server)** comando.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Eliminar um servidor principal

> [!IMPORTANT]
> A eliminar um servidor principal para a replicação para todos os servidores de réplica e elimina o próprio servidor mestre. Servidores de réplica se tornar a servidores autónomos que agora oferecem suporte a leitura e escrita.

Para eliminar um servidor principal, pode executar o **[delete do az mysql server](/cli/azure/mysql/server)** comando.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>API REST
Você pode criar e gerenciar réplicas de leitura usando a [API REST do Azure](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Você pode criar uma réplica de leitura usando a [API criar](/rest/api/mysql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
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
Você pode exibir a lista de réplicas de um servidor mestre usando a [API da lista de réplicas](/rest/api/mysql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Você pode interromper a replicação entre um servidor mestre e uma réplica de leitura usando a [API de atualização](/rest/api/mysql/servers/update).

Depois de parar a replicação em um servidor mestre e em uma réplica de leitura, ela não pode ser desfeita. A réplica de leitura torna-se um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode ser tornado novamente em uma réplica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Excluir um servidor mestre ou de réplica
Para excluir um servidor mestre ou de réplica, use a [API de exclusão](/rest/api/mysql/servers/delete):

Quando você exclui um servidor mestre, a replicação para todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores autônomos que agora dão suporte a leituras e gravações.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)
