---
title: Gerir políticas de indexação no Azure Cosmos DB
description: Saiba como gerir políticas de indexação no Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 05fd369cfebba03c814507f82755fa6cb6a89400
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386804"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gerir políticas de indexação no Azure Cosmos DB

No Azure Cosmos DB, os dados forem indexados seguindo [políticas de indexação](index-policy.md) que estão definidas para cada contentor. A predefinição de política para contentores recém-criado de indexação impõe índices de intervalo para qualquer cadeia de caracteres ou número e os índices espaciais para qualquer objeto GeoJSON do tipo Point. Esta política pode ser substituída:

- No portal do Azure
- Com a CLI do Azure
- através de um dos SDKs

Uma [atualização da política de indexação](index-policy.md#modifying-the-indexing-policy) aciona uma transformação de índice. O progresso dessa transformação também pode ser controlado dos SDKs.

> [!NOTE]
> Como parte da atualização do SDK e o Portal, estamos a desenvolver a política de índice para alinhar com um novo esquema de índice, que podemos ter implementadas em novos contentores. Com este novo esquema, todos os tipos de dados primitivos são indexados como intervalo com total precisão (-1). Por conseguinte, os tipos de índice e a precisão não são expostos ao utilizador deixa de poder. No futuro, os utilizadores terão simplesmente adicionar caminhos para a secção de includedPaths e ignorar indexKinds e precisão. Esta alteração não tem qualquer impacto no desempenho e pode continuar a atualizar a política de indexação com a mesma sintaxe. Pode continuar a utilizar todos os exemplos na documentação do existente ao atualizar a política de índice.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Contentores do Azure Cosmos armazenam sua política de indexação, como um documento JSON que o portal do Azure permite-lhe editar diretamente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Criar uma nova conta do Cosmos do Azure ou selecione uma conta existente.

1. Abra o **Data Explorer** painel e selecione o contentor que deseja trabalhar.

1. Clique em **dimensionamento e definições**.

1. Modificar o documento JSON de política indexação (veja exemplos [abaixo](#indexing-policy-examples))

1. Clique em **guardar** quando tiver terminado.

![Gerir a indexação com o portal do Azure](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

O [atualização da coleção az cosmosdb](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) comando a partir da CLI do Azure permite-lhe substituir a definição JSON de política de indexação de um contentor:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Utilizar o V2 do SDK de .NET

O `DocumentCollection` de objeto do [v2 do SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (consulte [neste início rápido](create-sql-api-dotnet.md) em relação à sua utilização) expõe um `IndexingPolicy` propriedade que lhe permite alterar o `IndexingMode` e adicionar ou remover `IncludedPaths` e `ExcludedPaths`.

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

Para acompanhar o progresso de transformação de índice, passe um `RequestOptions` objeto que define o `PopulateQuotaInfo` propriedade `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Utilizar o Java SDK

O `DocumentCollection` de objeto do [SDK do Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (consulte [neste início rápido](create-sql-api-java.md) em relação à sua utilização) expõe `getIndexingPolicy()` e `setIndexingPolicy()` métodos. O `IndexingPolicy` de objeto que manipulam permite-lhe alterar o modo de indexação e adiciona ou remover incluídos e excluídos caminhos.

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

Para acompanhar o progresso de transformação de índice num contentor, passe um `RequestOptions` objeto que solicita as informações de quota para ser preenchido, em seguida, recupere o valor da `x-ms-documentdb-collection-index-transformation-progress` cabeçalho de resposta.

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

## <a name="use-the-nodejs-sdk"></a>Utilizar o SDK de node. js

O `ContainerDefinition` de interface do [SDK node. js](https://www.npmjs.com/package/@azure/cosmos) (consulte [neste início rápido](create-sql-api-nodejs.md) em relação à sua utilização) expõe um `indexingPolicy` propriedade que lhe permite alterar o `indexingMode` e adicionar ou remover `includedPaths` e `excludedPaths`.

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

Para acompanhar o progresso de transformação de índice num contentor, passe um `RequestOptions` objeto que define o `populateQuotaInfo` propriedade para `true`, em seguida, recupere o valor da `x-ms-documentdb-collection-index-transformation-progress` cabeçalho de resposta.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Utilizar o SDK Python

Ao utilizar o [SDK de Python](https://pypi.org/project/azure-cosmos/) (consulte [neste início rápido](create-sql-api-python.md) em relação à sua utilização), a configuração do contentor é gerenciada como um dicionário. De nesse dicionário, é possível acessar a política de indexação e todos os seus atributos.

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [{"path" : "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Exemplos de política de indexação

Aqui estão alguns exemplos de políticas de indexação apresentadas no seu formato JSON, que é a forma como elas são expostas no portal do Azure. Os mesmos parâmetros podem ser definidos através da CLI do Azure ou qualquer SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Política de sair para seletivamente excluir alguns caminhos de propriedade
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

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Política de optar por incluir seletivamente alguns caminhos de propriedade
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

Nota: Em geral, é recomendado utilizar um **sair** política para permitir que o Azure Cosmos DB proativamente de indexação de índice qualquer nova propriedade, que pode ser adicionada ao seu modelo.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Usando um índice espacial em apenas um caminho de propriedade específica
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

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Excluir todos os caminhos de propriedade, mas mantendo a indexação do Active Directory

Esta política pode ser usada em situações em que o [Time-to-Live (TTL) funcionalidade](time-to-live.md) é Active Directory, mas não secundário índice é necessário (para utilizar o Azure Cosmos DB como um arquivo de chave-valor puro).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Não existem indexação
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Exemplos de política de indexação compostos

Além de incluindo ou excluindo os caminhos para propriedades individuais, também pode especificar um índice composto. Se quiser executar uma consulta que tem um `ORDER BY` cláusula de várias propriedades, uma [índice composto](index-policy.md#composite-indexes) sobre essas propriedades é necessária.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Índice composto definido para (asc nome, idade desc):
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

Este índice composto deve ser capaz de dar suporte as duas consultas seguintes:

Consulta de #1:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

Consulta de #2:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Índice composto definido para (asc nome, idade asc) e (nome asc, idade desc):

Pode definir vários índices compostos diferentes na mesma política de indexação. 
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
            ]
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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Índice composto definido para (asc nome, idade asc):

É opcional especificar a ordem. Se não for especificado, a ordem é ascendente.
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

- [Descrição geral de indexação](index-overview.md)
- [Política de indexação](index-policy.md)
