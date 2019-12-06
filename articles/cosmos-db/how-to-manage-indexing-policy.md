---
title: Gerenciar políticas de indexação no Azure Cosmos DB
description: Saiba como gerenciar políticas de indexação, incluir ou excluir uma propriedade da indexação, como definir a indexação usando diferentes SDKs de Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: 3b98975df194af4625087e1beb556efb2a347f43
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872065"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gerenciar políticas de indexação no Azure Cosmos DB

No Azure Cosmos DB, os dados são indexados seguindo [políticas de indexação](index-policy.md) que são definidas para cada contêiner. A política de indexação predefinida para os contentores recém-criados impõe índices de intervalo para qualquer cadeia ou número. Esta política pode ser substituída pela sua própria política de indexação personalizada.

## <a name="indexing-policy-examples"></a>Exemplos de política de indexação

Aqui estão alguns exemplos de políticas de indexação mostradas em seu formato JSON, que é como elas são expostas no portal do Azure. Os mesmos parâmetros podem ser definidos por meio da CLI do Azure ou de qualquer SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Política de recusa para excluir seletivamente alguns caminhos de propriedade

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

Essa política de indexação é equivalente à seguinte, que define manualmente ```kind```, ```dataType```e ```precision``` aos seus valores padrão. Essas propriedades não são mais necessárias para definir explicitamente e você pode omiti-las de sua política de indexação totalmente (como mostrado no exemplo acima).

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

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Política de aceitação para incluir seletivamente alguns caminhos de propriedade

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

Essa política de indexação é equivalente à seguinte, que define manualmente ```kind```, ```dataType```e ```precision``` aos seus valores padrão. Essas propriedades não são mais necessárias para definir explicitamente e você pode omiti-las de sua política de indexação totalmente (como mostrado no exemplo acima).

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
> Geralmente, é recomendável usar uma política de indexação de **recusa** para permitir que Azure Cosmos DB indexe proativamente qualquer nova propriedade que possa ser adicionada ao seu modelo.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Usando um índice espacial somente em um caminho de propriedade específico

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
            "path": "/\"_etag\"/?"
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

## <a name="composite-indexing-policy-examples"></a>Exemplos de política de indexação composta

Além de incluir ou excluir caminhos para propriedades individuais, você também pode especificar um índice composto. Se você quiser executar uma consulta que tenha uma cláusula `ORDER BY` para várias propriedades, um [índice composto](index-policy.md#composite-indexes) nessas propriedades será necessário. Além disso, os índices compostos terão um benefício de desempenho para consultas que têm um filtro e têm uma cláusula ORDER BY em propriedades diferentes.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Índice composto definido para (nome ASC, idade DESC):

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

O índice composto acima em nome e idade é necessário para consulta #1 e #2 de consulta:

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

Esse índice composto beneficiará a consulta #3 e #4 de consulta e otimizará os filtros:

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

Você pode definir vários índices compostos diferentes na mesma política de indexação.

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

É opcional especificar a ordem. Se não for especificado, a ordem será crescente.

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

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Excluindo todos os caminhos de propriedade, mas mantendo a indexação ativa

Essa política pode ser usada em situações em que o [recurso TTL (vida útil)](time-to-live.md) está ativo, mas nenhum índice secundário é necessário (para usar Azure Cosmos DB como um armazenamento de chave-valor puro).

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

Esta política desativará a indexação. Se `indexingMode` for definido como `none`, você não poderá definir um TTL no contêiner.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Atualizando política de indexação

No Azure Cosmos DB, a política de indexação pode ser atualizada usando qualquer um dos métodos abaixo:

- Da portal do Azure
- usando o CLI do Azure
- usando o PowerShell
- usando um dos SDKs

Uma [atualização de política de indexação](index-policy.md#modifying-the-indexing-policy) dispara uma transformação de índice. O progresso dessa transformação também pode ser acompanhado dos SDKs.

> [!NOTE]
> Ao atualizar a política de indexação, as gravações para Azure Cosmos DB não serão interrompidas. Durante a reindexação, as consultas podem retornar resultados parciais à medida que o índice está sendo atualizado.

## <a name="use-the-azure-portal"></a>Utilizar o Portal do Azure

Os contêineres Cosmos do Azure armazenam sua política de indexação como um documento JSON que o portal do Azure permite editar diretamente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Crie uma nova conta do Azure Cosmos ou selecione uma conta existente.

1. Abra o painel de **Data Explorer** e selecione o contêiner no qual você deseja trabalhar.

1. Clique em **escala configurações de &** .

1. Modificar o documento JSON da política de indexação (veja os exemplos [abaixo](#indexing-policy-examples))

1. Clique em **Guardar** quando terminar.

![Gerenciar a indexação usando portal do Azure](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Para criar um contêiner com uma política de indexação personalizada, consulte [criar um contêiner com uma política de índice personalizada usando a CLI](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>Utilizar o PowerShell

Para criar um contêiner com uma política de indexação personalizada, consulte [criar um contêiner com uma política de índice personalizada usando o PowerShell](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk-v2"></a>Usar o SDK do .NET v2

O objeto `DocumentCollection` do [SDK do .net v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) expõe uma propriedade `IndexingPolicy` que permite alterar a `IndexingMode` e adicionar ou remover `IncludedPaths` e `ExcludedPaths`.

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

Para acompanhar o progresso da transformação do índice, passe um objeto `RequestOptions` que define a propriedade `PopulateQuotaInfo` como `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-net-sdk-v3"></a>Usar o SDK do .NET v3

O objeto `ContainerProperties` do [SDK do .net v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (consulte [este guia de início rápido](create-sql-api-dotnet.md) sobre seu uso) expõe uma propriedade `IndexingPolicy` que permite alterar a `IndexingMode` e adicionar ou remover `IncludedPaths` e `ExcludedPaths`.

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

Para acompanhar o progresso da transformação do índice, passe um objeto `RequestOptions` que define a propriedade `PopulateQuotaInfo` como `true`e, em seguida, recupere o valor do cabeçalho de resposta `x-ms-documentdb-collection-index-transformation-progress`.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Ao definir uma política de indexação personalizada ao criar um novo contêiner, a API Fluent V3's do SDK permite que você escreva essa definição de maneira concisa e eficiente:

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

## <a name="use-the-java-sdk"></a>Usar o SDK do Java

O objeto `DocumentCollection` do [SDK do Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (consulte este guia de [início rápido](create-sql-api-java.md) sobre seu uso) expõe os métodos `getIndexingPolicy()` e `setIndexingPolicy()`. O objeto `IndexingPolicy` que eles manipulam permite alterar o modo de indexação e adicionar ou remover caminhos incluídos e excluídos.

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
ExcludedPath includedPath = new IncludedPath();
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

Para acompanhar o progresso da transformação de índice em um contêiner, passe um objeto `RequestOptions` que solicita que as informações de cota sejam preenchidas e, em seguida, recupere o valor do cabeçalho de resposta `x-ms-documentdb-collection-index-transformation-progress`.

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

## <a name="use-the-nodejs-sdk"></a>Usar o SDK do node. js

A interface `ContainerDefinition` do [SDK do node. js](https://www.npmjs.com/package/@azure/cosmos) (consulte este guia de [início rápido](create-sql-api-nodejs.md) sobre seu uso) expõe uma propriedade `indexingPolicy` que permite alterar a `indexingMode` e adicionar ou remover `includedPaths` e `excludedPaths`.

Recuperar os detalhes do contêiner

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Definir o modo de indexação como consistente

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

Atualizar o contêiner com alterações

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Para acompanhar o progresso da transformação do índice em um contêiner, passe um objeto `RequestOptions` que define a propriedade `populateQuotaInfo` como `true`e, em seguida, recupere o valor do cabeçalho de resposta `x-ms-documentdb-collection-index-transformation-progress`.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Usar o SDK do Python

Ao usar o [SDK do Python](https://pypi.org/project/azure-cosmos/) (consulte este guia de [início rápido](create-sql-api-python.md) sobre seu uso), a configuração do contêiner é gerenciada como um dicionário. Nesse dicionário, é possível acessar a política de indexação e todos os seus atributos.

Recuperar os detalhes do contêiner

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Definir o modo de indexação como consistente

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Definir uma política de indexação com um caminho incluído e um índice espacial

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

Adicionar um índice composto

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

Atualizar o contêiner com alterações

```python
response = client.ReplaceContainer(containerPath, container)
```

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre a indexação nos seguintes artigos:

- [Visão geral da indexação](index-overview.md)
- [Política de indexação](index-policy.md)
