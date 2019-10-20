---
title: Políticas de indexação de Azure Cosmos DB
description: Saiba como configurar e alterar a política de indexação padrão para indexação automática e melhor desempenho no Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 886d17098259ddbb78698a3c1280f797e370c714
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597147"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Políticas de indexação no Azure Cosmos DB

No Azure Cosmos DB, cada contêiner tem uma política de indexação que determina como os itens do contêiner devem ser indexados. A política de indexação padrão para contêineres recém-criados indexa cada propriedade de cada item, impondo índices de intervalo para qualquer cadeia de caracteres ou número e índices espaciais para qualquer objeto geojson do tipo Point. Isso permite que você obtenha alto desempenho de consulta sem precisar pensar na indexação e no gerenciamento de índice antecipadamente.

Em algumas situações, talvez você queira substituir esse comportamento automático para atender melhor às suas necessidades. Você pode personalizar a política de indexação de um contêiner definindo seu *modo de indexação*e incluir ou excluir os *caminhos de propriedade*.

> [!NOTE]
> O método de atualização das políticas de indexação descritas neste artigo se aplica somente à API do SQL (Core) do Azure Cosmos DB.

## <a name="indexing-mode"></a>Modo de indexação

O Azure Cosmos DB dá suporte a dois modos de indexação:

- **Consistente**: o índice é atualizado de forma síncrona à medida que você cria, atualiza ou exclui itens. Isso significa que a consistência de suas consultas de leitura será a [consistência configurada para a conta](consistency-levels.md).
- **Nenhum**: a indexação está desabilitada no contêiner. Isso é normalmente usado quando um contêiner é usado como um repositório de chave-valor puro sem a necessidade de índices secundários. Ele também pode ser usado para melhorar o desempenho de operações em massa. Depois que as operações em massa forem concluídas, o modo de índice poderá ser definido como consistente e, em seguida, monitorado usando o [IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) até ser concluído.

> [!NOTE]
> O Cosmos DB também dá suporte a um modo de indexação lento. A indexação lenta executa atualizações no índice em um nível de prioridade muito menor quando o mecanismo não está fazendo nenhum outro trabalho. Isso pode resultar em resultados de consulta **inconsistentes ou incompletos** . Além disso, o uso de indexação lenta no lugar de ' none ' para operações em massa também não fornece nenhum benefício, pois qualquer alteração no modo de índice fará com que o índice seja descartado e recriado. Por esses motivos, é recomendável em relação aos clientes que o utilizam. Para melhorar o desempenho de operações em massa, defina modo de índice como nenhum e, em seguida, retorne ao modo consistente e monitore a propriedade `IndexTransformationProgress` no contêiner até concluir.

Por padrão, a política de indexação é definida como `automatic`. É possível definir a propriedade `automatic` na política de indexação como `true`. Definir essa propriedade como `true` permite que o Azure CosmosDB indexe automaticamente os documentos conforme eles são gravados.

## <a name="including-and-excluding-property-paths"></a>Incluindo e excluindo caminhos de propriedade

Uma política de indexação personalizada pode especificar caminhos de propriedade que são explicitamente incluídos ou excluídos da indexação. Ao otimizar o número de caminhos que são indexados, você pode diminuir a quantidade de armazenamento usada pelo seu contêiner e melhorar a latência de operações de gravação. Esses caminhos são definidos seguindo [o método descrito na seção visão geral da indexação](index-overview.md#from-trees-to-property-paths) com as seguintes adições:

- um caminho que leva a um valor escalar (cadeia de caracteres ou número) termina com `/?`
- os elementos de uma matriz são abordados em conjunto por meio da notação de `/[]` (em vez de `/0`, `/1` etc.)
- o curinga `/*` pode ser usado para corresponder a qualquer elemento abaixo do nó

Dando o mesmo exemplo novamente:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- o caminho de `employees` do `headquarters` é `/headquarters/employees/?`

- o caminho do `locations` ' `country` é `/locations/[]/country/?`

- o caminho para qualquer coisa em `headquarters` é `/headquarters/*`

Por exemplo, poderíamos incluir o caminho `/headquarters/employees/?`. Esse caminho garantiria que indexamos a propriedade Employees, mas não indexo JSON aninhado adicional dentro dessa propriedade.

## <a name="includeexclude-strategy"></a>Estratégia de inclusão/exclusão

Qualquer política de indexação deve incluir o caminho raiz `/*` como um caminho incluído ou excluído.

- Inclua o caminho raiz para excluir seletivamente os caminhos que não precisam ser indexados. Essa é a abordagem recomendada, pois ela permite que Azure Cosmos DB indexe proativamente qualquer nova propriedade que possa ser adicionada ao seu modelo.
- Exclua o caminho raiz para incluir seletivamente os caminhos que precisam ser indexados.

- Para caminhos com caracteres regulares que incluem: caracteres alfanuméricos e _ (sublinhado), você não precisa escapar da cadeia de caracteres do caminho em volta de aspas duplas (por exemplo, "/Path/?"). Para caminhos com outros caracteres especiais, você precisa escapar da cadeia de caracteres de caminho em aspas duplas (por exemplo, "/\"path-ABC \"/?"). Se você espera caracteres especiais em seu caminho, pode escapar de cada caminho para segurança. Funcionalmente não faz nenhuma diferença se você escapa de todos os caminhos, e não apenas aqueles com caracteres especiais.

- A propriedade do sistema "ETag" é excluída da indexação por padrão, a menos que a ETag seja adicionada ao caminho incluído para indexação.

Ao incluir e excluir caminhos, você pode encontrar os seguintes atributos:

- `kind` pode ser `range` ou `hash`. A funcionalidade de índice de intervalo fornece toda a funcionalidade de um índice de hash, portanto, é recomendável usar um índice de intervalo.

- `precision` é um número definido no nível de índice para caminhos incluídos. Um valor de `-1` indica A precisão máxima. É recomendável sempre definir esse valor como `-1`.

- `dataType` pode ser `String` ou `Number`. Isso indica os tipos de propriedades JSON que serão indexadas.

Quando não for especificado, essas propriedades terão os seguintes valores padrão:

| **Nome da propriedade**     | **Valor padrão** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` e `Number` |

Consulte [esta seção](how-to-manage-indexing-policy.md#indexing-policy-examples) para obter exemplos de política de indexação para incluir e excluir caminhos.

## <a name="spatial-indexes"></a>Índices espaciais

Ao definir um caminho espacial na política de indexação, você deve definir qual ```type``` de índice deve ser aplicado a esse caminho. Os tipos possíveis para índices espaciais incluem:

* Empresas

* Polygon

* MultiPolygon

* LineString

Azure Cosmos DB, por padrão, não criará nenhum índice espacial. Se você quiser usar funções internas SQL espaciais, deverá criar um índice espacial nas propriedades necessárias. Consulte [esta seção](geospatial.md) para obter exemplos de política de indexação para adicionar índices espaciais.

## <a name="composite-indexes"></a>Índices compostos

As consultas que têm uma cláusula `ORDER BY` com duas ou mais propriedades exigem um índice composto. Você também pode definir um índice composto para melhorar o desempenho de várias consultas de igualdade e de intervalo. Por padrão, nenhum índice composto é definido, portanto, você deve [Adicionar índices compostos](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) conforme necessário.

Ao definir um índice composto, você especifica:

- Dois ou mais caminhos de propriedade. A sequência na qual os caminhos de propriedade são definidos é importante.

- A ordem (crescente ou decrescente).

> [!NOTE]
> Quando você adiciona um índice composto, a consulta utilizará índices de intervalo existentes até que a nova adição de índice composto seja concluída. Portanto, ao adicionar um índice composto, você pode não observar imediatamente as melhorias de desempenho. É possível acompanhar o progresso da transformação de índice [usando um dos SDKs](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>ORDENAr por consultas em várias propriedades:

As seguintes considerações são usadas ao usar índices compostos para consultas com uma cláusula `ORDER BY` com duas ou mais propriedades:

- Se os caminhos do índice composto não corresponderem à sequência das propriedades na cláusula `ORDER BY`, o índice composto não poderá dar suporte à consulta.

- A ordem dos caminhos de índice composto (crescente ou decrescente) também deve corresponder à `order` na cláusula `ORDER BY`.

- O índice composto também dá suporte a uma cláusula `ORDER BY` com a ordem oposta em todos os caminhos.

Considere o exemplo a seguir em que um índice composto é definido nas propriedades Name, age e _ts:

| **Índice composto**     | **Consulta de `ORDER BY` de exemplo**      | **Com suporte do índice composto?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Você deve personalizar a política de indexação para que possa atender a todas as consultas de `ORDER BY` necessárias.

### <a name="queries-with-filters-on-multiple-properties"></a>Consultas com filtros em várias propriedades

Se uma consulta tiver filtros em duas ou mais propriedades, poderá ser útil criar um índice composto para essas propriedades.

Por exemplo, considere a seguinte consulta que tem um filtro de igualdade em duas propriedades:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Essa consulta será mais eficiente, levando menos tempo e consumindo menos RU, se for capaz de aproveitar um índice composto em (nome ASC, idade ASC).

Consultas com filtros de intervalo também podem ser otimizadas com um índice composto. No entanto, a consulta só pode ter um filtro de intervalo único. Os filtros de intervalo incluem `>`, `<`, `<=`, `>=` e `!=`. O filtro de intervalo deve ser definido por último no índice composto.

Considere a seguinte consulta com filtros de igualdade e de intervalo:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Essa consulta será mais eficiente com um índice composto em (nome ASC, idade ASC). No entanto, a consulta não utilizará um índice composto em (age ASC, Name ASC) porque os filtros de igualdade devem ser definidos primeiro no índice composto.

As seguintes considerações são usadas ao criar índices compostos para consultas com filtros em várias propriedades

- As propriedades no filtro da consulta devem corresponder às do índice composto. Se uma propriedade estiver no índice composto, mas não estiver incluída na consulta como um filtro, a consulta não usará o índice composto.
- Se uma consulta tiver propriedades adicionais no filtro que não foram definidas em um índice composto, uma combinação de índices compostos e de intervalo será usada para avaliar a consulta. Isso exigirá menos RU do que usar exclusivamente índices de intervalo.
- Se uma propriedade tiver um filtro de intervalo (`>`, `<`, `<=`, `>=` ou `!=`), essa propriedade deverá ser definida por último no índice composto. Se uma consulta tiver mais de um filtro de intervalo, ela não usará o índice composto.
- Ao criar um índice composto para otimizar consultas com vários filtros, a `ORDER` do índice composto não terá impacto sobre os resultados. Esta propriedade é opcional.
- Se você não definir um índice composto para uma consulta com filtros em várias propriedades, a consulta ainda terá sucesso. No entanto, o custo de RU da consulta pode ser reduzido com um índice composto.

Considere os seguintes exemplos em que um índice composto é definido nas propriedades Name, age e timestamp:

| **Índice composto**     | **Consulta de exemplo**      | **Com suporte do índice composto?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Consultas com um filtro e com uma cláusula ORDER BY

Se uma consulta filtrar em uma ou mais propriedades e tiver propriedades diferentes na cláusula ORDER BY, poderá ser útil adicionar as propriedades no filtro à cláusula `ORDER BY`.

Por exemplo, ao adicionar as propriedades no filtro à cláusula ORDER BY, a consulta a seguir pode ser reescrita para aproveitar um índice composto:

Consulta usando índice de intervalo:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Consultar usando índice composto:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

As mesmas otimizações de padrão e consulta podem ser generalizadas para consultas com vários filtros de igualdade:

Consulta usando índice de intervalo:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Consultar usando índice composto:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

As seguintes considerações são usadas ao criar índices compostos para otimizar uma consulta com uma cláusula Filter e `ORDER BY`:

* Se a consulta filtrar em Propriedades, elas deverão ser incluídas primeiro na cláusula `ORDER BY`.
* Se você não definir um índice composto em uma consulta com um filtro em uma propriedade e uma cláusula de `ORDER BY` separada usando uma propriedade diferente, a consulta ainda terá sucesso. No entanto, o custo de RU da consulta pode ser reduzido com um índice composto, especialmente se a propriedade na cláusula `ORDER BY` tiver uma alta cardinalidade.
* Todas as considerações para a criação de índices compostos para consultas de `ORDER BY` com várias propriedades, bem como consultas com filtros em várias propriedades ainda se aplicam.


| **Índice composto**                      | **Consulta de `ORDER BY` de exemplo**                                  | **Com suporte do índice composto?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Modificando a política de indexação

A política de indexação de um contêiner pode ser atualizada a qualquer momento [usando o portal do Azure ou um dos SDKs com suporte](how-to-manage-indexing-policy.md). Uma atualização para a política de indexação dispara uma transformação do índice antigo para o novo, que é executado online e em vigor (portanto, nenhum espaço de armazenamento adicional é consumido durante a operação). O índice antigo da política é transformado com eficiência na nova política sem afetar a disponibilidade de gravação ou a taxa de transferência provisionada no contêiner. A transformação de índice é uma operação assíncrona e o tempo necessário para concluir depende da taxa de transferência provisionada, do número de itens e de seu tamanho.

> [!NOTE]
> Ao adicionar um intervalo ou índice espacial, as consultas podem não retornar todos os resultados correspondentes e farão isso sem retornar erros. Isso significa que os resultados da consulta podem não ser consistentes até que a transformação do índice seja concluída. É possível acompanhar o progresso da transformação de índice [usando um dos SDKs](how-to-manage-indexing-policy.md).

Se o modo da nova política de indexação for definido como consistente, nenhuma outra alteração de política de indexação poderá ser aplicada enquanto a transformação do índice estiver em andamento. Uma transformação índice em execução pode ser cancelada definindo o modo da política de indexação como None (o que descartará imediatamente o índice).

## <a name="indexing-policies-and-ttl"></a>Políticas de indexação e TTL

O [recurso TTL (vida útil)](time-to-live.md) requer que a indexação esteja ativa no contêiner em que está ativada. Isso significa que:

- Não é possível ativar o TTL em um contêiner em que o modo de indexação está definido como nenhum,
- Não é possível definir o modo de indexação como None em um contêiner em que TTL está ativado.

Para cenários em que nenhum caminho de propriedade precisa ser indexado, mas o TTL é necessário, você pode usar uma política de indexação com:

- um modo de indexação definido como consistente e
- nenhum caminho incluído e
- `/*` como o único caminho excluído.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre a indexação nos seguintes artigos:

- [Visão geral da indexação](index-overview.md)
- [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)
