---
title: Pré-visualização do armazenamento de mesas colunar - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Comprimindo dados utilizando o armazenamento de colunas (pré-visualização)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024005"
---
# <a name="columnar-table-storage-preview"></a>Armazenamento de mesas colunar (pré-visualização)

> [!IMPORTANT]
> O armazenamento da mesa colunar em Hyperscale (Citus) está atualmente em pré-visualização. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
>
> Pode ver uma lista completa de outras novidades em [funcionalidades de pré-visualização para Hyperscale (Citus)](hyperscale-preview-features.md).

A Base de Dados Azure para PostgreSQL - Hiperescala (Citus) suporta o armazenamento de mesas de coluna apenas para cargas de trabalho de armazenamento de dados e analíticos e de armazenamento de dados. Quando as colunas (em vez de linhas) são armazenadas contíguas no disco, os dados tornam-se mais compressíveis, e as consultas podem solicitar um subconjunto de colunas mais rapidamente.

Para utilizar o armazenamento de colunas, especifique `USING columnar` ao criar uma tabela:

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

A hiperescala (Citus) converte linhas para armazenamento colunar em "listras" durante a inserção. Cada risca contém dados de uma transação, ou 150000 linhas, o que for menor.  (O tamanho das listras e outros parâmetros de uma tabela colunar podem ser alterados com a função [alter_columnar_table_set.)](reference-hyperscale-functions.md#alter_columnar_table_set)

Por exemplo, a seguinte declaração coloca todas as cinco linhas na mesma faixa, porque todos os valores são inseridos numa única transação:

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

É melhor fazer listras grandes quando possível, porque a Hyperscale (Citus) comprime dados colunares separadamente por listra. Podemos ver fatos sobre a nossa tabela colunar como taxa de compressão, número de listras e filas médias por listra `VACUUM VERBOSE` usando:

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

A saída mostra que a Hyperscale (Citus) usou o algoritmo de compressão zstd para obter uma compressão de dados de 1,31x. A taxa de compressão compara a) o tamanho dos dados inseridos tal como foi encenado na memória contra b) o tamanho desses dados comprimidos na sua eventual risca.

Devido à forma como é medido, a taxa de compressão pode ou não corresponder à diferença de tamanho entre a linha e o armazenamento colunar para uma mesa. A única maneira de realmente encontrar essa diferença é construir uma tabela de linha e colunar que contenha os mesmos dados, e comparar:

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

Para a nossa minúscula tabela, o armazenamento colunar realmente usa mais espaço, mas à medida que os dados crescem, a compressão vai ganhar.

## <a name="example"></a>Exemplo

O armazenamento colunar funciona bem com a divisória de mesa. Por exemplo, consulte a documentação comunitária do Citus Engine, [arquivando com armazenamento de colunas.](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage)

## <a name="gotchas"></a>Gotchas

* Comprimem compressas de armazenamento colunar por listra. As riscas são criadas por transação, por isso a inserção de uma linha por transação colocará linhas únicas nas suas próprias riscas. Compressão e desempenho de riscas de linha única serão piores do que uma mesa de linha. Insira sempre a granel numa mesa de colunar.
* Se fizeres asneira e forres um monte de riscas minúsculas, ficas preso.
  A única correção é criar uma nova tabela colunar e copiar dados do original numa transação:
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* Os dados fundamentalmente não compressíveis podem ser um problema, embora o armazenamento de colunares ainda seja útil na seleção de colunas específicas. Não precisa de colocar as outras colunas na memória.
* Numa mesa dividida com uma mistura de divisórias de linha e colunas, as atualizações devem ser cuidadosamente direcionadas. Filtre-os para atingir apenas as divisórias de linha.
   * Se a operação for direcionada a uma partição de linha específica (por `UPDATE p2 SET i = i + 1` exemplo, ), terá sucesso; se for direcionada a uma partição colunar especificada (por `UPDATE p1 SET i = i + 1` exemplo, ), falhará.
   * Se a operação for direcionada para a mesa dividida e tiver uma cláusula WHERE que exclui todas as divisórias colunares (por `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'` exemplo), terá sucesso.
   * Se a operação for direcionada para a mesa partida, mas não filtrar as colunas das chaves de partição, falhará. Mesmo que existam cláusulas WHERE que correspondem a linhas apenas em divisórias colunares, não é suficiente-- a chave de partição também deve ser filtrada.

## <a name="limitations"></a>Limitações

Esta funcionalidade ainda possui uma série de limitações significativas. Ver [limites e limitações de Hiperescala (Citus).](concepts-hyperscale-limits.md#columnar-storage)

## <a name="next-steps"></a>Passos seguintes

* Veja um exemplo de armazenamento colunar num [tutorial de horários](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) do Citus (ligação externa).
