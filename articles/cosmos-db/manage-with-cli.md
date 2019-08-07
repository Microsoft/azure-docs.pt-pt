---
title: Gerenciar Azure Cosmos DB recursos usando CLI do Azure
description: Use CLI do Azure para gerenciar sua conta do Azure Cosmos DB, banco de dados e contêineres.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 9ec049311fc158b13bba45deb2974d7cdd531f90
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815035"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Gerenciar recursos do cosmos do Azure usando o CLI do Azure

O guia a seguir descreve os comandos comuns para automatizar o gerenciamento de suas contas de Azure Cosmos DB, bancos de dados e contêineres usando CLI do Azure. As páginas de referência para todos os comandos da CLI do Azure Cosmos DB encontram-se disponíveis em [Referência da CLI do Azure](https://docs.microsoft.com/cli/azure/cosmosdb). Você também pode encontrar mais exemplos em [exemplos de CLI do Azure para Azure Cosmos DB](cli-samples.md), incluindo como criar e gerenciar contas de Cosmos DB, bancos de dados e contêineres para MongoDB, Gremlin, Cassandra e API de tabela.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Para criar uma conta de Azure Cosmos DB com a API do SQL, consistência de sessão nas regiões leste dos EUA e oeste dos EUA, execute o seguinte comando:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount # must be lowercase and < 31 characters \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> O nome da conta do Azure Cosmos deve estar em minúsculas.

## <a name="create-a-database"></a>Criar uma base de dados

Para criar um banco de dados Cosmos DB, execute o seguinte comando:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Criar um contentor

Para criar um contêiner de Cosmos DB com RU/s de 400 e uma chave de partição, execute o seguinte comando:

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

## <a name="change-the-throughput-of-a-container"></a>Alterar a taxa de transferência de um contêiner

Para alterar a taxa de transferência de um contêiner de Cosmos DB para 1000 RU/s, execute o seguinte comando:

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

Para obter as chaves para sua conta do cosmos, execute o seguinte comando:

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Listar cadeias de conexão

Para obter as cadeias de conexão para sua conta do cosmos, execute o seguinte comando:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Regenerar chave de conta

Para regenerar uma nova chave primária para sua conta do cosmos, execute o seguinte comando:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o CLI do Azure, consulte:

- [Instalar a CLI do Azure](/cli/azure/install-azure-cli)
- [Referência do CLI do Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Exemplos de CLI do Azure adicionais para Azure Cosmos DB](cli-samples.md)
