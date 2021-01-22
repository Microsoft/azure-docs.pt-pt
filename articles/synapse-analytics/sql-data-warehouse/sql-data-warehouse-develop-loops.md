---
title: Usando laços T-SQL
description: Dicas para o desenvolvimento de soluções utilizando laços T-SQL e substituição de cursores para piscinas SQL dedicadas em Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: a6c4eb98d77ece6e6ae130fd57cc263ee7e5ca64
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683227"
---
# <a name="using-t-sql-loops-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Utilização de loops T-SQL para piscinas SQL dedicadas em Azure Synapse Analytics

Incluído neste artigo estão dicas para o desenvolvimento dedicado da solução de piscina SQL usando laços T-SQL e substituindo cursores.

## <a name="purpose-of-while-loops"></a>Finalidade dos ciclos DO WHILE

Piscinas SQL dedicadas em Azure Synapse suportam o loop [WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para executar repetidamente blocos de declaração. Este laço WHILE continua enquanto as condições especificadas forem verdadeiras ou até que o código encerre especificamente o loop utilizando a palavra-chave BREAK.

Os loops são úteis para substituir cursores definidos no código SQL. Felizmente, quase todos os cursores que estão escritos no código SQL são da variedade rápida e só de leitura. Assim, os loops ENQUANTO são uma ótima alternativa para substituir cursores.

## <a name="replacing-cursors-in-dedicated-sql-pool"></a>Substituição de cursores em piscina SQL dedicada

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
