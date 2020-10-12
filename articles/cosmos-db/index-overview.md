---
title: Indexação no Azure Cosmos DB
description: Entenda como a indexação funciona em Azure Cosmos DB, diferentes tipos de índices tais como Range, Spatial, índices compósitos suportados.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: 3d07657fc3345ddd8dfadd163dc3c9f957d77af3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90068392"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexação no Azure Cosmos DB – Descrição geral

Azure Cosmos DB é uma base de dados de esquemas agnósticos que lhe permite iterar a sua aplicação sem ter de lidar com esquemas ou gestão de índices. Por padrão, a Azure Cosmos DB indexa automaticamente todas as propriedades para todos os itens do seu [contentor](databases-containers-items.md#azure-cosmos-containers) sem ter de definir qualquer esquema ou configurar índices secundários.

O objetivo deste artigo é explicar como o Azure Cosmos DB indexa os dados e como utiliza os índices para melhorar o desempenho da consulta. Recomenda-se passar por esta secção antes de explorar como personalizar [políticas de indexação.](index-policy.md)

## <a name="from-items-to-trees"></a>De itens a árvores

Sempre que um item é armazenado num recipiente, o seu conteúdo é projetado como um documento JSON e, em seguida, convertido numa representação de árvore. O que isso significa é que cada propriedade desse item é representada como um nó numa árvore. Um nó de raiz pseudo é criado como um pai para todas as propriedades de primeiro nível do item. Os nós de folha contêm os valores de escalar reais transportados por um item.

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

Seria representada pela seguinte árvore:

:::image type="content" source="./media/index-overview/item-as-tree.png" alt-text="O item anterior representado como uma árvore" border="false":::

Note como as matrizes são codificadas na árvore: cada entrada numa matriz recebe um nó intermédio rotulado com o índice dessa entrada dentro da matriz (0, 1 etc.).

## <a name="from-trees-to-property-paths"></a>Das árvores aos caminhos da propriedade

A razão pela qual a Azure Cosmos DB transforma itens em árvores é porque permite que as propriedades sejam referenciadas pelos seus caminhos dentro dessas árvores. Para obter o caminho para uma propriedade, podemos atravessar a árvore desde o nó raiz até aquela propriedade, e concatenar os rótulos de cada nó atravessado.

Aqui estão os caminhos para cada propriedade a partir do item de exemplo acima descrito:

- /localizações/0/país: "Alemanha"
- /localizações/0/cidade: "Berlim"
- /localizações/1/país: "França"
- /localizações/1/cidade: "Paris"
- /sede/país: "Bélgica"
- /sede/empregados: 250
- /exportações/0/cidade: "Moscovo"
- /exportações/1/cidade: "Atenas"

Quando um item é escrito, a Azure Cosmos DB indexa eficazmente o caminho de cada propriedade e o seu valor correspondente.

## <a name="index-kinds"></a>Tipos de índice

A Azure Cosmos DB suporta atualmente três tipos de índices.

### <a name="range-index"></a>Índice de Gama

**O** índice de alcance baseia-se numa estrutura ordenada em forma de árvore. O tipo de índice de gama é utilizado para:

- Consultas de igualdade:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Igualdade jogo em um elemento de matriz
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Consultas de alcance:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (obras `>` `<` para, `>=` , , , `<=` `!=` )

- Verificação da presença de um imóvel:

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

- `ORDER BY` Consultas:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` Consultas:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Os índices de gama podem ser usados em valores escalares (cadeia ou número).

### <a name="spatial-index"></a>Índice espacial

Os índices **espaciais** permitem consultas eficientes em objetos geoespaciais tais como - pontos, linhas, polígonos e multipolígonos. Estas consultas usam ST_DISTANCE, ST_WITHIN, ST_INTERSECTS palavras-chave. Seguem-se alguns exemplos que utilizam o tipo de índice espacial:

- Consultas de distância geoespaciais:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoespacial dentro de consultas:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] })
   ```

- Consultas intersectais geoespaciais:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Os índices espaciais podem ser utilizados em objetos [GeoJSON](geospatial.md) corretamente formatados. Pontos, LineStrings, Polígonos e MultiPolygons são atualmente suportados.

### <a name="composite-indexes"></a>Índices compostos

Os índices **compósitos** aumentam a eficiência quando está a realizar operações em vários campos. O tipo de índice composto é utilizado para:

- `ORDER BY` consultas sobre múltiplas propriedades:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Consultas com um filtro e `ORDER BY` . Estas consultas podem utilizar um índice composto se a propriedade do filtro for adicionada à `ORDER BY` cláusula.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Consultas com um filtro em duas ou mais propriedades onde pelo menos uma propriedade é um filtro de igualdade

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Enquanto um pré-filtro de filtros utilizar um do tipo indexado, o motor de consulta avaliará primeiro antes de digitalizar o resto. Por exemplo, se tiver uma consulta SQL, como `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* A consulta acima filtra-se primeiro para entradas onde primeiro Nome = "Andrew" utilizando o índice. Em seguida, passa todas as entradas do primeiro Nome = "Andrew" através de um pipeline subsequente para avaliar o predicado do filtro CONTAINS.

* Pode acelerar as consultas e evitar análises completas dos contentores quando utilizar funções que não utilizem o índice (por exemplo, CONTAINS) adicionando predicados de filtros adicionais que utilizam o índice. A ordem das cláusulas de filtragem não é importante. O motor de consulta é vai descobrir quais os predicados são mais seletivos e executar a consulta em conformidade.

## <a name="querying-with-indexes"></a>Consulta com índices

Os caminhos extraídos ao indexar dados facilitam a procura do índice ao processar uma consulta. Ao combinar a `WHERE` cláusula de uma consulta com a lista de caminhos indexados, é possível identificar os itens que correspondem à consulta predicado muito rapidamente.

Por exemplo, considere a seguinte consulta: `SELECT location FROM location IN company.locations WHERE location.country = 'France'` . O predicado de consulta (filtragem em itens, onde qualquer local tem "França" como seu país/região) corresponderia ao caminho realçado a vermelho abaixo:

:::image type="content" source="./media/index-overview/matching-path.png" alt-text="O item anterior representado como uma árvore" border="false":::

> [!NOTE]
> Uma `ORDER BY` cláusula que ordena por uma única propriedade *sempre* precisa de um índice de alcance e falhará se o caminho que referenciar não tiver um. Da mesma forma, uma `ORDER BY` consulta que encomenda por múltiplas propriedades *sempre* precisa de um índice composto.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre a indexação nos seguintes artigos:

- [Política de indexação](index-policy.md)
- [Como gerir a política de indexação](how-to-manage-indexing-policy.md)
