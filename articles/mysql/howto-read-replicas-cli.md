---
title: Criar e gerir réplicas de leitura na base de dados do Azure para MySQL
description: Este artigo descreve como configurar e gerir réplicas de leitura na base de dados do Azure para MySQL com a CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: e291cb46b5f8cb8722348bd8fcd6031ed29beb9a
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888317"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli"></a>Como criar e gerir ler réplicas na base de dados do Azure para MySQL com a CLI do Azure

Neste artigo, aprenderá como criar e gerir réplicas de leitura na mesma região do Azure como o modelo na base de dados do Azure para o serviço de MySQL com a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Uma [base de dados do Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que será utilizado como o servidor mestre. 

> [!IMPORTANT]
> A funcionalidade de réplica de leitura só está disponível para a base de dados do Azure para MySQL servidores nos escalões de preços para fins gerais ou com otimização de memória. Certifique-se de que o servidor mestre está em um destes escalões de preço.

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

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

> [!NOTE]
> Réplicas de leitura são criadas com a mesma configuração de servidor como o modelo. A configuração do servidor de réplica pode ser alterada depois de este ter sido criado. Recomenda-se que a configuração do servidor de réplica deve ser mantida em valores iguais ou maiores do que o principal para garantir que a réplica é capaz de acompanhar o mestre.

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica

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

## <a name="delete-a-replica-server"></a>Eliminar um servidor de réplica

A eliminar um servidor de réplica de leitura pode ser feita ao executar o **[delete do az mysql server](/cli/azure/mysql/server)** comando.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Eliminar um servidor principal

> [!IMPORTANT]
> A eliminar um servidor principal para a replicação para todos os servidores de réplica e elimina o próprio servidor mestre. Servidores de réplica se tornar a servidores autónomos que agora oferecem suporte a leitura e escrita.

Para eliminar um servidor principal, pode executar o **[delete do az mysql server](/cli/azure/mysql/server)** comando.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Lista de réplicas para um servidor principal

Para ver todas as réplicas para um determinado servidor mestre, execute o seguinte comando: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

O `az mysql server replica list` comando requer os seguintes parâmetros:

| Definição | Valor de exemplo | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos em que o servidor de réplica será criado para.  |
| server-name | mydemoserver | O nome ou ID do servidor mestre. |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)
