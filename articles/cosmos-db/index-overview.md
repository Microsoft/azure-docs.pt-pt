---
title: Indexação no Azure Cosmos DB
description: Compreenda como a indexação funciona no Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: thweiss
ms.openlocfilehash: 3bb8913725acf04f71aba8b4c4350235f2c44dfb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61051890"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexação no Azure Cosmos DB - descrição geral

O Azure Cosmos DB é uma base de dados sem esquema, que permite que itere na sua aplicação sem ter de lidar com a gestão de índices ou esquemas. Por predefinição, o Azure Cosmos DB indexa automaticamente todas as propriedades para todos os itens no seu [contentor](databases-containers-items.md#azure-cosmos-containers) sem ter de definir qualquer esquema ou índices secundários de configurar.

O objetivo deste artigo é explicar como o Azure Cosmos DB indexa dados e como ele usa índices para melhorar o desempenho de consulta. Recomenda-se passar por esta secção antes de explorar como personalizar [políticas de indexação](index-policy.md).

## <a name="from-items-to-trees"></a>A partir de itens para árvores

Sempre que um item está armazenado num contentor, o seu conteúdo é projetado como um documento JSON então convertido numa representação em árvore. O que significa que todas as propriedades desse item é representada como um nó numa árvore. Um nó de raiz de pseudo-autenticação é criado um elemento principal para todas as propriedades de primeiro nível do item. Os nós de folha contêm os valores de escalares reais realizados por um item.

Por exemplo, considere este item:

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

Seria representada pela árvore do seguinte:

![O item anterior, representado como uma árvore](./media/index-overview/item-as-tree.png)

Tenha em atenção o modo como as matrizes são codificadas na árvore: cada entrada numa matriz obtém um nó de nível intermediário rotulado com o índice dessa entrada dentro da matriz (0, 1 etc.).

## <a name="from-trees-to-property-paths"></a>De árvores para caminhos de propriedade

O motivo por que o Azure Cosmos DB transforma itens em árvores é porque ela permite que as propriedades para ser referenciada por seus caminhos dentro essas árvores. Para obter o caminho para uma propriedade, podemos percorrer a árvore do nó raiz para essa propriedade e concatenar as etiquetas de cada nó percorrido.

Seguem-se os caminhos para cada propriedade do item de exemplo descrito acima:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Quando um item é escrito, o Azure Cosmos DB indexa efetivamente o caminho de cada propriedade e o respetivo valor correspondente.

## <a name="index-kinds"></a>Tipos de índice

Atualmente, o Azure Cosmos DB suporta dois tipos de índices:

O **intervalo** tipo de índice é utilizado para:

- consultas de igualdade: `SELECT * FROM container c WHERE c.property = 'value'`
- consultas de intervalo: `SELECT * FROM container c WHERE c.property > 'value'` (funciona para `>`, `<`, `>=`, `<=`, `!=`)
- `ORDER BY` consultas: `SELECT * FROM container c ORDER BY c.property`
- `JOIN` consultas: `SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'`

Índices de intervalo podem ser utilizados em valores escalares (cadeia de caracteres ou número).

O **geográficos** tipo de índice é utilizado para:

- consultas de distância geoespacial: `SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40`
- geoespacial dentro de consultas: `SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })`

Os índices espaciais podem ser utilizados em formatado corretamente [GeoJSON](geospatial.md) objetos. Pontos, LineStrings e polígonos são atualmente suportados.

## <a name="querying-with-indexes"></a>Consultar com índices

Os caminhos extraídos durante a indexação de dados facilitam a pesquisar o índice quando uma consulta de processamento. Comparando o `WHERE` cláusula de uma consulta com a lista de caminhos indexados, é possível identificar os itens que correspondem ao predicado de consulta muito rapidamente.

Por exemplo, considere a seguinte consulta: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. O predicado de consulta (filtragem em itens, onde qualquer localização tem "França" como o seu país) deve corresponder ao caminho realçado em vermelho abaixo:

![Um caminho específico dentro de uma árvore de correspondência](./media/index-overview/matching-path.png)

> [!NOTE]
> Uma `ORDER BY` cláusula *sempre* necessita de um intervalo de índice e irá falhar se o caminho referencia não o tenha.

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre indexação nos seguintes artigos:

- [Política de indexação](index-policy.md)
- [Como gerir a política de indexação](how-to-manage-indexing-policy.md)
