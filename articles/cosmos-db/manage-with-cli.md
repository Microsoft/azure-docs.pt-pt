---
title: Gerir recursos do Azure Cosmos DB com a CLI do Azure
description: Utilize a CLI do Azure para gerir a conta do Azure Cosmos DB, base de dados e contentores.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 144515fef9da714ab80f15bb39757ed2283c6dd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243383"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Gerir recursos do Cosmos do Azure com a CLI do Azure

O guia seguinte descreve os comandos comuns para automatizar a gestão das contas do Azure Cosmos DB, bases de dados e contentores com a CLI do Azure. As páginas de referência para todos os comandos da CLI do Azure Cosmos DB encontram-se disponíveis em [Referência da CLI do Azure](https://docs.microsoft.com/cli/azure/cosmosdb). Também pode encontrar mais exemplos [amostras de CLI do Azure para o Azure Cosmos DB](cli-samples.md), incluindo como criar e gerir contas do Cosmos DB, bases de dados e contentores para o MongoDB, Gremlin, Cassandra e API de tabela.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Para criar uma conta do Azure Cosmos DB com a API de SQL, a consistência da sessão nas regiões E.U.A. leste e E.U.A. oeste, execute o seguinte comando:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations EastUS=0 WestUS=1 \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> O nome de conta do Cosmos do Azure tem de estar em minúsculo.

## <a name="create-a-database"></a>Criar uma base de dados

Para criar uma base de dados do Cosmos DB, execute o seguinte comando:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Criar um contentor

Para criar um contentor do Cosmos DB com RU/s de 400 e uma chave de partição, execute o seguinte comando:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>Alterar o débito de um contentor

Para alterar o débito de um contentor do Cosmos DB para 1000 RU/s, execute o seguinte comando:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>Listar chaves de conta

Para obter as chaves para a sua conta do Cosmos, execute o seguinte comando:

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Lista cadeias de ligação

Para obter as cadeias de ligação para a sua conta do Cosmos, execute o seguinte comando:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Regenerar chave da conta

Para voltar a gerar uma nova chave primária para a sua conta do Cosmos, execute o seguinte comando:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, consulte:

- [Instalar a CLI do Azure](/cli/azure/install-azure-cli)
- [Referência do CLI do Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Exemplos da CLI do Azure adicionais para o Azure Cosmos DB](cli-samples.md)
