---
title: Crie recipientes Azure Cosmos com grande chave de partição
description: Aprenda a criar um recipiente em Azure Cosmos DB com grande chave de partição utilizando o portal Azure e diferentes SDKs.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 3b0a3387fe34a0e1c15109aec877c0654fc1a5b0
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82870004"
---
# <a name="create-containers-with-large-partition-key"></a>Criar recipientes com grande chave de partição

A Azure Cosmos DB utiliza um esquema de partição baseado em hash para alcançar a escala horizontal de dados. Todos os contentores Azure Cosmos criados antes de 3 de maio de 2019 usam uma função de haxixe que calcula haxixe com base nos primeiros 100 bytes da chave de partição. Se existem várias teclas de partição que têm os mesmos 100 primeiros bytes, então essas divisórias lógicas são consideradas como a mesma divisória lógica pelo serviço. Isto pode levar a que questões como a quota de tamanho da divisória sejam incorretas, e índices únicos sejam aplicados através das teclas de partição. São introduzidas chaves de partição grandes para resolver este problema. A Azure Cosmos DB suporta agora grandes teclas de partição com valores até 2 KB.

As grandes teclas de partição são suportadas utilizando a funcionalidade de uma versão melhorada da função hash, que pode gerar um hash único a partir de grandes teclas de partição até 2 KB. Esta versão hash também é recomendada para cenários com alta cardeal-chave de partição, independentemente do tamanho da chave de partição. Uma cardeal-chave de partição é definida como o número de divisórias lógicas únicas, por exemplo na ordem de ~30000 divisórias lógicas em um recipiente. Este artigo descreve como criar um recipiente com uma grande chave de partição usando o portal Azure e diferentes SDKs.

## <a name="create-a-large-partition-key-azure-portal"></a>Criar uma grande chave de partição (portal Azure)

Para criar uma grande chave de partição, quando criar um novo recipiente utilizando o portal Azure, verifique se a **chave de partição My é maior do que a opção de 100 bytes.** Desmarque a caixa de verificação se não precisar de chaves de partição grandes ou se tiver aplicações em execução na versão SDKs com mais de 1.18.

![Crie grandes chaves de partição usando o portal Azure](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Criar uma grande chave de partição (PowerShell)

Para criar um recipiente com grande suporte à chave de divisória, consulte,

* [Crie um recipiente Azure Cosmos com uma grande chave de partição](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Criar uma grande chave de partição (.Net SDK)

Para criar um recipiente com uma grande chave de partição utilizando o .NET SDK, especifique a `PartitionKeyDefinitionVersion.V2` propriedade. O exemplo seguinte mostra como especificar a propriedade versão dentro do objeto PartitionKeyDefinition e defini-la para PartitionKeyDefinitionVersion.V2.

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

As teclas de partição Grandes são suportadas com as seguintes versões mínimas de SDKs:

|Tipo SDK  | Versão mínima   |
|---------|---------|
|.Net     |    1.18     |
|Sincronização java     |   2.4.0      |
|Java Async   |  2.5.0        |
| API REST | versão superior `2017-05-03` ao `x-ms-version` usando o cabeçalho de pedido.|
| Modelo do Resource Manager | versão 2 usando `"version":2` a `partitionKey` propriedade dentro do objeto. |

Atualmente, não é possível utilizar recipientes com grande chave de partição dentro de Aplicações lógicas Power BI e Azure. Pode utilizar recipientes sem uma grande chave de partição destas aplicações.

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Request Units in Azure Cosmos DB](request-units.md) (Unidades de Pedido no Azure Cosmos DB)
* [Aprovisionar débito em contentores e bases de dados](set-throughput.md)
* [Trabalhar com uma conta do Azure Cosmos](account-overview.md)
