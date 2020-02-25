---
title: Dados geoespaciais indexados com Azure Cosmos DB
description: Dados espaciais indexados com Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566377"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Dados geoespaciais indexados com Azure Cosmos DB

Desenhamos o motor de base de dados da Azure Cosmos DB para ser verdadeiramente agnóstico e fornecer suporte de primeira classe para a JSON. O motor de base de dados otimizado de escrita da Azure Cosmos DB compreende de forma nativa os dados espaciais representados na norma GeoJSON.

Em resumo, a geometria é projetada a partir de coordenadas geodésicas para um plano 2D e depois dividida progressivamente em células usando um **quadtree**. Estas células são mapeadas para 1D com base na localização da célula dentro de uma curva de enchimento espacial **Hilbert,** que preserva a localidade dos pontos. Além disso, quando os dados de localização são indexados, passa por um processo conhecido como **tessellation**, isto é, todas as células que se cruzam com um local são identificadas e armazenadas como chaves no índice Azure Cosmos DB. No momento da consulta, argumentos como pontos e polígonos são também presentes para extrair os intervalos de ID de célula relevantes, em seguida, usados para recuperar dados do índice.

Se especificar uma política de indexação que inclui o índice espacial para /* (todos os caminhos), então todos os dados encontrados dentro do recipiente estão indexados para consultas espaciais eficientes.

> [!NOTE]
> Azure Cosmos DB suporta indexação de Pontos, LineStrings, Polígonos e MultiPolygons
>
>

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
>
>
>

Também pode [modificar a política de indexação](how-to-manage-indexing-policy.md) utilizando o Azure CLI, PowerShell ou qualquer SDK.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como começar com o suporte geoespacial no Azure Cosmos DB, em seguida, pode:

* Saiba mais sobre [a consulta de DB Azure Cosmos](sql-query-getting-started.md)
* Saiba mais sobre [consulta de dados espaciais com Azure Cosmos DB](sql-query-geospatial-query.md)
* Saiba mais sobre os dados de [localização Geospatial e GeoJSON em Azure Cosmos DB](sql-query-geospatial-intro.md)