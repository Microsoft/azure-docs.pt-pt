---
title: Usando loops T-SQL
description: Dicas para a utilização de loops T-SQL, substituição de cursores e desenvolvimento de soluções relacionadas com piscina SQL em Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: baff2806b1a8c3c99546365c2496238c24b2b243
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429957"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>Utilização de loops T-SQL em Synapse SQL
Este artigo fornece-lhe dicas essenciais para a utilização de loops T-SQL, substituindo os cursores e desenvolvendo soluções relacionadas com piscina SQL em Synapse SQL.

## <a name="purpose-of-while-loops"></a>Propósito dos loops WHILE

Synapse SQL suporta o loop [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) para executar repetidamente blocos de declaração. Este loop WHILE continua enquanto as condições especificadas forem verdadeiras ou até que o código termine especificamente o loop usando a palavra-chave BREAK. 

Os loops na piscina SQL são úteis para substituir os cursores definidos no código SQL. Felizmente, quase todos os cursores que estão escritos no código SQL são da variedade de leitura rápida. Assim, os loops [WHILE] são uma excelente alternativa para substituir os cursores.

## <a name="replacing-cursors-in-sql-pool"></a>Substituição de cursores em piscina SQL

Antes de mergulhar, deve considerar-se a seguinte pergunta: "Este cursor pode ser reescrito para utilizar operações baseadas em set?" Em muitos casos, a resposta é sim e é frequentemente a melhor abordagem. Uma operação baseada em conjunto executa frequentemente mais rápido do que uma abordagem iterativa, de linha a linha.

Os cursores de leitura para a frente são facilmente substituídos por uma construção em loop. O seguinte código é um exemplo simples. Este exemplo de código atualiza as estatísticas de cada tabela da base de dados. Ao iterar sobre as mesas no loop, cada comando executa em sequência.

Em primeiro lugar, crie uma tabela temporária contendo um número único de linha utilizado para identificar as declarações individuais:

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

Em segundo lugar, inicializar as variáveis necessárias para executar o ciclo:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Agora, passe por cima de declarações executando-as uma de cada vez:

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

Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](develop-overview.md)
