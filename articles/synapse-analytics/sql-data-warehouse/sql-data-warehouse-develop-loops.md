---
title: Usando laços T-SQL
description: Dicas para o desenvolvimento de soluções utilizando laços T-SQL e substituindo cursores na piscina Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 25dad01a54b6ffe08656379340f58e0fe70ec666
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213419"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Utilizando laços T-SQL na piscina Sinaapse SQL

Incluído neste artigo estão dicas para o desenvolvimento da solução de piscina SQL usando laços T-SQL e substituindo cursores.

## <a name="purpose-of-while-loops"></a>Finalidade dos ciclos DO WHILE

A piscina Synapse SQL suporta o loop [WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para executar repetidamente blocos de declaração. Este laço WHILE continua enquanto as condições especificadas forem verdadeiras ou até que o código encerre especificamente o loop utilizando a palavra-chave BREAK.

Os loops são úteis para substituir cursores definidos no código SQL. Felizmente, quase todos os cursores que estão escritos no código SQL são da variedade rápida e só de leitura. Assim, os loops ENQUANTO são uma ótima alternativa para substituir cursores.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Substituição de cursores na piscina Synapse SQL

No entanto, antes de mergulhar de cabeça primeiro, deve perguntar a si mesmo a seguinte pergunta: "Poderia este cursor ser reescrito para usar operações baseadas em conjunto?"

Em muitos casos, a resposta é sim e é frequentemente a melhor abordagem. Uma operação baseada em conjunto realiza frequentemente mais rápido do que uma abordagem iterativa, linha a linha.

Cursores de leitura rápidos para a frente podem ser facilmente substituídos por uma construção em loop. O exemplo a seguir é simples. Este exemplo de código atualiza as estatísticas de cada tabela da base de dados. Ao iterar sobre as tabelas no loop, cada comando executa em sequência.

Primeiro, crie uma tabela temporária contendo um número de linha único usado para identificar as declarações individuais:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Em segundo lugar, inicialize as variáveis necessárias para executar o loop:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Agora loop sobre declarações executando-as uma de cada vez:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Finalmente deixe cair a tabela temporária criada no primeiro passo

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
