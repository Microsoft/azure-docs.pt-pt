---
title: Políticas de indexação de DB Azure Cosmos
description: Aprenda a configurar e alterar a política de indexação padrão para indexação automática e maior desempenho em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 9e62d6c475a4aeb366d034af1c80fc728f1a9211
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335828"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Políticas de indexação no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

No Azure Cosmos DB, cada contentor tem uma política de indexação que determina como os itens do contentor devem ser indexados. A política de indexação predefinida para os contentores recém-criados indexa todas as propriedades de cada item e aplica índices de intervalo a qualquer cadeia ou número. Isto permite-lhe obter um bom desempenho de consulta sem ter que pensar em indexar e gestão de índices antecipadamente.

Em algumas situações, poderá querer substituir este comportamento automático para se adequar melhor aos requisitos. Pode personalizar a política de indexação de um recipiente definindo o seu *modo de indexação,* e incluir ou excluir *caminhos de propriedade*.

> [!NOTE]
> O método de atualização das políticas de indexação descritas neste artigo aplica-se apenas à API SQL (Core) da Azure Cosmos DB. Saiba mais sobre a indexação na [API da Azure Cosmos para a MongoDB](mongodb-indexing.md)

## <a name="indexing-mode"></a>Modo de indexação

AZure Cosmos DB suporta dois modos de indexação:

- **Consistente** : O índice é atualizado sincronizadamente à medida que cria, atualiza ou apaga itens. Isto significa que a consistência das suas consultas de leitura será a [consistência configurada para a conta](consistency-levels.md).
- **Nenhum** : A indexação é desativada no recipiente. Isto é comumente usado quando um recipiente é usado como uma loja de valor-chave pura sem a necessidade de índices secundários. Também pode ser usado para melhorar o desempenho das operações a granel. Após a conclusão das operações a granel, o modo de índice pode ser definido como Consistente e depois monitorizado utilizando o [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) até estar concluído.

> [!NOTE]
> AZure Cosmos DB também suporta um modo de indexação preguiçoso. A indexação em diferido executa atualizações ao índice com um nível de prioridade muito menor quando o motor não está a realizar qualquer outro trabalho. Tal poderá levar a resultados de consulta **inconsistentes ou incompletos**. Se planear consultar um contentor do Cosmos, não deverá selecionar a indexação em diferido. Os novos recipientes não podem selecionar uma indexação preguiçosa. Pode solicitar uma isenção contactando o [suporte Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) (exceto se estiver a utilizar uma conta Azure Cosmos em modo [sem servidor,](serverless.md) que não suporta uma indexação preguiçosa).

Por predefinição, a política de indexação está definida para `automatic` . É conseguido colocando a `automatic` propriedade na política de indexação para `true` . Configurar esta propriedade para `true` permitir que a Azure CosmosDB indexe automaticamente os documentos à medida que estão escritos.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Incluindo e excluindo caminhos imobiliários

Uma política de indexação personalizada pode especificar caminhos de propriedade que são explicitamente incluídos ou excluídos da indexação. Ao otimizar o número de caminhos indexados, pode reduzir substancialmente a latência e a carga RU das operações de escrita. Estes caminhos são definidos seguindo [o método descrito na secção de visão geral de indexação](index-overview.md#from-trees-to-property-paths) com as seguintes adições:

- um caminho que conduz a um valor escalar (string ou número) termina com `/?`
- os elementos de uma matriz são endereçados em conjunto através da `/[]` notação (em vez `/0` de, `/1` etc.)
- o `/*` wildcard pode ser usado para combinar com quaisquer elementos abaixo do nó

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

- o `headquarters` caminho é `employees``/headquarters/employees/?`

- o `locations` `country` caminho é `/locations/[]/country/?`

- o caminho para qualquer coisa sob `headquarters` é `/headquarters/*`

Por exemplo, podemos incluir o `/headquarters/employees/?` caminho. Este caminho garantiria que indexamos a propriedade dos colaboradores, mas não indexariamos json adicional aninhado dentro desta propriedade.

## <a name="includeexclude-strategy"></a>Incluir/excluir estratégia

Qualquer política de indexação tem de incluir o caminho da raiz `/*` como um caminho incluído ou um caminho excluído.

- Inclua o caminho da raiz para excluir seletivamente caminhos que não precisam de ser indexados. Esta é a abordagem recomendada pois permite que a Azure Cosmos DB indexe proativamente qualquer nova propriedade que possa ser adicionada ao seu modelo.
- Excluir o caminho da raiz para incluir seletivamente caminhos que precisam de ser indexados.

- Para caminhos com caracteres regulares que incluem: caracteres alfanuméricos e _ (sublinhado), não é preciso escapar da corda do caminho em torno de citações duplas (por exemplo, "/caminho/?"). Para caminhos com outros caracteres especiais, é necessário escapar da corda do caminho em torno de citações duplas (por exemplo, "/ \" caminho-abc \" /?"). Se espera personagens especiais no seu caminho, pode escapar de todos os caminhos por segurança. Funcionalmente, não faz diferença se escapares de todos os caminhos vs apenas aqueles que têm personagens especiais.

- A propriedade do sistema `_etag` está excluída da indexação por defeito, a menos que o etag seja adicionado à trajetória incluída para a indexação.

- Se o modo de indexação for definido de **forma consistente,** as propriedades do sistema `id` são automaticamente `_ts` indexadas.

Ao incluir e excluir caminhos, poderá encontrar os seguintes atributos:

- `kind` pode ser `range` `hash` ou. O suporte do índice de haxixe está limitado a filtros de igualdade. A funcionalidade do índice de gama fornece toda a funcionalidade dos índices de haxixe, bem como uma triagem eficiente, filtros de gama, funções do sistema. Recomendamos sempre a utilização de um índice de intervalo.

- `precision` é um número definido ao nível do índice para caminhos incluídos. Um valor `-1` de indica a máxima precisão. Recomendamos sempre definir este valor para `-1` .

- `dataType` pode ser `String` `Number` ou. Isto indica os tipos de propriedades JSON que serão indexadas.

Quando não especificadas, estas propriedades terão os seguintes valores predefinidos:

| **Nome da propriedade**     | **Valor Predefinido** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` e `Number` |

Consulte [esta secção](how-to-manage-indexing-policy.md#indexing-policy-examples) para indexar exemplos de política para incluir e excluir caminhos.

## <a name="includeexclude-precedence"></a>Incluir/excluir precedência

Se os seus caminhos incluídos e caminhos excluídos tiverem um conflito, o caminho mais preciso tem precedência.

Eis um exemplo:

**Caminho Incluído:**`/food/ingredients/nutrition/*`

**Caminho excluído:**`/food/ingredients/*`

Neste caso, o caminho incluído tem precedência sobre o caminho excluído porque é mais preciso. Com base nestes caminhos, quaisquer dados no `food/ingredients` caminho ou aninhados dentro seriam excluídos do índice. A exceção seria dados dentro do caminho incluído: `/food/ingredients/nutrition/*` , que seria indexado.

Aqui estão algumas regras para a precedência de caminhos incluídos e excluídos em Azure Cosmos DB:

- Caminhos mais profundos são mais precisos do que caminhos mais estreitos. por exemplo: `/a/b/?` é mais preciso do que `/a/?` .

- O `/?` é mais preciso do que `/*` . Por exemplo, `/a/?` é mais preciso do que tem `/a/*` `/a/?` precedência.

- O caminho `/*` deve ser um caminho incluído ou um caminho excluído.

## <a name="spatial-indexes"></a>Índices espaciais

Quando se define um caminho espacial na política de indexação, deve definir qual o índice que ```type``` deve ser aplicado a esse caminho. Os tipos possíveis para índices espaciais incluem:

* Ponto

* Polígono

* MultiPolygon

* LineString

Azure Cosmos DB, por padrão, não criará quaisquer índices espaciais. Se você gostaria de usar funções espaciais SQL incorporadas, você deve criar um índice espacial sobre as propriedades necessárias. Consulte [esta secção](sql-query-geospatial-index.md) para indexar exemplos de política para adicionar índices espaciais.

## <a name="composite-indexes"></a>Índices compostos

Consultas que têm uma `ORDER BY` cláusula com duas ou mais propriedades requerem um índice composto. Também pode definir um índice composto para melhorar o desempenho de muitas consultas de igualdade e alcance. Por predefinição, não são definidos índices compostos, pelo que deve [adicionar índices compostos](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) conforme necessário.

Ao contrário de caminhos incluídos ou excluídos, não é possível criar um caminho com o `/*` wildcard. Cada caminho composto tem um implícito `/?` no final do caminho que não precisa de especificar. Os caminhos compostos conduzem a um valor escalar e este é o único valor que está incluído no índice composto.

Ao definir um índice composto, especifica:

- Dois ou mais caminhos de propriedade. A sequência em que os caminhos da propriedade são definidos importa.

- A ordem (ascendente ou descendente).

> [!NOTE]
> Quando adicionar um índice composto, a consulta utilizará os índices de gama existentes até que a nova adição do índice composto esteja completa. Portanto, quando adicionar um índice composto, não poderá observar imediatamente melhorias de desempenho. É possível acompanhar o progresso da transformação do índice [utilizando um dos SDKs.](how-to-manage-indexing-policy.md)

### <a name="order-by-queries-on-multiple-properties"></a>ENCOMENDA POR consultas sobre múltiplas propriedades:

As seguintes considerações são utilizadas quando se utilizam índices compostos para consultas com uma `ORDER BY` cláusula com duas ou mais propriedades:

- Se os percursos do índice composto não corresponderem à sequência das propriedades na `ORDER BY` cláusula, então o índice composto não pode suportar a consulta.

- A ordem dos percursos de índice composto (ascendente ou descendente) também deve corresponder `order` à `ORDER BY` cláusula.

- O índice composto também suporta uma `ORDER BY` cláusula com a ordem oposta em todos os caminhos.

Considere o seguinte exemplo quando um índice composto é definido no nome, idade e _ts:

| **Índice Composto**     | **`ORDER BY`Consulta de amostras**      | **Suportado por Índice Composto?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Deve personalizar a sua política de indexação para que possa servir todas as `ORDER BY` consultas necessárias.

### <a name="queries-with-filters-on-multiple-properties"></a>Consultas com filtros em várias propriedades

Se uma consulta tiver filtros em duas ou mais propriedades, pode ser útil criar um índice composto para estas propriedades.

Por exemplo, considere a seguinte consulta que tem um filtro de igualdade em duas propriedades:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Esta consulta será mais eficiente, demorando menos tempo e consumindo menos RU's, se for capaz de alavancar um índice composto em (nome ASC, idade ASC).

Consultas com filtros de gama também podem ser otimizadas com um índice composto. No entanto, a consulta só pode ter um filtro de gama única. Os filtros de gama `>` incluem, , , e `<` `<=` `>=` `!=` . O filtro de gama deve ser definido por último no índice composto.

Considere a seguinte consulta com filtros de igualdade e gama:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Esta consulta será mais eficiente com um índice composto em (nome ASC, idade ASC). No entanto, a consulta não utilizaria um índice composto em (asC de idade, nome ASC) porque os filtros de igualdade devem ser definidos primeiro no índice composto.

As seguintes considerações são usadas ao criar índices compostos para consultas com filtros em várias propriedades

- As propriedades do filtro da consulta devem coincidir com as do índice composto. Se uma propriedade estiver no índice composto mas não estiver incluída na consulta como um filtro, a consulta não utilizará o índice composto.
- Se uma consulta tiver propriedades adicionais no filtro que não foram definidas num índice composto, então uma combinação de índices compósitos e de intervalo será usada para avaliar a consulta. Isto requer menos RU's do que exclusivamente usando índices de gama.
- Se uma propriedade tiver um filtro de alcance ( `>` , , , , ou ), `<` `<=` `>=` `!=` então esta propriedade deve ser definida por último no índice composto. Se uma consulta tiver mais de um filtro de gama, não utilizará o índice composto.
- Ao criar um índice composto para otimizar consultas com múltiplos filtros, `ORDER` o índice composto não terá qualquer impacto nos resultados. Esta propriedade é opcional.
- Se não definir um índice composto para uma consulta com filtros em várias propriedades, a consulta continuará a ter sucesso. No entanto, o custo RU da consulta pode ser reduzido com um índice composto.
- As consultas com ambos os agregados (por exemplo, COUNT ou SUM) e filtros também beneficiam de índices compostos.

Considere os seguintes exemplos em que um índice composto é definido no nome, idade e marca de tempo das propriedades:

| **Índice Composto**     | **Consulta de amostras**      | **Suportado por Índice Composto?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name ASC, age ASC)```   | ```SELECT COUNT(1) FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Consultas com um filtro e com uma cláusula ORDER BY

Se uma consulta filtrar uma ou mais propriedades e tiver propriedades diferentes na cláusula ORDER BY, pode ser útil adicionar as propriedades no filtro à `ORDER BY` cláusula.

Por exemplo, adicionando as propriedades no filtro à cláusula ORDER BY, a seguinte consulta poderia ser reescrita para alavancar um índice composto:

Consulta utilizando índice de gama:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Consulta utilizando índice composto:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

As mesmas otimizações de padrão e consulta podem ser generalizadas para consultas com filtros de igualdade múltiplas:

Consulta utilizando índice de gama:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Consulta utilizando índice composto:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

As seguintes considerações são utilizadas na criação de índices compósitos para otimizar uma consulta com um filtro e `ORDER BY` cláusula:

* Se a consulta filtrar as propriedades, estas devem ser incluídas primeiro na `ORDER BY` cláusula.
* Se a consulta filtrar várias propriedades, os filtros de igualdade devem ser as primeiras propriedades na `ORDER BY` cláusula
* Se não definir um índice composto numa consulta com um filtro numa propriedade e uma cláusula separada `ORDER BY` usando uma propriedade diferente, a consulta continuará a ter sucesso. No entanto, o custo ru da consulta pode ser reduzido com um índice composto, particularmente se a propriedade na `ORDER BY` cláusula tiver uma cardinalidade elevada.
* Todas as considerações para a criação de índices compósitos para `ORDER BY` consultas com múltiplas propriedades, bem como consultas com filtros em várias propriedades ainda se aplicam.


| **Índice Composto**                      | **`ORDER BY`Consulta de amostras**                                  | **Suportado por Índice Composto?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" AND c.timestamp > 1589840355 ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT * FROM c WHERE c.timestamp > 1589840355 AND c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Modificação da política de indexação

A política de indexação de um contentor pode ser atualizada a qualquer momento [utilizando o portal Azure ou um dos SDKs suportados](how-to-manage-indexing-policy.md). Uma atualização da política de indexação desencadeia uma transformação do índice antigo para o novo, que é realizado online e no local (pelo que não é consumido nenhum espaço adicional de armazenamento durante a operação). A velha política de indexação é eficientemente transformada para a nova política sem afetar a disponibilidade de escrita, a disponibilidade de leitura ou a produção prevista no contentor. A transformação do índice é uma operação assíncronea, e o tempo que leva para completar depende da produção prevista, do número de itens e do seu tamanho.

> [!IMPORTANT]
> A transformação do índice é uma operação que consome [Unidades de Pedido.](request-units.md) As unidades de pedido consumidas por uma transformação de índice não são atualmente faturadas se estiver a utilizar recipientes [sem servidor.](serverless.md) Estas Unidades de Pedido serão faturadas assim que o servidor não estiver disponível.

> [!NOTE]
> É possível acompanhar o progresso da transformação do índice [utilizando um dos SDKs.](how-to-manage-indexing-policy.md)

Não há impacto em escrever disponibilidade durante quaisquer transformações de índice. A transformação do índice utiliza as suas RUs provisões, mas com uma prioridade inferior à das suas operações ou consultas CRUD.

Não há impacto em ler disponibilidade ao adicionar um novo índice. As consultas só utilizarão novos índices uma vez que a transformação do índice esteja completa. Durante a transformação do índice, o motor de consulta continuará a utilizar os índices existentes, pelo que observará um desempenho de leitura semelhante durante a transformação de indexação ao que tinha observado antes de iniciar a alteração de indexação. Ao adicionar novos índices, também não existe o risco de resultados de consulta incompletos ou inconsistentes.

Ao remover índices e executar imediatamente consultas que filtram nos índices caídos, não há garantia de resultados de consulta consistentes ou completos. Se remover vários índices e o fizer numa única alteração de política de indexação, o motor de consulta fornece resultados consistentes e completos ao longo da transformação do índice. No entanto, se remover índices através de múltiplas alterações de política de indexação, o motor de consulta não fornecerá resultados consistentes ou completos até que todas as transformações do índice sejam concluídas. A maioria dos desenvolvedores não baixa índices e, em seguida, imediatamente tenta executar consultas que utilizam estes índices para que, na prática, esta situação seja improvável.

> [!NOTE]
> Sempre que possível, deve sempre tentar agrupar múltiplas alterações de indexação numa única modificação da política de indexação

## <a name="indexing-policies-and-ttl"></a>Políticas de indexação e TTL

A utilização da [função Time-to-Live (TTL)](time-to-live.md) requer indexação. Isto significa que:

- não é possível ativar o TTL num recipiente onde o modo de indexação está definido para `none` ,
- não é possível definir o modo de indexação a Nenhum num recipiente onde o TTL é ativado.

Para cenários em que não é necessário indexar nenhuma trajetória imobiliária, mas o TTL é necessário, pode utilizar uma política de indexação com um modo de indexação definido para `consistent` , sem caminhos incluídos, e `/*` como o único caminho excluído.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre a indexação nos seguintes artigos:

- [Descrição geral da indexação](index-overview.md)
- [Como gerir a política de indexação](how-to-manage-indexing-policy.md)
