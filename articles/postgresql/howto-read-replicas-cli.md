---
title: Gerenciar réplicas de leitura para o banco de dados do Azure para PostgreSQL-servidor único da CLI do Azure
description: Saiba como gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL-servidor único do CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 5946c74d0075e04112e840d78dd9f5f57bec7475
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309403"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Criar e gerenciar réplicas de leitura do CLI do Azure

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL no CLI do Azure. Para saber mais sobre réplicas de leitura, consulte a [visão geral](concepts-read-replicas.md).

## <a name="prerequisites"></a>Pré-requisitos
- Um [banco de dados do Azure para o servidor PostgreSQL](quickstart-create-server-up-azure-cli.md) ser o servidor mestre.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Essas etapas devem ser usadas para preparar um servidor mestre nas camadas de Uso Geral ou com otimização de memória.

O `azure.replication_support` parâmetro deve ser definido como **réplica** no servidor mestre. Quando esse parâmetro estático é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor.

1. Defina `azure.replication_support` como réplica.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Reinicie o para aplicar a alteração ao servidor.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

O comando [AZ postgres Server Replica Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group | myResourceGroup |  O grupo de recursos em que o servidor de réplica será criado.  |
| name | mydemoserver-replica | O nome do novo servidor de réplica, que é criado. |
| source-server | mydemoserver | O nome ou a ID de recurso do servidor mestre existente do qual replicar. |

No exemplo de CLI abaixo, a réplica é criada na mesma região que o mestre.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Para criar uma réplica de leitura entre regiões, use `--location` o parâmetro. O exemplo de CLI abaixo cria a réplica no oeste dos EUA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Para saber mais sobre em quais regiões você pode criar uma réplica, visite o [artigo conceitos de leitura de réplica](concepts-read-replicas.md). 

Se você não tiver definido `azure.replication_support` o parâmetro para **réplica** em um servidor mestre de uso geral ou com otimização de memória e reiniciado o servidor, você receberá um erro. Conclua essas duas etapas antes de criar uma réplica.

Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: geração de computação, vCores, armazenamento e período de retenção de backup. O tipo de preço também pode ser alterado de forma independente, exceto para ou da camada básica.

> [!IMPORTANT]
> Antes que uma configuração de servidor mestre seja atualizada para um novo valor, atualize a configuração de réplica para um valor igual ou maior. Essa ação ajuda a réplica a acompanhar as alterações feitas no mestre.

## <a name="list-replicas"></a>Listar réplicas
Você pode exibir a lista de réplicas de um servidor mestre usando o comando [AZ postgres Server da lista de réplicas](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Você pode interromper a replicação entre um servidor mestre e uma réplica de leitura usando o comando [AZ postgres Server Replica Stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

Depois de parar a replicação em um servidor mestre e em uma réplica de leitura, ela não pode ser desfeita. A réplica de leitura torna-se um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode ser tornado novamente em uma réplica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Excluir um servidor mestre ou de réplica
Para excluir um servidor mestre ou de réplica, use o comando [AZ postgres Server Delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

Quando você exclui um servidor mestre, a replicação para todas as réplicas de leitura é interrompida. As réplicas de leitura tornam-se servidores autônomos que agora dão suporte a leituras e gravações.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [réplicas de leitura no banco de dados do Azure para PostgreSQL](concepts-read-replicas.md).
* Saiba como [criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).