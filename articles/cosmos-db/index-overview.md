---
title: Indexação no Azure Cosmos DB
description: Entenda como a indexação funciona em Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 4d961f8635a52a09011543b793ce8a87eaa4ea9e
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914196"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexação em Azure Cosmos DB-visão geral

Azure Cosmos DB é um banco de dados independente de esquema que permite iterar em seu aplicativo sem precisar lidar com o gerenciamento de esquema ou de índice. Por padrão, Azure Cosmos DB indexa automaticamente cada propriedade para todos os itens em seu [contêiner](databases-containers-items.md#azure-cosmos-containers) sem precisar definir qualquer esquema ou configurar índices secundários.

O objetivo deste artigo é explicar como Azure Cosmos DB os dados de índices e como ele usa índices para melhorar o desempenho da consulta. É recomendável percorrer esta seção antes de explorar como personalizar políticas de [indexação](index-policy.md).

## <a name="from-items-to-trees"></a>De itens para árvores

Toda vez que um item é armazenado em um contêiner, seu conteúdo é projetado como um documento JSON e, em seguida, convertido em uma representação de árvore. Isso significa que cada propriedade desse item é representada como um nó em uma árvore. Um pseudo nó raiz é criado como um pai para todas as propriedades de primeiro nível do item. Os nós folha contêm os valores escalares reais transportados por um item.

Por exemplo, considere este item:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Ele seria representado pela seguinte árvore:

![O item anterior representado como uma árvore](./media/index-overview/item-as-tree.png)

Observe como as matrizes são codificadas na árvore: cada entrada em uma matriz Obtém um nó intermediário rotulado com o índice dessa entrada dentro da matriz (0, 1 etc.).

## <a name="from-trees-to-property-paths"></a>De árvores para caminhos de propriedade

O motivo pelo qual Azure Cosmos DB transforma itens em árvores é porque permite que as propriedades sejam referenciadas por seus caminhos nessas árvores. Para obter o caminho para uma propriedade, podemos percorrer a árvore do nó raiz para essa propriedade e concatenar os rótulos de cada nó atravessado.

Aqui estão os caminhos para cada propriedade do item de exemplo descrito acima:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Quando um item é gravado, Azure Cosmos DB indexa efetivamente o caminho de cada propriedade e seu valor correspondente.

## <a name="index-kinds"></a>Tipos de índice

O Azure Cosmos DB atualmente dá suporte a três tipos de índices:

O tipo de índice de **intervalo** é usado para:

- Consultas de igualdade:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

- Consultas de intervalo:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (funciona para `>`, `<`, `>=` ,`<=`, )`!=`

- `ORDER BY`procura

   ```sql 
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`procura

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Os índices de intervalo podem ser usados em valores escalares (cadeia de caracteres ou número).

O tipo de índice **espacial** é usado para:

- Consultas de distância geoespaciais: 

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoespacial em consultas: 

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

Os índices espaciais podem ser usados em objetos [geojson](geospatial.md) formatados corretamente. Pontos, LineStrings, polígonos e multipolígonos atualmente têm suporte.

O tipo de índice **composto** é usado para:

- `ORDER BY`consultas em várias propriedades:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Consultas com um filtro e `ORDER BY`. Essas consultas podem utilizar um índice composto se a propriedade de filtro for adicionada à `ORDER BY` cláusula.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Consultas com um filtro em duas ou mais propriedades em que pelo menos uma propriedade é um filtro de igualdade

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

## <a name="querying-with-indexes"></a>Consultando com índices

Os caminhos extraídos ao indexar dados facilitam a pesquisa do índice durante o processamento de uma consulta. Ao corresponder a `WHERE` cláusula de uma consulta com a lista de caminhos indexados, é possível identificar os itens que correspondem ao predicado de consulta muito rapidamente.

Por exemplo, considere a seguinte consulta: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. O predicado de consulta (filtragem em itens, onde qualquer local tem "França" como seu país) corresponderia ao caminho realçado em vermelho abaixo:

![Correspondência de um caminho específico dentro de uma árvore](./media/index-overview/matching-path.png)

> [!NOTE]
> Uma `ORDER BY` cláusula que ordena por uma única propriedade *sempre* precisa de um índice de intervalo e falhará se o caminho referenciado não tiver um. Da mesma forma `ORDER BY` , uma consulta que ordena por várias propriedades *sempre* precisa de um índice composto.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre indexação nos seguintes artigos:

- [Política de indexação](index-policy.md)
- [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)
