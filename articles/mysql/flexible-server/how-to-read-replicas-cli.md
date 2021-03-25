---
title: Gerir réplicas de leitura na Base de Dados Azure para o MySQL Flexible Server utilizando o Azure CLI.
description: Saiba como configurar e gerir réplicas de leitura na Base de Dados Azure para o servidor flexível MySQL utilizando o Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: bc95cd3ab471826538a551687c38d1422e4b7163
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108660"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Como criar e gerir réplicas de leitura na Base de Dados Azure para servidor flexível MySQL utilizando o Azure CLI

> [!IMPORTANT]
> Leia réplicas na Base de Dados Azure para MySQL - Servidor Flexível está em pré-visualização.

Neste artigo, você aprenderá a criar e gerir réplicas de leitura na Base de Dados Azure para o servidor flexível MySQL usando o Azure CLI. Para saber mais sobre as réplicas lidas, consulte a [visão geral.](concepts-read-replicas.md)

> [!Note]
> A réplica não é suportada em servidor ativado por alta disponibilidade. 

## <a name="azure-cli"></a>CLI do Azure
Pode criar e gerir réplicas de leitura utilizando o Azure CLI.

### <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli)
- Uma [base de dados Azure para o MySQL Flexible Server](quickstart-create-server-cli.md) que será usada como servidor de origem.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando se cria uma réplica para uma fonte que não tem réplicas existentes, a fonte reinicia primeiro para se preparar para a replicação. Tome isto em consideração e execute estas operações durante um período fora do pico.

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> As réplicas de leitura são criadas com a mesma configuração do servidor que a fonte. A configuração do servidor de réplica pode ser alterada depois de ter sido criada. O servidor de réplica é sempre criado no mesmo grupo de recursos, na mesma localização e na mesma subscrição que o servidor de origem. Se pretender criar um servidor de réplica para um grupo de recursos diferente ou uma subscrição diferente, pode [mover o servidor de réplica](../../azure-resource-manager/management/move-resource-group-and-subscription.md) após a criação. Recomenda-se que a configuração do servidor de réplica seja mantida em valores iguais ou superiores à fonte para garantir que a réplica seja capaz de acompanhar a origem.


### <a name="list-replicas-for-a-source-server"></a>Lista réplicas para um servidor de origem

Para visualizar todas as réplicas de um determinado servidor de origem, executar o seguinte comando: 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

> [!IMPORTANT]
> Parar a replicação num servidor é irreversível. Uma vez que a replicação tenha parado entre uma fonte e uma réplica, não pode ser desfeita. O servidor de réplica torna-se então um servidor autónomo e agora suporta tanto a leitura como a escrita. Este servidor não pode ser transformado numa réplica novamente.

A replicação de um servidor de réplica de leitura pode ser interrompida utilizando o seguinte comando:

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A eliminação de um servidor de réplicas de leitura pode ser feita executando o comando de exclusão do **[servidor az mysql.](/cli/azure/mysql/server)**

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> Eliminar um servidor de origem interrompe a replicação de todos os servidores de réplica e elimina o próprio servidor de origem. Os servidores de réplica tornam-se servidores autónomos que suportam agora tanto leitura como escritas.

Para eliminar um servidor de origem, pode executar o comando **[de eliminação flexível do servidor az mysql.](/cli/azure/mysql/flexible-server)**

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)