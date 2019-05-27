---
title: Definir chaves exclusivas para um contentor do Cosmos do Azure
description: Saiba como definir as chaves exclusivas para um contentor do Cosmos do Azure
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: fb9872d2fd41066899ff9198915d573bfb4a0b84
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240978"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definir chaves exclusivas para um contentor do Cosmos do Azure

Este artigo apresenta as diferentes formas de definir [chaves exclusivas](unique-keys.md) ao criar um contentor do Cosmos do Azure. É atualmente possível efetuar esta operação com o portal do Azure ou através de um dos SDKs.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione um existente.

1. Abra o **Data Explorer** painel e selecione o contentor que deseja trabalhar.

1. Clique em **novo contentor**.

1. Na **adicionar contentor** caixa de diálogo, clique em **+ Adicionar chave exclusiva** para adicionar uma entrada de chave exclusiva.

1. Introduza o caminho da restrição de chave exclusiva (s)

1. Se for necessário, adicione mais entradas de chaves exclusivas ao clicar no **+ Adicionar chave exclusiva**

![Captura de ecrã da entrada de restrição de chave exclusiva no portal do Azure](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-the-net-sdk-v2"></a>Utilizar o V2 do SDK de .NET

Ao criar um novo contentor utilizando o [v2 do SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), um `UniqueKeyPolicy` objeto pode ser usado para definir restrições de chave exclusivas.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-java-sdk"></a>Utilizar o Java SDK

Ao criar um novo contentor utilizando o [SDK de Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), um `UniqueKeyPolicy` objeto pode ser usado para definir restrições de chave exclusivas.

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

## <a name="use-the-nodejs-sdk"></a>Utilizar o SDK de node. js

Ao criar um novo contentor utilizando o [SDK node. js](https://www.npmjs.com/package/@azure/cosmos), um `UniqueKeyPolicy` objeto pode ser usado para definir restrições de chave exclusivas.

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

Ao criar um novo contentor utilizando o [SDK de Python](https://pypi.org/project/azure-cosmos/), restrições de chave exclusivas que podem ser especificadas como parte do dicionário passado como parâmetro.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            { 'paths': ['/firstName', '/lastName', '/emailAddress'] },
            { 'paths': ['/address/zipCode'] }
        ]
    }
})
```

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [criação de partições](partition-data.md)
- Explore [como funciona a indexação](index-overview.md)