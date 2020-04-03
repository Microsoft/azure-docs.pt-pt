---
title: Usando loops T-SQL
description: Dicas para o desenvolvimento de soluções utilizando loops T-SQL e substituindo os cursores na piscina SYnapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4cec4801f2a15ebf858f50377c9718fdacac4e29
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619008"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Usando loops T-SQL na piscina Synapse SQL
Incluídos neste artigo estão dicas para o desenvolvimento da solução de piscina SQL utilizando loops T-SQL e substituindo os cursores.

## <a name="purpose-of-while-loops"></a>Propósito dos loops WHILE

A piscina Synapse SQL suporta o loop [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) para executar repetidamente blocos de declaração. Este loop WHILE continua enquanto as condições especificadas forem verdadeiras ou até que o código termine especificamente o loop usando a palavra-chave BREAK. 

Os loops são úteis para substituir os cursores definidos no código SQL. Felizmente, quase todos os cursores que estão escritos no código SQL são da variedade de leitura rápida. Assim, loops WHILE são uma ótima alternativa para substituir os cursores.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Substituição de cursores na piscina Synapse SQL
No entanto, antes de mergulhar na cabeça primeiro, deve perguntar a si mesmo a seguinte pergunta: "Será que este cursor pode ser reescrito para usar operações baseadas em set?" 

Em muitos casos, a resposta é sim e é frequentemente a melhor abordagem. Uma operação baseada em conjunto geralmente funciona mais rápido do que uma abordagem iterativa, linha a linha.

Os cursores de leitura para a frente rápidos podem ser facilmente substituídos por uma construção em loop. O exemplo que se segue é simples. Este exemplo de código atualiza as estatísticas de cada tabela da base de dados. Ao iterar sobre as mesas no loop, cada comando executa em sequência.

Em primeiro lugar, crie uma tabela temporária contendo um número único de linha utilizado para identificar as declarações individuais:

```
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

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Agora, passe por cima de declarações executando-as uma de cada vez:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Finalmente deixe cair a tabela temporária criada no primeiro passo

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)

