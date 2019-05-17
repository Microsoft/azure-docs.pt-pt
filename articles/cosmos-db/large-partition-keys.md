---
title: Crie contentores de Cosmos do Azure com a chave de partição de grandes dimensões com o portal do Azure e vários SDKs.
description: Saiba como criar um contentor no Azure Cosmos DB com a chave de partição de grandes dimensões com o portal do Azure e SDKs diferentes.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 322d5630daeedaa671f994b1374f15f655811de5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796093"
---
# <a name="create-containers-with-large-partition-key"></a>Criar contentores com a chave de partição de grandes dimensões

O Azure Cosmos DB utiliza o esquema de particionamento baseada em hash para alcançar dimensionamento horizontal dos dados. Todos os contentores do Azure Cosmos criados antes de 3 de Maio de 2019 utilizarem uma função de hash que calcula o hash com base nos primeiros 100 bytes da chave de partição. Se existirem várias chaves de partição que têm os mesmo primeiros 100 bytes, essas partições lógicas são consideradas como a mesma partição lógica pelo serviço. Isso pode causar problemas, como a quota de tamanho da partição a ser incorreto e índices exclusivos a ser aplicados nas chaves de partição. Chaves de partição de grandes dimensões são introduzidas para resolver este problema. O Azure Cosmos DB agora suporta chaves de partição de grandes dimensões com valores até 2 KB. 

Até 2 KB de chaves de partição de grandes dimensões, as chaves são suportadas com a funcionalidade de uma versão aprimorada da função de hash, que pode gerar um valor hash exclusivo da partição de grandes dimensões. Esta versão de hash também é recomendada para cenários com cardinalidade de chave de partição elevada, independentemente do tamanho da chave de partição. Uma cardinalidade de chave de partição é definida como o número de partições lógicas exclusivos, por exemplo, na ordem de ~ 30000 partições lógicas num contentor. Este artigo descreve como criar um contentor com uma chave de partição de grandes dimensões com o portal do Azure e SDKs diferentes. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Criar uma chave de partição de grandes dimensões (.Net SDK V2)

Ao utilizar o SDK do .net para criar um contentor com a chave de partição de grandes dimensões, deve especificar o `PartitionKeyDefinitionVersion.V2` propriedade. O exemplo seguinte mostra como especificar a propriedade de versão dentro do objeto PartitionKeyDefinition e defini-lo como PartitionKeyDefinitionVersion.V2:

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

## <a name="create-a-large-partition-key-azure-portal"></a>Criar uma chave de partição de grandes dimensões (portal do Azure) 

Para criar uma chave de partição de grandes dimensões, enquanto cria um novo contentor no portal do Azure, consulte a **minha chave de partição é maior do que 100 bytes** opção. Por predefinição, todos os novos contentores são optado por utilizar as chaves de partição de grandes dimensões. Desmarque a caixa de verificação se não precisar de chaves de partição de grandes dimensões ou se tiver aplicações em execução numa versão de SDKs mais antigo do que 1.18.

![Criar chaves de partição de grandes dimensões com o portal do Azure](./media/large-partition-keys/large-partition-key-with-portal.png)


## <a name="supported-sdk-versions"></a>Versões suportadas do SDK

As chaves de partição de grandes dimensões são suportadas com as seguintes versões mínimas SDKs:

|Tipo SDK  | Versão mínima   |
|---------|---------|
|.Net     |    1.18     |
|Sincronização de Java     |   2.4.0      |
|Java assíncrono   |  2.5.0        |
| API REST | versão superior `2017-05-03` utilizando o `x-ms-version` cabeçalho do pedido.|
 
## <a name="next-steps"></a>Passos Seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de pedido no Azure Cosmos DB](request-units.md)
* [Débito de aprovisionar em contentores e bases de dados](set-throughput.md)
* [Trabalhar com a conta do Cosmos do Azure](account-overview.md)


