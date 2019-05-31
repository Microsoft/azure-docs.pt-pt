---
title: Gerir réplicas de leitura da base de dados do Azure para PostgreSQL - servidor único a partir da CLI do Azure
description: Saiba como gerir réplicas de leitura na base de dados do Azure para PostgreSQL - servidor único a partir da CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: 9a6a1a744a8441d2f082d4d14a3aba8aa1cfc09e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306026"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Criar e gerir réplicas de leitura a partir da CLI do Azure

Neste artigo, saiba como criar e gerir réplicas de leitura na base de dados do Azure para PostgreSQL a partir da CLI do Azure. Para saber mais acerca das réplicas de leitura, veja a [descrição geral](concepts-read-replicas.md).

> [!IMPORTANT]
> Pode criar uma réplica de leitura na mesma região que o servidor principal ou em qualquer outra região do Azure à sua escolha. A replicação entre regiões está atualmente em pré-visualização pública.

## <a name="prerequisites"></a>Pré-requisitos
- Uma [base de dados do Azure para o servidor PostgreSQL](quickstart-create-server-up-azure-cli.md) deve ser o servidor principal.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0 ou posterior da CLI do Azure. Para ver a versão instalada, execute o comando `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Estes passos devem ser utilizados para preparar um servidor mestre nos escalões fins gerais ou com otimização de memória.

O `azure.replication_support` parâmetro deve ser definido como **RÉPLICA** no servidor principal. Quando este parâmetro estático é alterado, reiniciar o servidor é necessário para que a alteração tenha efeito.

1. Definir `azure.replication_support` à RÉPLICA.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Reinicie para aplicar a alteração para o servidor.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

O [criar réplica do az postgres server](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group | myResourceGroup |  O grupo de recursos onde o servidor de réplica será criado.  |
| name | mydemoserver-replica | O nome do novo servidor de réplica, que é criado. |
| source-server | mydemoserver | O nome ou o recurso ID do servidor mestre existente para replicar a partir do. |

No exemplo da CLI abaixo, a réplica ser criada na mesma região que o mestre.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Para criar uma cruz região ler réplica, utilize o `--location` parâmetro. O exemplo da CLI abaixo cria a réplica na região E.U.A. oeste.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

Se ainda não definir o `azure.replication_support` parâmetro **RÉPLICA** numa finalidade geral ou com otimização de memória principal do servidor e reinicie o servidor, receberá um erro. Conclua esses dois passos antes de criar uma réplica.

Ao utilizar a mesma configuração de servidor como o mestre, é criada uma réplica. Depois de criar uma réplica, várias configurações podem ser alteradas independentemente do servidor mestre: computação geração, vCores, armazenamento e período de retenção de cópia de segurança. O escalão de preço também pode ser alterado de forma independente, exceto de ou para o escalão básico.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre é atualizada para novos valores, atualize a configuração de réplica para valores iguais ou superior. Esta ação garante que a réplica pode acompanhar todas as alterações efetuadas a mestre.

## <a name="list-replicas"></a>Lista de réplicas
Pode ver a lista de réplicas de um servidor mestre usando [lista de réplicas do az postgres server](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) comando.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Pode parar a replicação entre um servidor principal e uma réplica de leitura utilizando [parar de réplica do az postgres server](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) comando.

Depois de parar a replicação para um servidor principal e uma réplica de leitura, não pode ser anulada. A réplica de leitura torna-se um servidor autônomo que oferece suporte a leituras e gravações. O servidor autónomo não pode se transformar numa réplica novamente.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Eliminar um servidor de master ou à réplica.
Para eliminar um servidor de master ou a réplica, utilize o [delete do az postgres server](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) comando.

Quando elimina um servidor principal, a replicação para todas as réplicas de leitura é parada. As réplicas de leitura tornam-se a servidores autónomos que agora oferecem suporte a leituras e gravações.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [ler réplicas na base de dados do Azure para PostgreSQL](concepts-read-replicas.md).