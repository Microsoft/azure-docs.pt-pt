---
title: Políticas de indexação do Azure Cosmos DB
description: Aprenda a configurar e alterar a política de indexação padrão para indexação automática e maior desempenho em Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 86dbcee7150adacd0e961dbe07cf66ad117d2041
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252002"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Políticas de indexação em Azure Cosmos DB

Em Azure Cosmos DB, cada contentor tem uma política de indexação que dita como os itens do contentor devem ser indexados. A política de indexação padrão para contentores recém-criados indexa cada propriedade de cada item, aplicando índices de gama para qualquer string ou número, e índices espaciais para qualquer objeto GeoJSON do tipo Point. Isto permite-lhe obter um alto desempenho de consulta sem ter que pensar em indexação e gestão de índices antecipadamente.

Em algumas situações, poderá querer substituir este comportamento automático para se adequar melhor aos requisitos. Pode personalizar a política de indexação de um contentor definindo o seu modo de *indexação,* e incluir ou excluir caminhos de *propriedade.*

> [!NOTE]
> O método de atualização das políticas de indexação descritas neste artigo aplica-se apenas à API SQL (Core) da Azure Cosmos DB.

## <a name="indexing-mode"></a>Modo de indexação

O Azure Cosmos DB suporta dois modos de indexação:

- **Consistente**: O índice é atualizado sincronizadamente à medida que cria, atualiza ou elimina itens. Isto significa que a consistência das suas consultas de leitura será a [consistência configurada para a conta](consistency-levels.md).
- **Nenhuma**: A indexação é desativada no recipiente. Isto é comumente usado quando um recipiente é usado como uma loja de valor-chave puro sem a necessidade de índices secundários. Também pode ser usado para melhorar o desempenho das operações a granel. Após as operações a granel estarem concluídas, o modo de índice pode ser definido para Consistente e, em seguida, monitorizado utilizando o [IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) até estar concluído.

> [!NOTE]
> Cosmos DB também suporta um modo de indexação preguiçoso. A indexação preguiçosa executa atualizações ao índice a um nível de prioridade muito mais baixo quando o motor não está a fazer qualquer outro trabalho. Isto pode resultar em resultados de consulta **inconsistentes ou incompletos.** Além disso, a utilização de indexação preguiçosa em vez de "Nenhuma" para operações a granel também não beneficia, uma vez que qualquer alteração no Modo Indexizador fará com que o índice seja retirado e recriado. Por estas razões recomendamos que os clientes a utilizem. Para melhorar o desempenho das operações a granel, desajuste o modo de índice para Nenhum, em seguida, volte ao modo Consistente e monitorize a propriedade `IndexTransformationProgress` no recipiente até estar completo.

Por predefinição, a política de indexação está definida para `automatic`. É conseguido definindo a propriedade `automatic` na política de indexação para `true`. A definição desta propriedade para `true` permite ao Azure CosmosDB indexar automaticamente os documentos à medida que são escritos.

## <a id="include-exclude-paths"></a>Incluindo e excluindo caminhos imobiliários

Uma política de indexação personalizada pode especificar caminhos de propriedade que estão explicitamente incluídos ou excluídos da indexação. Ao otimizar o número de caminhos indexados, pode baixar a quantidade de armazenamento utilizado pelo seu recipiente e melhorar a latência das operações de escrita. Estes caminhos são definidos seguindo o método descrito na secção de visão geral de [indexação](index-overview.md#from-trees-to-property-paths) com as seguintes adições:

- um caminho que conduz a um valor escalar (corda ou número) termina com `/?`
- elementos de uma matriz são abordados em conjunto através da notação `/[]` (em vez de `/0`, `/1` etc.)
- o wildcard `/*` pode ser usado para combinar quaisquer elementos abaixo do nó

Tomando o mesmo exemplo novamente:

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

- o caminho `employees` do `headquarters`é `/headquarters/employees/?`

- o caminho `country` `locations`é `/locations/[]/country/?`

- o caminho para qualquer coisa sob `headquarters` é `/headquarters/*`

Por exemplo, poderíamos incluir o caminho `/headquarters/employees/?`. Este caminho garantiria que indexássemos a propriedade dos empregados, mas não indexaria a JSON aninhada adicional dentro desta propriedade.

## <a name="includeexclude-strategy"></a>Incluir/excluir estratégia

Qualquer política de indexação tem de incluir o caminho raiz `/*` como um caminho incluído ou excluído.

- Inclua o caminho raiz para excluir seletivamente caminhos que não precisam de ser indexados. Esta é a abordagem recomendada, pois permite que o Azure Cosmos DB indexe proativamente qualquer nova propriedade que possa ser adicionada ao seu modelo.
- Excluir o caminho raiz para incluir seletivamente caminhos que precisam de ser indexados.

- Para caminhos com caracteres regulares que incluem: caracteres alfanuméricos e _ (sublinhado), não é preciso escapar à corda do caminho em torno de citações duplas (por exemplo, "/path/?"). Para caminhos com outros personagens especiais, você precisa escapar da corda do caminho em torno de citações duplas (por exemplo, "/\"caminho-abc\"/?). Se espera personagens especiais no seu caminho, pode escapar de todos os caminhos por segurança. Funcionalmente, não faz diferença se escapares de todos os caminhos vs apenas aqueles que têm personagens especiais.

- A propriedade do sistema "_etag" está excluída da indexação por padrão, a menos que o etag seja adicionado à trajetória incluída para indexação.

Ao incluir e excluir caminhos, poderá encontrar os seguintes atributos:

- `kind` pode ser `range` ou `hash`. A funcionalidade do índice de alcance fornece toda a funcionalidade de um índice de hash, por isso recomendamos a utilização de um índice de gama.

- `precision` é um número definido ao nível do índice para caminhos incluídos. Um valor de `-1` indica a máxima precisão. Recomendamos que este valor seja sempre configurado para `-1`.

- `dataType` pode ser `String` ou `Number`. Isto indica os tipos de propriedades JSON que serão indexadas.

Quando não especificadas, estas propriedades terão os seguintes valores predefinidos:

| **Nome da propriedade**     | **Valor Predefinido** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` e `Number` |

Consulte [esta secção](how-to-manage-indexing-policy.md#indexing-policy-examples) para indexar exemplos de políticas para incluir e excluir caminhos.

## <a name="spatial-indexes"></a>Índices espaciais

Quando definir uma trajetória espacial na política de indexação, deve definir qual o índice ```type``` deve ser aplicado a esse caminho. Os tipos possíveis para índices espaciais incluem:

* Ponto

* Polígono

* MultiPolygon

* Linha string

A Azure Cosmos DB, por defeito, não criará quaisquer índices espaciais. Se quiser utilizar funções espaciais SQL incorporadas, deve criar um índice espacial sobre as propriedades necessárias. Consulte [esta secção](geospatial.md) para indexar exemplos de políticas para adicionar índices espaciais.

## <a name="composite-indexes"></a>Índices compósitos

Consultas que têm uma cláusula `ORDER BY` com duas ou mais propriedades requerem um índice composto. Também pode definir um índice composto para melhorar o desempenho de muitas consultas de igualdade e gama. Por predefinição, não são definidos índices compostos, pelo que deve [adicionar índices compostos](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) conforme necessário.

Ao definir um índice composto, especifice:

- Dois ou mais caminhos de propriedade. A sequência em que os caminhos da propriedade são definidos importa.

- A ordem (ascendente ou descendente).

> [!NOTE]
> Quando adicionar um índice composto, a consulta utilizará os índices de gama existentes até que a nova adição de índice composto esteja completa. Portanto, ao adicionar um índice composto, pode não observar imediatamente melhorias de desempenho. É possível acompanhar o progresso da transformação de índices [utilizando um dos SDKs](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>ENCOMENDAR POR consultas sobre várias propriedades:

As seguintes considerações são utilizadas quando se utilizam índices compósitos para consultas com uma cláusula `ORDER BY` com duas ou mais propriedades:

- Se os caminhos de índice composto não corresponderem à sequência das propriedades na cláusula `ORDER BY`, então o índice composto não pode suportar a consulta.

- A ordem dos caminhos de índice composto (ascendente ou descendente) também deve corresponder ao `order` na cláusula `ORDER BY`.

- O índice composto também suporta uma cláusula de `ORDER BY` com a ordem oposta em todos os caminhos.

Considere o seguinte exemplo em que um índice composto é definido sobre o nome, idade e _ts:

| **Índice Composto**     | **Amostra `ORDER BY` Consulta**      | **Apoiado pelo Índice Composto?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Deve personalizar a sua política de indexação para que possa servir todas as consultas necessárias `ORDER BY`.

### <a name="queries-with-filters-on-multiple-properties"></a>Consultas com filtros em várias propriedades

Se uma consulta tiver filtros em duas ou mais propriedades, pode ser útil criar um índice composto para estas propriedades.

Por exemplo, considere a seguinte consulta que tem um filtro de igualdade em duas propriedades:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Esta consulta será mais eficiente, demorando menos tempo e consumindo menos RU's, se for capaz de alavancar um índice composto (nome ASC, idade ASC).

Consultas com filtros de gama também podem ser otimizadas com um índice composto. No entanto, a consulta só pode ter um filtro de alcance único. Os filtros de gama incluem `>`, `<`, `<=`, `>=`e `!=`. O filtro de alcance deve ser definido em último no índice composto.

Considere a seguinte consulta com filtros de igualdade e gama:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Esta consulta será mais eficiente com um índice composto (nome ASC, idade ASC). No entanto, a consulta não utilizaria um índice composto sobre (idade ASC, nome ASC) porque os filtros de igualdade devem ser definidos primeiro no índice composto.

As seguintes considerações são usadas na criação de índices compósitos para consultas com filtros em várias propriedades

- As propriedades do filtro da consulta devem coincidir com as do índice composto. Se uma propriedade estiver no índice composto mas não estiver incluída na consulta como filtro, a consulta não utilizará o índice composto.
- Se uma consulta tiver propriedades adicionais no filtro que não foram definidas num índice composto, então uma combinação de índices compostos e de gama será usada para avaliar a consulta. Isto exigirá menos RU's do que exclusivamente usando índices de gama.
- Se uma propriedade tiver um filtro de alcance (`>`, `<`, `<=`, `>=`, ou `!=`), então esta propriedade deve ser definida em último lugar no índice composto. Se uma consulta tiver mais de um filtro de gama, não utilizará o índice composto.
- Ao criar um índice composto para otimizar consultas com vários filtros, o `ORDER` do índice composto não terá qualquer impacto nos resultados. Esta propriedade é opcional.
- Se não definir um índice composto para uma consulta com filtros em várias propriedades, a consulta ainda terá sucesso. No entanto, o custo ru da consulta pode ser reduzido com um índice composto.

Considere os seguintes exemplos em que um índice composto é definido sobre o nome, idade e carimbo de tempo:

| **Índice Composto**     | **Consulta de amostra**      | **Apoiado pelo Índice Composto?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Consultas com um filtro e com uma cláusula ORDER BY

Se uma consulta filtrar uma ou mais propriedades e tiver propriedades diferentes na cláusula ORDER BY, pode ser útil adicionar as propriedades do filtro à cláusula `ORDER BY`.

Por exemplo, adicionando as propriedades do filtro à cláusula ORDER BY, a seguinte consulta poderia ser reescrita para alavancar um índice composto:

Consulta utilizando índice de gama:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Consulta utilizando índice composto:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

O mesmo padrão e otimizações de consultas podem ser generalizados para consultas com múltiplos filtros de igualdade:

Consulta utilizando índice de gama:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Consulta utilizando índice composto:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

As seguintes considerações são usadas ao criar índices compósitos para otimizar uma consulta com um filtro e cláusula de `ORDER BY`:

* Se a consulta filtrar as propriedades, estas devem ser incluídas primeiro na cláusula `ORDER BY`.
* Se não definir um índice composto numa consulta com um filtro numa propriedade e uma cláusula de `ORDER BY` separada usando uma propriedade diferente, a consulta ainda terá sucesso. No entanto, o custo ru da consulta pode ser reduzido com um índice composto, particularmente se a propriedade na cláusula `ORDER BY` tiver uma elevada cardinalidade.
* Todas as considerações para a criação de índices compósitos para consultas `ORDER BY` com múltiplas propriedades, bem como consultas com filtros em várias propriedades ainda se aplicam.


| **Índice Composto**                      | **Amostra `ORDER BY` Consulta**                                  | **Apoiado pelo Índice Composto?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Modificação da política de indexação

A política de indexação de um contentor pode ser atualizada a qualquer momento [utilizando o portal Azure ou um dos SDKs suportados](how-to-manage-indexing-policy.md). Uma atualização da política de indexação desencadeia uma transformação do antigo índice para o novo, que é realizado online e no lugar (por isso não é consumido nenhum espaço adicional de armazenamento durante a operação). O índice da velha política é eficientemente transformado na nova política sem afetar a disponibilidade de escrita ou o resultado previsto no recipiente. A transformação do índice é uma operação assíncrona, e o tempo que leva a concluir depende da entrada prevista, do número de itens e da sua dimensão.

> [!NOTE]
> Ao adicionar um intervalo ou índice espacial, as consultas podem não devolver todos os resultados correspondentes, e fá-lo-ão sem responder a quaisquer erros. Isto significa que os resultados da consulta podem não ser consistentes até que a transformação do índice esteja concluída. É possível acompanhar o progresso da transformação de índices [utilizando um dos SDKs](how-to-manage-indexing-policy.md).

Se o modo da nova política de indexação for definido para Consistente, nenhuma outra alteração de política de indexação pode ser aplicada enquanto a transformação do índice estiver em curso. Uma transformação de índice em execução pode ser cancelada definindo o modo da política de indexação para Nenhum (que irá imediatamente baixar o índice).

## <a name="indexing-policies-and-ttl"></a>Políticas de indexação e TTL

A [função Time-to-Live (TTL)](time-to-live.md) requer que a indexação esteja ativa no recipiente em que está ligada. Isso significa que:

- não é possível ativar o TTL num recipiente onde o modo de indexação está definido para Nenhum,
- não é possível definir o modo de indexação para Nenhum num recipiente onde a TTL é ativada.

Para cenários em que nenhuma trajetória imobiliária precisa de ser indexada, mas a TTL é necessária, pode utilizar uma política de indexação com:

- um modo de indexação definido para Consistente, e
- nenhum caminho incluído, e
- `/*` como o único caminho excluído.

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre a indexação nos seguintes artigos:

- [Visão geral de indexação](index-overview.md)
- [Como gerir a política de indexação](how-to-manage-indexing-policy.md)
