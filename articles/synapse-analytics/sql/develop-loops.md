---
title: Use laços T-SQL
description: Dicas para a utilização de laços T-SQL, substituição de cursores e desenvolvimento de soluções relacionadas com o Synapse SQL em Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0d83e1305a851bf6bafb6c4c79f5caf73f8e44b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120891"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>Use laços T-SQL com Sinaapse SQL em Azure Synapse Analytics

Este artigo fornece-lhe dicas essenciais para a utilização de laços T-SQL, substituição de cursores e desenvolvimento de soluções relacionadas com o Synapse SQL.

## <a name="purpose-of-while-loops"></a>Finalidade dos ciclos DO WHILE

O SQL de Sinapse suporta o loop [WHILE](/sql/t-sql/language-elements/while-transact-sql?preserve-view=true&view=sql-server-ver15) para executar repetidamente blocos de declaração. Este laço WHILE continua enquanto as condições especificadas forem verdadeiras ou até que o código encerre especificamente o loop utilizando a palavra-chave BREAK. 

Os loops em Synapse SQL são úteis para substituir cursores definidos no código SQL. Felizmente, quase todos os cursores que estão escritos no código SQL são da variedade rápida e só de leitura. Assim, os loops ENQUANTO são uma ótima alternativa para substituir cursores.

## <a name="replace-cursors-in-synapse-sql"></a>Substitua os cursores em Synapse SQL

Antes de mergulhar, a seguinte pergunta deve ser considerada: "Poderia este cursor ser reescrito para usar operações baseadas em conjunto?" Em muitos casos, a resposta é sim e é frequentemente a melhor abordagem. Uma operação baseada em conjunto executa frequentemente mais rápido do que uma abordagem iterativa, linha a linha.

Os cursores de leitura rápidos para a frente são facilmente substituídos por uma construção em loop. O seguinte código é um exemplo simples. Este exemplo de código atualiza as estatísticas de cada tabela da base de dados. Ao iterar sobre as tabelas no loop, cada comando executa em sequência.

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

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](develop-overview.md)