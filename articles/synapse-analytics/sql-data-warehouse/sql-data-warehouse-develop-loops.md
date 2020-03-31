---
title: Usando loops T-SQL
description: Dicas para utilizar loops T-SQL e substituir cursores no Azure SQL Data Warehouse para desenvolver soluções.
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
ms.openlocfilehash: afb2160cb9b4e34d3d17db86bac9cd3be79886d0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351596"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Utilização de loops T-SQL no Armazém de Dados SQL
Dicas para utilizar loops T-SQL e substituir cursores no Azure SQL Data Warehouse para desenvolver soluções.

## <a name="purpose-of-while-loops"></a>Propósito dos loops WHILE

O SQL Data Warehouse suporta o loop [WHILE](/sql/t-sql/language-elements/while-transact-sql) para executar repetidamente blocos de declaração. Este loop WHILE continua enquanto as condições especificadas forem verdadeiras ou até que o código termine especificamente o loop usando a palavra-chave BREAK. Os loops são úteis para substituir os cursores definidos no código SQL. Felizmente, quase todos os cursores que estão escritos no código SQL são da variedade de leitura rápida. Portanto, os loops [WHILE] são uma excelente alternativa para substituir os cursores.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Substituição de cursores no Armazém de Dados SQL
No entanto, antes de mergulhar na cabeça primeiro, deve perguntar a si mesmo a seguinte pergunta: "Será que este cursor pode ser reescrito para usar operações baseadas em set?." Em muitos casos, a resposta é sim e muitas vezes é a melhor abordagem. Uma operação baseada em conjunto geralmente funciona mais rápido do que uma abordagem iterativa, linha a linha.

Os cursores de leitura para a frente rápidos podem ser facilmente substituídos por uma construção em loop. O seguinte é um exemplo simples. Este exemplo de código atualiza as estatísticas de cada tabela da base de dados. Ao iterar sobre as mesas no loop, cada comando executa em sequência.

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

