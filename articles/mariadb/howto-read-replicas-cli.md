---
title: Gerir réplicas de leitura - Azure CLI, REST API - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar e gerir réplicas de leitura na Base de Dados Azure para MariaDB usando o ALI ELI e REST API.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ae413a1e0b5711a7c9c6bdec7317c62b4cb9733
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664958"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Como criar e gerir réplicas de leitura na Base de Dados Azure para MariaDB usando o ALI ELI e REST API

Neste artigo, você aprenderá a criar e gerir réplicas de leitura na Base de Dados Azure para o serviço MariaDB usando o ALI ELI e REST API.

## <a name="azure-cli"></a>CLI do Azure
Pode criar e gerir réplicas de leitura utilizando o Azure CLI.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli)
- Uma [Base de Dados Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) que será utilizado como servidor de origem. 

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a Base de Dados Azure para servidores MariaDB nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de origem está num destes níveis de preços.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando se cria uma réplica para uma fonte que não tem réplicas existentes, a fonte reinicia primeiro para se preparar para a replicação. Tome isto em consideração e execute estas operações durante um período fora do pico.

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

O `az mariadb server replica create` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos para onde o servidor de réplica será criado.  |
| name | mydemoreplicaserver | O nome do novo servidor de réplica que é criado. |
| source-server | mydemoserver | O nome ou ID do servidor de origem existente para se replicar. |

Para criar uma réplica de leitura de região cruzada, use o `--location` parâmetro. 

O exemplo do CLI abaixo cria a réplica nos EUA Ocidentais.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Para saber mais sobre em que regiões pode criar uma réplica, visite o [artigo conceitos de réplica lido.](concepts-read-replicas.md) 

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração do servidor que o mestre. A configuração do servidor de réplica pode ser alterada depois de ter sido criada. Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores à fonte para garantir que a réplica seja capaz de acompanhar o mestre.

### <a name="list-replicas-for-a-source-server"></a>Lista réplicas para um servidor de origem

Para visualizar todas as réplicas de um determinado servidor de origem, executar o seguinte comando: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

O `az mariadb server replica list` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos para onde o servidor de réplica será criado.  |
| server-name | mydemoserver | O nome ou identificação do servidor de origem. |

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação num servidor é irreversível. Uma vez que a replicação tenha parado entre uma fonte e uma réplica, não pode ser desfeita. O servidor de réplica torna-se então um servidor autónomo e agora suporta tanto a leitura como a escrita. Este servidor não pode ser transformado numa réplica novamente.

A replicação de um servidor de réplica de leitura pode ser interrompida utilizando o seguinte comando:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

O `az mariadb server replica stop` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos onde existe o servidor de réplica.  |
| name | mydemoreplicaserver | O nome do servidor de réplica para parar a replicação. |

### <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A eliminação de um servidor de réplicas de leitura pode ser feita executando o comando de exclusão do **[servidor az mariadb.](/cli/azure/mariadb/server)**

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> Eliminar um servidor de origem interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor de origem. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor de origem, pode executar o comando de exclusão do **[servidor az mariadb.](/cli/azure/mariadb/server)**

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>API REST
Pode criar e gerir réplicas de leitura utilizando a [API Azure REST](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura
Pode criar uma réplica de leitura utilizando a [API de criação:](/rest/api/mariadb/servers/create)

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
> Para saber mais sobre em que regiões pode criar uma réplica, visite o [artigo conceitos de réplica lido.](concepts-read-replicas.md) 

Se não tiver definido o `azure.replication_support` parâmetro para **REPLICA** num servidor de origem otimizado para Fins Gerais ou Memória e reiniciou o servidor, receberá um erro. Complete estes dois passos antes de criar uma réplica.

Uma réplica é criada utilizando as mesmas definições de computação e armazenamento que o mestre. Após a criação de uma réplica, várias configurações podem ser alteradas independentemente do servidor de origem: geração de cálculo, vCores, armazenamento e período de retenção de back-up. O nível de preços também pode ser alterado de forma independente, exceto de ou para o nível básico.


> [!IMPORTANT]
> Antes de uma definição de servidor de origem ser atualizada para um novo valor, atualize a definição de réplica para um valor igual ou maior. Esta ação ajuda a réplica a acompanhar quaisquer alterações feitas ao mestre.

### <a name="list-replicas"></a>Lista réplicas
Pode ver a lista de réplicas de um servidor de origem utilizando a [lista de réplicas API](/rest/api/mariadb/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Pode parar a replicação entre um servidor de origem e uma réplica de leitura utilizando a API de [atualização](/rest/api/mariadb/servers/update).

Depois de parar a replicação num servidor de origem e uma réplica de leitura, não pode ser desfeita. A réplica de leitura torna-se um servidor autónomo que suporta tanto as leituras como as escritas. O servidor autónomo não pode ser transformado numa réplica novamente.

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

### <a name="delete-a-source-or-replica-server"></a>Eliminar um servidor de origem ou réplica
Para eliminar um servidor de origem ou réplica, utilize a [API de exclusão](/rest/api/mariadb/servers/delete):

Quando elimina um servidor de origem, a replicação de todas as réplicas lidas é interrompida. As réplicas de leitura tornam-se servidores autónomos que agora suportam tanto as leituras como as escritas.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)