---
title: Gerenciar políticas de indexação no Azure Cosmos DB
description: Saiba como gerenciar políticas de indexação no Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/27/2019
ms.author: thweiss
ms.openlocfilehash: 0100be7eeacdcda5b123356e95e2510a365d0f22
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356437"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gerenciar políticas de indexação no Azure Cosmos DB

No Azure Cosmos DB, os dados são indexados seguindo [políticas](index-policy.md) de indexação que são definidas para cada contêiner. A política de indexação padrão para contêineres recém-criados impõe índices de intervalo para qualquer cadeia de caracteres ou número e índices espaciais para qualquer objeto geojson do tipo Point. Essa política pode ser substituída:

- da portal do Azure
- Usando o CLI do Azure
- usando um dos SDKs

Uma [atualização de política](index-policy.md#modifying-the-indexing-policy) de indexação dispara uma transformação de índice. O progresso dessa transformação também pode ser acompanhado dos SDKs.

> [!NOTE]
> Como parte da atualização do SDK e do portal, estamos desenvolvendo a política de índice para se alinhar com um novo layout de índice que distribuímos para novos contêineres. Com esse novo layout, todos os tipos de dados primitivos são indexados como intervalo com precisão total (-1). Portanto, os tipos e a precisão de índice não são mais expostos ao usuário. No futuro, os usuários precisarão simplesmente adicionar caminhos à seção includedPaths e ignorar indexKinds e Precision. Essa alteração não tem impacto sobre o desempenho e você pode continuar a atualizar a política de indexação usando a mesma sintaxe. Você pode continuar a usar todos os exemplos em nossa documentação existente para atualizar a política de índice.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Os contêineres Cosmos do Azure armazenam sua política de indexação como um documento JSON que o portal do Azure permite editar diretamente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Crie uma nova conta do Azure Cosmos ou selecione uma conta existente.

1. Abra o painel de **Data Explorer** e selecione o contêiner no qual você deseja trabalhar.

1. Clique em **escala configurações de &** .

1. Modificar o documento JSON da política de indexação (veja os exemplos [abaixo](#indexing-policy-examples))

1. Clique em **salvar** quando terminar.

![Gerenciar a indexação usando portal do Azure](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

O comando [AZ cosmosdb Collection Update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) da CLI do Azure permite substituir a definição de JSON da política de indexação de um contêiner:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Usar o SDK do .NET v2

O `DocumentCollection` objeto do [SDK do .net v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (consulte [este guia de início rápido](create-sql-api-dotnet.md) sobre seu uso `IndexingPolicy` ) expõe uma propriedade que permite `IndexingMode` alterar e adicionar ou `IncludedPaths` remover `ExcludedPaths`e.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// set the indexing mode to Consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/headquarters/employees/?" });
// update the container with our changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Para acompanhar o progresso da transformação do índice, `RequestOptions` passe um objeto que `PopulateQuotaInfo` define a `true`Propriedade como.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Usar o SDK do Java

O `DocumentCollection` objeto do [SDK do Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (consulte este guia de [início rápido](create-sql-api-java.md) sobre seu `getIndexingPolicy()` uso `setIndexingPolicy()` ) expões e métodos. O `IndexingPolicy` objeto que ele manipula permite alterar o modo de indexação e adicionar ou remover caminhos incluídos e excluídos.

```java
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
    DocumentCollection container = result.getResource();
    IndexingPolicy indexingPolicy = container.getIndexingPolicy();
    // set the indexing mode to Consistent
    indexingPolicy.setIndexingMode(IndexingMode.Consistent);
    Collection<ExcludedPath> excludedPaths = new ArrayList<>();
    ExcludedPath excludedPath = new ExcludedPath();
    excludedPath.setPath("/*");
    // add an excluded path
    excludedPaths.add(excludedPath);
    indexingPolicy.setExcludedPaths(excludedPaths);
    // update the container with our changes
    client.replaceCollection(container, null);
});
```

Para acompanhar o progresso da transformação de índice em um contêiner, `RequestOptions` passe um objeto que solicita que as informações de cota sejam preenchidas e, em `x-ms-documentdb-collection-index-transformation-progress` seguida, recupere o valor do cabeçalho de resposta.

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

A `ContainerDefinition` interface do [SDK do node. js](https://www.npmjs.com/package/@azure/cosmos) (consulte este guia de [início rápido](create-sql-api-nodejs.md) sobre seu `indexingPolicy` uso) expõe uma propriedade que `indexingMode` permite alterar e adicionar `includedPaths` ou `excludedPaths`remover e.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read();
// set the indexing mode to Consistent
containerResponse.body.indexingPolicy.indexingMode = "consistent";
// add an excluded path
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/headquarters/employees/?' });
// update the container with our changes
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Para acompanhar o progresso da transformação do índice em um contêiner, `RequestOptions` passe um objeto que `populateQuotaInfo` define a `true`propriedade para e, em seguida, `x-ms-documentdb-collection-index-transformation-progress` recupere o valor do cabeçalho de resposta.

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

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [
    {"path": "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Exemplos de política de indexação

Aqui estão alguns exemplos de políticas de indexação mostradas em seu formato JSON, que é como elas são expostas no portal do Azure. Os mesmos parâmetros podem ser definidos por meio da CLI do Azure ou de qualquer SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Política de recusa para excluir seletivamente alguns caminhos de propriedade
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
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
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
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

Nota: Geralmente, é recomendável usar uma política de indexação de **recusa** para permitir que Azure Cosmos DB indexe proativamente qualquer nova propriedade que possa ser adicionada ao seu modelo.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Usando um índice espacial somente em um caminho de propriedade específico
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
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
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Excluindo todos os caminhos de propriedade, mas mantendo a indexação ativa

Essa política pode ser usada em situações em que o [recurso TTL (vida útil)](time-to-live.md) está ativo, mas nenhum índice secundário é necessário (para usar Azure Cosmos DB como um armazenamento de chave-valor puro).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Sem indexação
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Exemplos de política de indexação composta

Além de incluir ou excluir caminhos para propriedades individuais, você também pode especificar um índice composto. Se você quiser executar uma consulta que tenha uma `ORDER BY` cláusula para várias propriedades, um [índice composto](index-policy.md#composite-indexes) nessas propriedades será necessário.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Índice composto definido para (nome ASC, idade DESC):
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

Esse índice composto seria capaz de dar suporte às duas consultas a seguir:

#1 de consulta:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

#2 de consulta:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Índice composto definido para (nome ASC, idade ASC) e (nome ASC, idade DESC):

Você pode definir vários índices compostos diferentes na mesma política de indexação. 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre a indexação nos seguintes artigos:

- [Visão geral da indexação](index-overview.md)
- [Política de indexação](index-policy.md)
