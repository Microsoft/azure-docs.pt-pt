---
title: Dados geoespaciais de índice com Azure Cosmos DB
description: Dados espaciais de índice com Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/03/2020
ms.author: tisande
ms.openlocfilehash: f250c15dbb30736e3e89a301fc236a848bd05da2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092063"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Dados geoespaciais de índice com Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Desenhamos o motor de base de dados da Azure Cosmos DB para ser verdadeiramente agnóstico e fornecer suporte de primeira classe para o JSON. O motor de base de dados otimizado da Azure Cosmos DB compreende de forma nativa os dados espaciais representados na norma GeoJSON.

Em resumo, a geometria é projetada a partir de coordenadas geodésicas para um plano 2D então dividido progressivamente em células usando uma **árvore quadtree** . Estas células são mapeadas para 1D com base na localização da célula dentro de uma curva de **enchimento de espaço Hilbert** , que preserva a localidade dos pontos. Adicionalmente, quando os dados de localização são indexados, passa por um processo conhecido como **tessellation** , isto é, todas as células que intersectam uma localização são identificadas e armazenadas como chaves no índice DB do Azure Cosmos. No momento da consulta, argumentos como pontos e polígonos também são tessellados para extrair as gamas de ID celular relevantes, então usados para obter dados do índice.

Se especificar uma política de indexação que inclua índice espacial para /* (todos os caminhos), todos os dados encontrados dentro do recipiente são indexados para consultas espaciais eficientes.

> [!NOTE]
> Azure Cosmos DB suporta a indexação de pontos, LineStrings, Polígonos e MultiPolígonos
>
>

## <a name="modifying-geospatial-data-type"></a>Modificação do tipo de dados geoespaciais

No seu recipiente, a **Configuração Geoespacial** especifica como os dados espaciais serão indexados. Especifique uma **Configuração Geoespacial** por recipiente: geografia ou geometria.

Pode alternar entre o tipo espacial **de geografia** e **geometria** no portal Azure. É importante que crie uma [política de indexação de geometria espacial válida com uma caixa de delimitação](#geometry-data-indexing-examples) antes de mudar para o tipo espacial de geometria.

Eis como definir a **Configuração Geoespacial** no **Data Explorer** dentro do portal Azure:

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="Definição de configuração geoespacial":::

Também pode modificar `geospatialConfig` o SDK .NET para ajustar a **Configuração Geoespacial:**

Se não for especificado, o `geospatialConfig` idá-lo-á ao tipo de dados de geografia. Quando modificar `geospatialConfig` os dados geoespaciais existentes no recipiente serão reexexexados.

Aqui está um exemplo para modificar o tipo de dados geoespaciais para `geometry` definir a propriedade e adicionar uma caixa de `geospatialConfig` **limites:**

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Exemplos de indexação de dados de geografia

O seguinte snippet JSON mostra uma política de indexação com indexação espacial habilitada para o tipo de dados **de geografia.** É válido para dados espaciais com o tipo de dados de geografia e irá indexar qualquer Ponto GeoJSON, Polygon, MultiPolygon ou LineString encontrados dentro de documentos para consulta espacial. Se estiver a modificar a política de indexação utilizando o portal Azure, pode especificar o seguinte JSON para a política de indexação para permitir a indexação espacial no seu recipiente:

**Política de indexação de contentores JSON com indexação espacial de geografia**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> Se o valor geojson de localização dentro do documento for mal formado ou inválido, então não será indexado para consulta espacial. Pode validar os valores de localização utilizando ST_ISVALID e ST_ISVALIDDETAILED.

Também pode [modificar a política de indexação](how-to-manage-indexing-policy.md) utilizando o Azure CLI, PowerShell ou qualquer SDK.

## <a name="geometry-data-indexing-examples"></a>Exemplos de indexação de dados de geometria

Com o tipo de dados **de geometria,** semelhante ao tipo de dados de geografia, deve especificar caminhos e tipos relevantes para indexar. Além disso, também deve especificar um `boundingBox` dentro da política de indexação para indicar a área desejada a ser indexada para esse caminho específico. Cada caminho geoespacial requer o seu `boundingBox` próprio.

A caixa de delimitação é constituída pelas seguintes propriedades:

- **xmin** : a coordenada x indexada mínima
- **ymin** : a coordenada y indexada mínima
- **xmax** : a coordenada x indexada máxima
- **ymax** : a coordenada y indexada máxima

Uma caixa de delimitação é necessária porque os dados geométricos ocupam um plano que pode ser infinito. Índices espaciais, no entanto, requerem um espaço finito. Para o tipo de dados **de geografia,** a Terra é o limite e você não precisa definir uma caixa limite.

Crie uma caixa de delimitação que contenha todos (ou a maioria) dos seus dados. Apenas as operações calculadas nos objetos que estão inteiramente dentro da caixa de delimitação poderão utilizar o índice espacial. Tornar a caixa de delimitação maior do que o necessário irá impactar negativamente o desempenho da consulta.

Aqui está um exemplo de política de indexação que indexa dados **de geometria** com **geoespacialConfig** definido para `geometry` :

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
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

A política de indexação acima tem uma caixa de **limites** de (-10, 10) para coordenadas x e (-20, 20) para coordenadas y. O contentor com a política de indexação acima irá indexar todos os Pontos, Polígonos, MultiPolígonos e LineStrings que estão inteiramente dentro desta região.

> [!NOTE]
> Se tentar adicionar uma política de indexação com uma **caixa de delimitação** a um recipiente com `geography` tipo de dados, falhará. Deve modificar o **GeoespacialConfig** do recipiente para ser `geometry` antes de adicionar uma caixa de **delimitação** . Pode adicionar dados e modificar o restante da sua política de indexação (como os caminhos e tipos) antes ou depois de selecionar o tipo de dados geoespaciais para o recipiente.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a começar com o suporte geoespacial em Azure Cosmos DB, a seguir pode:

* Saiba mais sobre [a Consulta DB do Azure Cosmos](sql-query-getting-started.md)
* Saiba mais sobre [consulta de dados espaciais com a Azure Cosmos DB](sql-query-geospatial-query.md)
* Saiba mais sobre [os dados de localização geoespaciais e geojson em Azure Cosmos DB](sql-query-geospatial-intro.md)
