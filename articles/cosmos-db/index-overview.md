---
title: Indexação no Azure Cosmos DB
description: Compreenda como funciona a indexação em Azure Cosmos DB, diferentes tipos de índices como Range, Spatial, indexs compósitos suportados.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: df9135c39c1ff27abe8915c221185fca517a5614
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849795"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexação no Azure Cosmos DB – Descrição geral

A Azure Cosmos DB é uma base de dados schema-agnóstica que lhe permite iterar na sua aplicação sem ter de lidar com esquemas ou gestão de índices. Por padrão, o Azure Cosmos DB indexa automaticamente todas as propriedades para todos os itens do seu [recipiente](databases-containers-items.md#azure-cosmos-containers) sem ter de definir qualquer esquema ou configurar índices secundários.

O objetivo deste artigo é explicar como o Azure Cosmos DB indexa os dados e como utiliza os índices para melhorar o desempenho da consulta. Recomenda-se passar por esta secção antes de explorar como personalizar políticas de [indexação.](index-policy.md)

## <a name="from-items-to-trees"></a>De itens a árvores

Sempre que um item é armazenado num recipiente, o seu conteúdo é projetado como um documento JSON, e depois convertido em representação de árvores. O que isso significa é que todas as propriedades desse item são representadas como um nó numa árvore. Um nó de raiz pseudo é criado como um pai para todas as propriedades de primeiro nível do item. Os nódosos de folha contêm os valores reais do escalar transportados por um item.

Como exemplo, considere este item:

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

Seria representado pela seguinte árvore:

![O item anterior representado como uma árvore](./media/index-overview/item-as-tree.png)

Note como as matrizes são codificadas na árvore: cada entrada numa matriz obtém um nó intermédio marcado com o índice dessa entrada dentro da matriz (0, 1 etc.).

## <a name="from-trees-to-property-paths"></a>Das árvores aos caminhos da propriedade

A razão pela qual o Azure Cosmos DB transforma itens em árvores é porque permite que as propriedades sejam referenciadas pelos seus caminhos dentro dessas árvores. Para obter o caminho para uma propriedade, podemos atravessar a árvore desde o nó de raiz até aquela propriedade, e concatenar os rótulos de cada nó atravessado.

Aqui estão os caminhos para cada propriedade a partir do item de exemplo descrito acima:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Quando um item é escrito, o Azure Cosmos DB indexa efetivamente o caminho de cada propriedade e o seu valor correspondente.

## <a name="index-kinds"></a>Tipos de índice

A Azure Cosmos DB suporta atualmente três tipos de índices.

### <a name="range-index"></a>Índice de Gama

**O** índice de alcance baseia-se numa estrutura ordenada como uma árvore. O tipo de índice de gama é utilizado para:

- Consultas sobre igualdade:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Igualdade de fósforo em um elemento matriz
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Consultas de alcance:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (trabalha `>` `<` para, `>=` , , , `<=` `!=` )

- Verificando a presença de um imóvel:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Funções do sistema de cordas:

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STRINGEQUALS(c.property, "value")
   ```

- `ORDER BY`consultas:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`consultas:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Os índices de gama podem ser utilizados em valores escalar (corda ou número).

### <a name="spatial-index"></a>Índice espacial

Os índices **espaciais** permitem consultas eficientes em objetos geoespaciais tais como - pontos, linhas, polígonos e multipoligon. Estas consultas usam ST_DISTANCE, ST_WITHIN, ST_INTERSECTS palavras-chave. Seguem-se alguns exemplos que utilizam o tipo de índice espacial:

- Consultas de distância geoespacial:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoespacial dentro de consultas:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Consultas de interseto geoespacial:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Os índices espaciais podem ser utilizados em objetos [GeoJSON](geospatial.md) corretamente formatados. Os pontos, LineStrings, Polígonos e MultiPolygons são atualmente suportados.

### <a name="composite-indexes"></a>Índices compósitos

Os índices **compósitos** aumentam a eficiência quando se está a realizar operações em vários campos. O tipo de índice composto é utilizado para:

- `ORDER BY`consultas sobre várias propriedades:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Consultas com filtro e `ORDER BY` . Estas consultas podem utilizar um índice composto se a propriedade do filtro for adicionada à `ORDER BY` cláusula.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Consultas com um filtro em duas ou mais propriedades onde pelo menos uma propriedade é um filtro de igualdade

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Enquanto um predicado de filtro utilizar um dos tipos de índice, o motor de consulta avaliará isso primeiro antes de digitalizar o resto. Por exemplo, se tiver uma consulta SQL como`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* A consulta acima será primeiramente filtrada para entradas onde o primeiro nome = "Andrew" utilizando o índice. Em seguida, passa todas as entradas do primeiro nome = "Andrew" através de um pipeline subsequente para avaliar o predicado do filtro CONTÉM.

* Pode acelerar as consultas e evitar exames completos de contentores quando utilizar funções que não utilizam o índice (por exemplo, CONTÉM) adicionando predicados de filtro adicionais que utilizam o índice. A ordem das cláusulas de filtro não é importante. O motor de consulta vai descobrir quais os predicados mais seletivos e executar a consulta em conformidade.


## <a name="querying-with-indexes"></a>Consulta com índices

Os caminhos extraídos ao indexar os dados facilitam a procura do índice no processamento de uma consulta. Ao combinar a `WHERE` cláusula de uma consulta com a lista de caminhos indexados, é possível identificar os itens que correspondem à consulta predicada muito rapidamente.

Por exemplo, considere a seguinte consulta: `SELECT location FROM location IN company.locations WHERE location.country = 'France'` . O predicado da consulta (filtragem em itens, onde qualquer local tem "França" como seu país/região) corresponderia ao caminho realçado a vermelho abaixo:

![Combinando um caminho específico dentro de uma árvore](./media/index-overview/matching-path.png)

> [!NOTE]
> Uma `ORDER BY` cláusula que encomenda por uma única propriedade precisa *sempre* de um índice de alcance e falhará se o caminho que refere não tiver um. Da mesma forma, uma `ORDER BY` consulta que encomenda por múltiplas propriedades *sempre* precisa de um índice composto.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre indexação nos seguintes artigos:

- [Política de indexação](index-policy.md)
- [Como gerir a política de indexação](how-to-manage-indexing-policy.md)
