---
title: Indexação no Azure Cosmos DB
description: Entenda como a indexação funciona em Azure Cosmos DB, tipos diferentes de índices, como intervalos, espaciais e índices compostos com suporte.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: 65186262095560d7ae54d32b218d1c01f1fb921d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873629"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexação em Azure Cosmos DB-visão geral

Azure Cosmos DB é um banco de dados independente de esquema que permite iterar em seu aplicativo sem precisar lidar com o gerenciamento de esquema ou de índice. Por padrão, Azure Cosmos DB indexa automaticamente cada propriedade para todos os itens em seu [contêiner](databases-containers-items.md#azure-cosmos-containers) sem precisar definir qualquer esquema ou configurar índices secundários.

O objetivo deste artigo é explicar como o Azure Cosmos DB indexa os dados e como utiliza os índices para melhorar o desempenho da consulta. É recomendável percorrer esta seção antes de explorar como personalizar políticas de [indexação](index-policy.md).

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

O Azure Cosmos DB atualmente dá suporte a três tipos de índices.

### <a name="range-index"></a>Índice de intervalo

O índice de **intervalo** se baseia em uma estrutura ordenada de árvore. O tipo de índice de intervalo é usado para:

- Consultas de igualdade:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Correspondência de igualdade em um elemento de matriz
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- Consultas de intervalo:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (funciona para `>`, `<`, `>=`, `<=`, `!=`)

- Verificando a presença de uma propriedade:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Correspondências de prefixo de cadeia de caracteres (CONTAINS palavra-chave não aproveitará o índice de intervalo):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- consultas de `ORDER BY`:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- consultas de `JOIN`:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Os índices de intervalo podem ser usados em valores escalares (cadeia de caracteres ou número).

### <a name="spatial-index"></a>Índice espacial

Os índices **espaciais** permitem consultas eficientes em objetos geoespaciais, como pontos, linhas, polígonos e MultiPolygon. Essas consultas usam as palavras-chave ST_DISTANCE, ST_WITHIN ST_INTERSECTS. Veja a seguir alguns exemplos que usam o tipo de índice espacial:

- Consultas de distância geoespaciais:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoespacial em consultas:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Consultas de interseção geoespacial:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Os índices espaciais podem ser usados em objetos [geojson](geospatial.md) formatados corretamente. Pontos, LineStrings, polígonos e multipolígonos atualmente têm suporte.

### <a name="composite-indexes"></a>Índices compostos

Os índices **compostos** aumentam a eficiência quando você está executando operações em vários campos. O tipo de índice composto é usado para:

- `ORDER BY` consultas em várias propriedades:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Consultas com um filtro e `ORDER BY`. Essas consultas podem utilizar um índice composto se a propriedade de filtro for adicionada à cláusula `ORDER BY`.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Consultas com um filtro em duas ou mais propriedades em que pelo menos uma propriedade é um filtro de igualdade

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Desde que um predicado de filtro use no tipo de índice, o mecanismo de consulta avaliará isso primeiro antes de verificar o restante. Por exemplo, se você tiver uma consulta SQL como `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* A consulta acima primeiro filtrará as entradas em que firstName = "Andrew" usando o índice. Em seguida, ele passa todas as entradas firstName = "Andrew" por meio de um pipeline subsequente para avaliar o predicado de filtro CONTAINS.

* Você pode acelerar as consultas e evitar verificações de contêiner completas ao usar funções que não usam o índice (por exemplo, CONTAINS) adicionando predicados de filtro adicionais que usam o índice. A ordem das cláusulas de filtro não é importante. O mecanismo de consulta irá descobrir quais predicados são mais seletivos e executar a consulta de acordo.


## <a name="querying-with-indexes"></a>Consultando com índices

Os caminhos extraídos ao indexar dados facilitam a pesquisa do índice durante o processamento de uma consulta. Ao corresponder a cláusula de `WHERE` de uma consulta com a lista de caminhos indexados, é possível identificar os itens que correspondem ao predicado de consulta muito rapidamente.

Por exemplo, considere a seguinte consulta: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. O predicado de consulta (filtragem em itens, onde qualquer local tem "França" como seu país) corresponderia ao caminho realçado em vermelho abaixo:

![Correspondência de um caminho específico dentro de uma árvore](./media/index-overview/matching-path.png)

> [!NOTE]
> Uma cláusula `ORDER BY` que Orders por uma única propriedade *sempre* precisa de um índice de intervalo e falhará se o caminho referenciado não tiver um. Da mesma forma, um `ORDER BY` consulta quais pedidos por várias propriedades *sempre* precisam de um índice composto.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre indexação nos seguintes artigos:

- [Política de indexação](index-policy.md)
- [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)
