---
title: Defina chaves únicas para um recipiente Azure Cosmos
description: Aprenda a definir chaves únicas para um recipiente Azure Cosmos usando o portal Azure, PowerShell, .NET, Java e vários outros SDKs.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/02/2019
ms.author: thweiss
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 55fc5222c1c245c56ba0a26caa816c5c845147c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336627"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Defina chaves únicas para um recipiente Azure Cosmos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo apresenta as diferentes formas de definir [chaves únicas](unique-keys.md) ao criar um recipiente Azure Cosmos. Atualmente é possível realizar esta operação, quer utilizando o portal Azure, quer através de um dos SDKs.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma já existente.

1. Abra o painel **do Data Explorer** e selecione o recipiente em que pretende trabalhar.

1. Clique em **Novo Recipiente**.

1. No diálogo **'Adicionar recipiente',** clique em **+ Adicione uma tecla única** para adicionar uma entrada única na chave.

1. Insira o(s) caminho(s) do constrangimento chave único

1. Se necessário, adicione mais entradas chave únicas clicando em **+ Adicionar chave única**

    :::image type="content" source="./media/how-to-define-unique-keys/unique-keys-portal.png" alt-text="Screenshot da entrada única de restrição de chave no portal Azure":::

## <a name="use-powershell"></a>Utilizar o PowerShell

Para criar um recipiente com chaves únicas ver, [Crie um recipiente Azure Cosmos com chave única e TTL](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Ao criar um novo recipiente utilizando o [.NET SDK v2,](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)pode ser utilizado um `UniqueKeyPolicy` objeto para definir restrições únicas de chaves.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Ao criar um novo recipiente utilizando o [.NET SDK v3,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)utilize a fluente API da SDK para declarar chaves únicas de forma concisa e legível.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>Use o Java SDK

Ao criar um novo recipiente utilizando o [Java SDK,](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) `UniqueKeyPolicy` um objeto pode ser usado para definir restrições únicas de chaves.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Use o Node.js SDK

Ao criar um novo recipiente utilizando o [Node.js SDK,](https://www.npmjs.com/package/@azure/cosmos)pode ser utilizado um `UniqueKeyPolicy` objeto para definir restrições únicas.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Utilizar o SDK Python

Ao criar um novo recipiente utilizando o [Python SDK,](https://pypi.org/project/azure-cosmos/)os principais constrangimentos exclusivos podem ser especificados como parte do dicionário passado como parâmetro.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [partição](partitioning-overview.md)
- Explore [como funciona a indexação](index-overview.md)
