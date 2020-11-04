---
title: Gerir políticas de indexação no Azure Cosmos DB
description: Saiba como gerir políticas de indexação, incluir ou excluir uma propriedade da indexação, como definir a indexação usando diferentes SDKs Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 11/02/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: cd51210a64223fab5d2d48a91bd3d0a6521a9627
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341319"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gerir políticas de indexação no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Em Azure Cosmos DB, os dados são indexados seguindo [as políticas de indexação](index-policy.md) definidas para cada recipiente. A política de indexação predefinida para os contentores recém-criados impõe índices de intervalo para qualquer cadeia ou número. Esta política pode ser substituída pela sua própria política de indexação personalizada.

> [!NOTE]
> O método de atualização das políticas de indexação descritas neste artigo aplica-se apenas à API SQL (Core) da Azure Cosmos DB. Saiba mais sobre a indexação na [API da Azure Cosmos DB para a MongoDB](mongodb-indexing.md) e [a Indexação Secundária em Azure Cosmos DB Cassandra API.](cassandra-secondary-index.md)

## <a name="indexing-policy-examples"></a>Exemplos de política de indexação

Eis alguns exemplos de políticas de indexação mostradas no [seu formato JSON](index-policy.md#include-exclude-paths), que é como são expostas no portal Azure. Os mesmos parâmetros podem ser definidos através do CLI Azure ou de qualquer SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Política de opt-out para excluir seletivamente alguns caminhos de propriedade

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Esta política de indexação é equivalente à abaixo da que define manualmente ```kind``` , e aos seus ```dataType``` ```precision``` valores padrão. Estas propriedades já não são necessárias para definir explicitamente e você deve omiti-las inteiramente da sua política de indexação (como mostrado acima).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Política de opt-in para incluir seletivamente alguns caminhos de propriedade

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Esta política de indexação é equivalente à abaixo da que define manualmente ```kind``` , e aos seus ```dataType``` ```precision``` valores padrão. Estas propriedades já não são necessárias para definir explicitamente e você deve omiti-las inteiramente da sua política de indexação (como mostrado acima).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE]
> É geralmente recomendado usar uma política de indexação **de opt-out** para permitir que a Azure Cosmos DB indexe proativamente qualquer nova propriedade que possa ser adicionada ao seu modelo de dados.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Usando um índice espacial apenas num caminho de propriedade específico

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Exemplos de política de indexação compósita

Além de incluir ou excluir caminhos para propriedades individuais, também pode especificar um índice composto. Se você gostaria de realizar uma consulta que tenha uma `ORDER BY` cláusula para várias propriedades, é necessário um [índice composto](index-policy.md#composite-indexes) nessas propriedades. Além disso, os índices compostos terão um benefício de desempenho para consultas que tenham um filtro múltiplo ou um filtro e uma cláusula ORDER BY.

> [!NOTE]
> Os caminhos compostos têm um implícito, uma `/?` vez que apenas o valor escalar nesse caminho é indexado. O `/*` wildcard não é suportado em caminhos compostos. Não deve especificar `/?` ou `/*` num caminho composto.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Índice composto definido para (nome asc, idade desc):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

O índice composto acima referido sobre o nome e a idade é necessário para consultas #1 e #2 de consulta:

#1 de consulta:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

#2 de consulta:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Este índice composto beneficiará a #3 de consulta e a #4 de consulta e otimizará os filtros:

#3 de consulta:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

#4 de consulta:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Índice composto definido para (nome ASC, idade ASC) e (nome ASC, idade DESC):

Pode definir vários índices compostos diferentes dentro da mesma política de indexação.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Índice composto definido para (nome ASC, idade ASC):

É opcional especificar a encomenda. Se não for especificado, a ordem está a subir.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Excluindo todos os caminhos imobiliários, mas mantendo a indexação ativa

Esta política pode ser utilizada em situações em que a [funcionalidade Time-to-Live (TTL)](time-to-live.md) está ativa, mas não são necessários índices adicionais (para utilizar a Azure Cosmos DB como uma loja de valor-chave pura).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Sem indexação

Esta política vai desligar a indexação. Se `indexingMode` estiver `none` programado, não é possível definir um TTL no recipiente.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Atualização da política de indexação

Na Azure Cosmos DB, a política de indexação pode ser atualizada utilizando qualquer um dos métodos abaixo:

- do portal Azure
- usando o Azure CLI
- usando PowerShell
- usando um dos SDKs

Uma [atualização da política de indexação](index-policy.md#modifying-the-indexing-policy) desencadeia uma transformação de índice. O progresso desta transformação também pode ser acompanhado pelos SDKs.

> [!NOTE]
> Ao atualizar a política de indexação, escreve para a Azure Cosmos DB será ininterrupta. Saiba mais sobre [a indexação das transformações](index-policy.md#modifying-the-indexing-policy)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Os contentores Azure Cosmos armazenam a sua política de indexação como um documento JSON que o portal Azure permite editar diretamente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Crie uma nova conta Azure Cosmos ou selecione uma conta existente.

1. Abra o painel **do Data Explorer** e selecione o recipiente em que pretende trabalhar.

1. Clique em **Definições de & escala**.

1. Modificar a política de indexação JSON documento (ver exemplos [abaixo)](#indexing-policy-examples)

1. Clique em **Guardar** quando terminar.

:::image type="content" source="./media/how-to-manage-indexing-policy/indexing-policy-portal.png" alt-text="Gerir Indexação utilizando o portal Azure":::

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Para criar um recipiente com uma política de indexação personalizada ver, [crie um recipiente com uma política de índice personalizado usando CLI](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>Utilizar o PowerShell

Para criar um recipiente com uma política de indexação personalizada ver, [crie um recipiente com uma política de índice personalizado usando o PowerShell](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk"></a><a id="dotnet-sdk"></a> Utilize o .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

O `DocumentCollection` objeto do [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) expõe uma `IndexingPolicy` propriedade que permite alterar e adicionar ou remover e `IndexingMode` `IncludedPaths` `ExcludedPaths` .

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Para acompanhar o progresso da transformação do índice, passe um `RequestOptions` objeto que define a propriedade para `PopulateQuotaInfo` `true` .

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

O `ContainerProperties` objeto do [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (ver [este Quickstart](create-sql-api-dotnet.md) relativo à sua utilização) expõe uma `IndexingPolicy` propriedade que permite alterar e adicionar ou remover e `IndexingMode` `IncludedPaths` `ExcludedPaths` .

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Para acompanhar o progresso da transformação do índice, passe um `RequestOptions` objeto que define a propriedade `PopulateQuotaInfo` `true` para, em seguida, recupere o valor do cabeçalho de `x-ms-documentdb-collection-index-transformation-progress` resposta.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Ao definir uma política de indexação personalizada enquanto cria um novo recipiente, a fluente API do SDK V3 permite-lhe escrever esta definição de forma concisa e eficiente:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>Use o Java SDK

O `DocumentCollection` objeto do Java [SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (ver [este Quickstart](create-sql-api-java.md) no que diz respeito à sua utilização) expõe e `getIndexingPolicy()` `setIndexingPolicy()` métodos. O `IndexingPolicy` objeto que manipulam permite alterar o modo de indexação e adicionar ou remover caminhos incluídos e excluídos.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Para acompanhar o progresso da transformação do índice num contentor, passe um `RequestOptions` objeto que solicite a informação de quota para ser povoado e, em seguida, recupere o valor do cabeçalho de `x-ms-documentdb-collection-index-transformation-progress` resposta.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Use o Node.js SDK

A `ContainerDefinition` interface deNode.js [ SDK](https://www.npmjs.com/package/@azure/cosmos) (ver [este Quickstart](create-sql-api-nodejs.md) sobre a sua utilização) expõe uma `indexingPolicy` propriedade que permite alterar e adicionar ou remover e `indexingMode` `includedPaths` `excludedPaths` .

Recupere os detalhes do contentor

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Desa ajuste o modo de indexação para consistente

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Adicionar caminho incluído, incluindo um índice espacial

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Adicionar caminho excluído

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

Atualizar o recipiente com alterações

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Para acompanhar o progresso da transformação do índice num recipiente, passe um `RequestOptions` objeto que define a propriedade `populateQuotaInfo` `true` para, em seguida, recupere o valor do cabeçalho de `x-ms-documentdb-collection-index-transformation-progress` resposta.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Utilizar o SDK Python

# <a name="python-sdk-v3"></a>[Python SDK V3](#tab/pythonv3)

Ao utilizar o [Python SDK V3](https://pypi.org/project/azure-cosmos/) (ver [este Quickstart](create-sql-api-python.md) no que diz respeito à sua utilização), a configuração do recipiente é gerida como um dicionário. A partir deste dicionário, é possível aceder à política de indexação e a todos os seus atributos.

Recupere os detalhes do contentor

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Desa ajuste o modo de indexação para consistente

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Defina uma política de indexação com um caminho incluído e um índice espacial

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definir uma política de indexação com um caminho excluído

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Adicione um índice composto

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

Atualizar o recipiente com alterações

```python
response = client.ReplaceContainer(containerPath, container)
```

# <a name="python-sdk-v4"></a>[Python SDK V4](#tab/pythonv4)

Ao utilizar o [Python SDK V4,](https://pypi.org/project/azure-cosmos/)a configuração do recipiente é gerida como um dicionário. A partir deste dicionário, é possível aceder à política de indexação e a todos os seus atributos.

Recupere os detalhes do contentor

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Desa ajuste o modo de indexação para consistente

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Defina uma política de indexação com um caminho incluído e um índice espacial

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definir uma política de indexação com um caminho excluído

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Adicione um índice composto

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

Atualizar o recipiente com alterações

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

Recupere o progresso da transformação do índice a partir dos cabeçalhos de resposta
```python
container_client.read(populate_quota_info = True,
                      response_hook = lambda h,p: print(h['x-ms-documentdb-collection-index-transformation-progress']))
```

---

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre a indexação nos seguintes artigos:

- [Descrição geral da indexação](index-overview.md)
- [Política de indexação](index-policy.md)
