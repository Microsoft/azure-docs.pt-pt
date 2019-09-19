---
title: Definir chaves exclusivas para um contêiner Cosmos do Azure
description: Saiba como definir chaves exclusivas para um contêiner Cosmos do Azure
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: thweiss
ms.openlocfilehash: 3b950565e0a44f979c11e3eb67b702c4dcb44769
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104897"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definir chaves exclusivas para um contêiner Cosmos do Azure

Este artigo apresenta as diferentes maneiras de definir [chaves exclusivas](unique-keys.md) ao criar um contêiner Cosmos do Azure. No momento, é possível executar essa operação usando o portal do Azure ou por meio de um dos SDKs.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma existente.

1. Abra o painel de **Data Explorer** e selecione o contêiner no qual você deseja trabalhar.

1. Clique em **novo contêiner**.

1. Na caixa de diálogo **Adicionar contêiner** , clique em **+ Adicionar chave exclusiva** para adicionar uma entrada de chave exclusiva.

1. Insira os caminhos da restrição de chave exclusiva

1. Se necessário, adicione mais entradas de chave exclusivas clicando em **+ Adicionar chave exclusiva**

![Captura de tela de entrada de restrição de chave exclusiva em portal do Azure](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-the-net-sdk-v2"></a>Usar o SDK do .NET v2

Ao criar um novo contêiner usando o [SDK do .net v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), `UniqueKeyPolicy` um objeto pode ser usado para definir restrições de chave exclusivas.

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

## <a name="use-the-net-sdk-v3"></a>Usar o SDK do .NET v3

Ao criar um novo contêiner usando o [SDK do .net v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), use a API Fluent do SDK para declarar chaves exclusivas de maneira concisa e legível.

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

## <a name="use-the-java-sdk"></a>Usar o SDK do Java

Ao criar um novo contêiner usando o [SDK do Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), `UniqueKeyPolicy` um objeto pode ser usado para definir restrições de chave exclusivas.

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

## <a name="use-the-nodejs-sdk"></a>Usar o SDK do node. js

Ao criar um novo contêiner usando o [SDK do node. js](https://www.npmjs.com/package/@azure/cosmos), `UniqueKeyPolicy` um objeto pode ser usado para definir restrições de chave exclusivas.

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

## <a name="use-the-python-sdk"></a>Usar o SDK do Python

Ao criar um novo contêiner usando o [SDK do Python](https://pypi.org/project/azure-cosmos/), as restrições de chave exclusivas podem ser especificadas como parte do dicionário passado como parâmetro.

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

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [particionamento](partition-data.md)
- Explore [como funciona a indexação](index-overview.md)