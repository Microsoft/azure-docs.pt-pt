---
title: Dados geoespaciais indexados com Azure Cosmos DB
description: Dados espaciais indexados com Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137908"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Dados geoespaciais indexados com Azure Cosmos DB

Desenhamos o motor de base de dados da Azure Cosmos DB para ser verdadeiramente agnóstico e fornecer suporte de primeira classe para a JSON. O motor de base de dados otimizado de escrita da Azure Cosmos DB compreende de forma nativa os dados espaciais representados na norma GeoJSON.

Em resumo, a geometria é projetada a partir de coordenadas geodésicas para um plano 2D e depois dividida progressivamente em células usando um **quadtree**. Estas células são mapeadas para 1D com base na localização da célula dentro de uma curva de enchimento espacial **Hilbert,** que preserva a localidade dos pontos. Além disso, quando os dados de localização são indexados, passa por um processo conhecido como **tessellation**, isto é, todas as células que se cruzam com um local são identificadas e armazenadas como chaves no índice Azure Cosmos DB. No momento da consulta, argumentos como pontos e polígonos são também presentes para extrair os intervalos de ID de célula relevantes, em seguida, usados para recuperar dados do índice.

Se especificar uma política de indexação que inclui o índice espacial para /* (todos os caminhos), então todos os dados encontrados dentro do recipiente estão indexados para consultas espaciais eficientes.

> [!NOTE]
> Azure Cosmos DB suporta indexação de Pontos, LineStrings, Polígonos e MultiPolygons
>
>

## <a name="modifying-geospatial-data-type"></a>Modificação do tipo de dados geoespaciais

No seu recipiente, o `geospatialConfig` especifica como os dados geoespaciais serão indexados. Deve especificar um `geospatialConfig` por recipiente: geografia ou geometria. Se não for especificado, o `geospatialConfig` será indefinido com o tipo de dados de geografia. Quando modificar o `geospatialConfig`, todos os dados geoespaciais existentes no recipiente serão reindexados.

> [!NOTE]
> A Tualmente, a Azure Cosmos DB suporta modificações no geoespacialConfig no .NET SDK apenas nas versões 3.6 ou superior.
>

Aqui está um exemplo para modificar o tipo de dados geoespaciais para `geometry`, definindo a propriedade `geospatialConfig` e adicionando uma **Caixa de limitadora:**

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

O seguinte snippet JSON mostra uma política de indexação com indexação espacial habilitada para o tipo de dados **de geografia.** É válido para dados espaciais com o tipo de dados de geografia e indexará qualquer Ponto GeoJSON, Polygon, MultiPolygon ou LineString encontrados dentro de documentos para consulta espacial. Se estiver a modificar a política de indexação utilizando o portal Azure, pode especificar o seguinte JSON para a política de indexação para permitir a indexação espacial no seu recipiente:

**Política de indexação de contentores JSON com indexação espacial de geografia**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
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
       "excludedPaths":[]
    }
```

> [!NOTE]
> Se a localização do valor GeoJSON dentro do documento é um formato incorreto ou é inválido, em seguida, este será não obter indexado para consultas espaciais. Pode validar valores de localização usando ST_ISVALID e ST_ISVALIDDETAILED.

Também pode [modificar a política de indexação](how-to-manage-indexing-policy.md) utilizando o Azure CLI, PowerShell ou qualquer SDK.

## <a name="geometry-data-indexing-examples"></a>Exemplos de indexação de dados de geometria

Com o tipo de dados de **geometria,** semelhante ao tipo de dados de geografia, deve especificar caminhos e tipos relevantes para indexar. Além disso, deve também especificar uma `boundingBox` dentro da política de indexação para indicar que a área desejada deve ser indexada para esse caminho específico. Cada caminho geoespacial requer o seu próprio`boundingBox`.

A caixa de delimitação é constituída pelas seguintes propriedades:

- **xmin:** a coordenada x indexada mínima
- **ymin**: a coordenada mínima indexada y
- **xmax**: a coordenada x indexada máxima
- **ymax**: a coordenada y indexada máxima

Uma caixa de delimitação é necessária porque os dados geométricos ocupam um plano que pode ser infinito. Índices espaciais, no entanto, requerem um espaço finito. Para o tipo de dados **de geografia,** a Terra é o limite e não é preciso definir uma caixa de delimitação.

Deve criar uma caixa de delimitação que contenha todos (ou a maioria) dos seus dados. Apenas as operações calculadas nos objetos que estão inteiramente dentro da caixa de delimitação poderão utilizar o índice espacial. Não deve tornar a caixa de delimitação significativamente maior do que o necessário, pois isso terá um impacto negativo no desempenho da consulta.

Aqui está uma política de indexação de exemplo que indexa dados de **geometria** com **geoespacialConfig** definido para `geometry`:

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

A política de indexação acima tem uma **Caixa limitada** de (-10, 10) para coordenadas x e (-20, 20) para coordenadas y. O contentor com a política de indexação acima indexará todos os Pontos, Polígonos, MultiPolygons e LineStrings que estão inteiramente dentro desta região.

> [!NOTE]
> Se tentar adicionar uma política de indexação com uma **Caixa de limitação** a um recipiente com `geography` tipo de dados, falhará. Deve modificar o **geoespacialConfig** do recipiente para ser `geometry` antes de adicionar uma **Caixa de delimitação**. Pode adicionar dados e modificar o restante da sua política de indexação (como os caminhos e tipos) antes ou depois de selecionar o tipo de dados geoespaciais para o recipiente.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como começar com o suporte geoespacial no Azure Cosmos DB, em seguida, pode:

* Saiba mais sobre [a consulta de DB Azure Cosmos](sql-query-getting-started.md)
* Saiba mais sobre [consulta de dados espaciais com Azure Cosmos DB](sql-query-geospatial-query.md)
* Saiba mais sobre os dados de [localização Geospatial e GeoJSON em Azure Cosmos DB](sql-query-geospatial-intro.md)