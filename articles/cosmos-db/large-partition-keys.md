---
title: Crie recipientes Azure Cosmos com chave de partição grande
description: Aprenda a criar um recipiente em Azure Cosmos DB com uma chave de partição grande usando o portal Azure e diferentes SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ad26d63ca06f5a46a4a1f77d329d04896e96c52
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339306"
---
# <a name="create-containers-with-large-partition-key"></a>Criar recipientes com chave de partição grande
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

AZure Cosmos DB usa um esquema de partição baseado em haxixe para alcançar a escala horizontal de dados. Todos os recipientes Azure Cosmos criados antes de 3 de maio de 2019 usam uma função de haxixe que calcula o haxixe com base nos primeiros 100 bytes da chave de partição. Se existem várias teclas de partição que têm os mesmos primeiros 100 bytes, então essas divisórias lógicas são consideradas como a mesma partição lógica pelo serviço. Isto pode levar a que questões como a quota de tamanho de partição sejam incorretas, e índices únicos sendo aplicados através das teclas de partição. As chaves de partição grandes são introduzidas para resolver este problema. A Azure Cosmos DB suporta agora grandes chaves de partição com valores até 2 KB.

As chaves de partição grandes são suportadas utilizando a funcionalidade de uma versão melhorada da função hash, que pode gerar um haxixe único a partir de chaves de partição grandes até 2 KB. Esta versão hash também é recomendada para cenários com cardealidade chave de partição alta, independentemente do tamanho da chave de partição. Uma cardinalidade-chave de partição é definida como o número de divisórias lógicas únicas, por exemplo na ordem de ~30000 divisórias lógicas num recipiente. Este artigo descreve como criar um recipiente com uma grande chave de partição usando o portal Azure e diferentes SDKs.

## <a name="create-a-large-partition-key-azure-portal"></a>Criar uma grande chave de partição (portal Azure)

Para criar uma chave de partição grande, quando criar um novo recipiente utilizando o portal Azure, verifique se a **tecla de partição My é maior do que a opção de 100 bytes.** Desescolhe a caixa de verificação se não precisar de chaves de partição grandes ou se tiver aplicações em execução na versão SDKs com mais de 1.18 anos.

:::image type="content" source="./media/large-partition-keys/large-partition-key-with-portal.png" alt-text="Criar chaves de partição grandes usando o portal Azure":::

## <a name="create-a-large-partition-key-powershell"></a>Criar uma grande chave de partição (PowerShell)

Para criar um recipiente com grande suporte de chaves de partição ver,

* [Crie um recipiente Azure Cosmos com um grande tamanho de chave de partição](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Criar uma grande chave de partição (.Net SDK)

Para criar um recipiente com uma chave de partição grande utilizando o .NET SDK, especifique a `PartitionKeyDefinitionVersion.V2` propriedade. O exemplo a seguir mostra como especificar a propriedade versão dentro do objeto PartitionKeyDefinition e defini-lo para PartitionKeyDefinitionVersion.V2.

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```
---

## <a name="supported-sdk-versions"></a>Versões SDK suportadas

As teclas de partição grandes são suportadas com as seguintes versões mínimas de SDKs:

|Tipo SDK  | Versão mínima   |
|---------|---------|
|.Net     |    1.18     |
|Sincronização de Java     |   2.4.0      |
|Java Async   |  2.5.0        |
| API REST | versão mais alta do que `2017-05-03` utilizando o `x-ms-version` cabeçalho de pedido.|
| Modelo do Resource Manager | versão 2 usando a `"version":2` propriedade dentro do `partitionKey` objeto. |

Atualmente, não é possível utilizar recipientes com chave de partição grande dentro de Power BI e Azure Logic Apps. Pode utilizar recipientes sem uma grande chave de partição destas aplicações.

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Trabalhar com uma conta do Azure Cosmos](./account-databases-containers-items.md)