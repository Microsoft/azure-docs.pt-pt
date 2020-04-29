---
title: Gerir réplicas de leitura - Azure CLI, REST API - Base de Dados Azure para MySQL
description: Aprenda a configurar e gerir réplicas de leitura na Base de Dados Azure para MySQL utilizando o Azure CLI ou REST API.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed57003c7a9a5a1a9d87aa2e8934af8c48b1d819
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063336"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Como criar e gerir réplicas de leitura em Base de Dados Azure para MySQL utilizando o Azure CLI e REST API

Neste artigo, você aprenderá a criar e gerir réplicas de leitura na Base de Dados Azure para o serviço MySQL usando o Azure CLI e REST API. Para saber mais sobre as réplicas de leitura, consulte a [visão geral.](concepts-read-replicas.md)

## <a name="azure-cli"></a>CLI do Azure
Pode criar e gerir réplicas de leitura utilizando o Azure CLI.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Uma [Base de Dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que será usada como servidor principal. 

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para base de dados Azure para servidores MySQL nos níveis de preços otimizados de Propósito Geral ou Memória. Certifique-se de que o servidor principal está num destes níveis de preços.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

O `az mysql server replica create` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos para onde o servidor de réplica será criado.  |
| nome | mydemoreplicaserver | O nome do novo servidor de réplicas que é criado. |
| source-server | mydemoserver | O nome ou identificação do servidor principal existente para replicar. |

Para criar uma réplica de `--location` leitura de região transversal, use o parâmetro. O exemplo cli abaixo cria a réplica nos EUA Ocidentais.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Para saber mais sobre quais as regiões em que pode criar uma réplica, visite o artigo da [réplica de leitura.](concepts-read-replicas.md) 

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração do servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de ter sido criada. Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores ao do mestre para garantir que a réplica é capaz de acompanhar o mestre.


### <a name="list-replicas-for-a-master-server"></a>Listar réplicas para um servidor principal

Para ver todas as réplicas para um determinado servidor principal, executar o seguinte comando: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

O `az mysql server replica list` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos para onde o servidor de réplica será criado.  |
| server-name | mydemoserver | O nome ou identificação do servidor principal. |

### <a name="stop-replication-to-a-replica-server"></a>Pare a replicação a um servidor de réplicas

> [!IMPORTANT]
> Parar a replicação num servidor é irreversível. Uma vez que a replicação tenha parado entre um mestre e uma réplica, não pode ser desfeita. O servidor de réplica torna-se então um servidor autónomo e agora suporta tanto a leitura como os escritos. Este servidor não pode ser transformado numa réplica novamente.

A replicação a um servidor de réplica de leitura pode ser interrompida utilizando o seguinte comando:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

O `az mysql server replica stop` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos onde o servidor de réplicas existe.  |
| nome | mydemoreplicaserver | O nome do servidor de réplicas para parar a replicação. |

### <a name="delete-a-replica-server"></a>Eliminar um servidor de réplicas

A eliminação de um servidor de réplica de leitura pode ser feita executando o **[servidor az mysql eliminar](/cli/azure/mysql/server)** o comando.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Eliminar um servidor principal

> [!IMPORTANT]
> Eliminar um servidor mestre interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor mestre. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor principal, pode executar o **[servidor az mysql eliminar](/cli/azure/mysql/server)** o comando.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>API REST
Pode criar e gerir réplicas de leitura utilizando a [API Azure REST](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Pode criar uma réplica de leitura utilizando a [Create API:](/rest/api/mysql/servers/create)

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
> Para saber mais sobre quais as regiões em que pode criar uma réplica, visite o artigo da [réplica de leitura.](concepts-read-replicas.md) 

Se não tiver definido `azure.replication_support` o parâmetro para **replicar** num servidor master otimizado de Propósito Geral ou Memória e reiniciado o servidor, receberá um erro. Complete esses dois passos antes de criar uma réplica.

Uma réplica é criada usando as mesmas definições de cálculo e armazenamento que o mestre. Após a criação de uma réplica, várias definições podem ser alteradas independentemente do servidor principal: geração de computação, vCores, armazenamento e período de retenção de back-up. O nível de preços também pode ser alterado de forma independente, exceto para ou a partir do nível Básico.


> [!IMPORTANT]
> Antes de uma definição de servidor principal ser atualizada para um novo valor, atualize a definição de réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

### <a name="list-replicas"></a>Réplicas de listas
Pode ver a lista de réplicas de um servidor principal utilizando a [lista de réplicas API:](/rest/api/mysql/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Pare a replicação a um servidor de réplicas
Pode parar a replicação entre um servidor principal e uma réplica de leitura utilizando a [atualização API](/rest/api/mysql/servers/update).

Depois de parar a replicação a um servidor principal e de uma réplica de leitura, não pode ser desfeita. A réplica de leitura torna-se um servidor autónomo que suporta tanto as leituras como as escritas. O servidor autónomo não pode voltar a ser transformado numa réplica.

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

### <a name="delete-a-master-or-replica-server"></a>Eliminar um servidor de mestre ou réplica
Para eliminar um servidor de mestrado ou réplica, utiliza a [API de exclusão:](/rest/api/mysql/servers/delete)

Quando se apaga um servidor principal, a replicação a todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores autónomos que agora suportam tanto as leituras como as escritas.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [réplicas de leitura](concepts-read-replicas.md)
