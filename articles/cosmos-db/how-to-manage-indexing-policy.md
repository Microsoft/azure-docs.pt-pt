---
title: Gerir políticas de indexação em Azure Cosmos DB
description: Saiba como gerir políticas de indexação, incluir ou excluir uma propriedade de indexação, como definir indexação usando diferentes SDKs DDKs DDKs Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: 171542c89b900eb8bf282156c79303a1deb5a5f7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364524"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gerir políticas de indexação em Azure Cosmos DB

No Azure Cosmos DB, os dados são indexados seguindo as políticas de [indexação](index-policy.md) que são definidas para cada recipiente. A política de indexação predefinida para os contentores recém-criados impõe índices de intervalo para qualquer cadeia ou número. Esta política pode ser substituída pela sua própria política de indexação personalizada.

## <a name="indexing-policy-examples"></a>Exemplos de política de indexação

Eis alguns exemplos de políticas de indexação mostradas no seu formato JSON, que é como são expostas no portal Azure. Os mesmos parâmetros podem ser definidos através do Azure CLI ou de qualquer SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Política de opt-out para excluir seletivamente alguns caminhos imobiliários

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

Esta política de indexação é equivalente à abaixo da qual se define manualmente ```kind```, ```dataType```e ```precision``` aos seus valores predefinidos. Estas propriedades já não são necessárias para definir explicitamente e pode omiti-las inteiramente da sua política de indexação (como mostra o exemplo acima).

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

Esta política de indexação é equivalente à abaixo da qual se define manualmente ```kind```, ```dataType```e ```precision``` aos seus valores predefinidos. Estas propriedades já não são necessárias para definir explicitamente e pode omiti-las inteiramente da sua política de indexação (como mostra o exemplo acima).

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
> É geralmente recomendado usar uma política de indexação **de opt-out** para permitir que o Azure Cosmos DB indexe proativamente qualquer nova propriedade que possa ser adicionada ao seu modelo.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Usando um índice espacial apenas em um caminho de propriedade específico

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

## <a name="composite-indexing-policy-examples"></a>Exemplos de política de indexação compósita

Além de incluir ou excluir caminhos para propriedades individuais, também pode especificar um índice composto. Se você gostaria de realizar uma consulta que tem uma cláusula `ORDER BY` para várias propriedades, é necessário um [índice composto](index-policy.md#composite-indexes) sobre essas propriedades. Além disso, os índices compósitos terão um benefício de desempenho para consultas que têm um filtro e têm uma cláusula ORDER BY em diferentes propriedades.

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

O índice composto acima referido sobre o nome e a idade é necessário para consulta #1 e consulta #2:

Consulta #1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

Consulta #2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Este índice composto beneficiará a Consulta #3 e Consulta #4 e otimizará os filtros:

Consulta #3:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

Consulta #4:

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

É opcional especificar a encomenda. Se não especificada, a ordem é ascendente.

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

Esta política pode ser utilizada em situações em que a [funcionalidade Time-to-Live (TTL)](time-to-live.md) está ativa, mas não é necessário um índice secundário (para utilizar o Azure Cosmos DB como uma loja de valor-chave puro).

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

Esta política vai desligar a indexação. Se `indexingMode` estiver programado para `none`, não pode colocar um TTL no recipiente.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Atualizar a política de indexação

Em Azure Cosmos DB, a política de indexação pode ser atualizada utilizando qualquer um dos métodos abaixo:

- do portal Azure
- utilizando o Azure CLI
- usando powerShell
- usando um dos SDKs

Uma [atualização de política de indexação](index-policy.md#modifying-the-indexing-policy) desencadeia uma transformação de índice. O progresso desta transformação também pode ser rastreado a partir dos SDKs.

> [!NOTE]
> Ao atualizar a política de indexação, escreve a Azure Cosmos DB será ininterrupta. Durante a reindexação, as consultas podem devolver resultados parciais à medida que o índice está a ser atualizado.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Os contentores Azure Cosmos armazenam a sua política de indexação como um documento JSON que o portal Azure permite editar diretamente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Crie uma nova conta Azure Cosmos ou selecione uma conta existente.

1. Abra o painel **do Data Explorer** e selecione o recipiente em que pretende trabalhar.

1. Clique em **Escala e Definições**.

1. Modificar o documento json da política de indexação (ver exemplos [abaixo)](#indexing-policy-examples)

1. Clique em **Guardar** quando terminar.

![Gerir indexação utilizando o portal Azure](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Para criar um recipiente com uma política de indexação personalizada ver, [Crie um recipiente com uma política](manage-with-cli.md#create-a-container-with-a-custom-index-policy) de índice personalizado usando cli

## <a name="use-powershell"></a>Utilizar o PowerShell

Para criar um recipiente com uma política de indexação personalizada ver, [Crie um recipiente com uma política](manage-with-powershell.md#create-container-custom-index) de índice personalizado usando powershell

## <a name="use-the-net-sdk-v2"></a>Utilize o .NET SDK V2

O objeto `DocumentCollection` do [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) expõe uma propriedade `IndexingPolicy` que permite alterar a `IndexingMode` e adicionar ou remover `IncludedPaths` e `ExcludedPaths`.

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

Para acompanhar o progresso da transformação do índice, passe um objeto `RequestOptions` que define a propriedade `PopulateQuotaInfo` para `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-net-sdk-v3"></a>Utilize o .NET SDK V3

O objeto `ContainerProperties` do [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (ver [este Quickstart](create-sql-api-dotnet.md) em relação à sua utilização) expõe uma propriedade `IndexingPolicy` que permite alterar a `IndexingMode` e adicionar ou remover `IncludedPaths` e `ExcludedPaths`.

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

Para acompanhar o progresso da transformação do índice, passe um objeto `RequestOptions` que define a propriedade `PopulateQuotaInfo` para `true`, em seguida, recuperar o valor do cabeçalho de resposta `x-ms-documentdb-collection-index-transformation-progress`.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Ao definir uma política de indexação personalizada ao criar um novo recipiente, a Fluente API do SDK V3 permite-lhe escrever esta definição de forma concisa e eficiente:

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

## <a name="use-the-java-sdk"></a>Use o Java SDK

O `DocumentCollection` objeto do [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (ver [este Quickstart](create-sql-api-java.md) em relação à sua utilização) expõe `getIndexingPolicy()` e métodos `setIndexingPolicy()`. O `IndexingPolicy` objeto que manipulam permite alterar o modo de indexação e adicionar ou remover caminhos incluídos e excluídos.

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

Para acompanhar o progresso da transformação do índice num contentor, passe um objeto `RequestOptions` que solicite que a informação de quota seja povoada e, em seguida, recupere o valor do cabeçalho de resposta `x-ms-documentdb-collection-index-transformation-progress`.

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

## <a name="use-the-nodejs-sdk"></a>Use o Nó.js SDK

A interface `ContainerDefinition` do [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (ver [este Quickstart](create-sql-api-nodejs.md) em relação à sua utilização) expõe uma propriedade `indexingPolicy` que permite alterar a `indexingMode` e adicionar ou remover `includedPaths` e `excludedPaths`.

Recupere os detalhes do contentor

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Desajuste o modo de indexação para consistente

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Adicione o caminho incluído, incluindo um índice espacial

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

Para acompanhar o progresso da transformação do índice num contentor, passe um objeto `RequestOptions` que coloque a propriedade `populateQuotaInfo` para `true`, em seguida, recupere o valor do cabeçalho de resposta `x-ms-documentdb-collection-index-transformation-progress`.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk-v3"></a>Use o Python SDK V3

Ao utilizar o [Python SDK V3](https://pypi.org/project/azure-cosmos/) (ver [este Quickstart](create-sql-api-python.md) em relação à sua utilização), a configuração do recipiente é gerida como um dicionário. A partir deste dicionário, é possível aceder à política de indexação e a todos os seus atributos.

Recupere os detalhes do contentor

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Desajuste o modo de indexação para consistente

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

## <a name="use-the-python-sdk-v4"></a>Use o Python SDK V4

Ao utilizar o [Python SDK V4,](https://pypi.org/project/azure-cosmos/)a configuração do recipiente é gerida como um dicionário. A partir deste dicionário, é possível aceder à política de indexação e a todos os seus atributos.

Recupere os detalhes do contentor

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Desajuste o modo de indexação para consistente

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

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre a indexação nos seguintes artigos:

- [Visão geral de indexação](index-overview.md)
- [Política de indexação](index-policy.md)
