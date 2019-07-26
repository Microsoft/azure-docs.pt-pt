---
title: Usando loops T-SQL no Azure SQL Data Warehouse | Microsoft Docs
description: Dicas para usar loops T-SQL e substituir cursores no Azure SQL Data Warehouse para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e27edcc1383a235fbdb9513066e69e2f680ea2f9
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479631"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Usando loops T-SQL no SQL Data Warehouse
Dicas para usar loops T-SQL e substituir cursores no Azure SQL Data Warehouse para desenvolver soluções.

## <a name="purpose-of-while-loops"></a>Finalidade de loops WHILE

SQL Data Warehouse dá suporte ao loop [while](/sql/t-sql/language-elements/while-transact-sql) para executar repetidamente blocos de instrução. Esse loop WHILE continua desde que as condições especificadas sejam verdadeiras ou até que o código termine especificamente o loop usando a palavra-chave BREAK. Os loops são úteis para substituir cursores definidos no código SQL. Felizmente, quase todos os cursores que são escritos no código SQL são da variedade de avanço rápido e somente leitura. Portanto, os loops [WHILE] são uma ótima alternativa para substituir cursores.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Substituindo cursores em SQL Data Warehouse
No entanto, antes de mergulhar no primeiro cabeçalho, você deve fazer a seguinte pergunta: "Este cursor pode ser reescrito para usar operações baseadas em conjunto?". Em muitos casos, a resposta é sim e geralmente é a melhor abordagem. Uma operação baseada em conjunto geralmente é executada mais rapidamente do que uma abordagem iterativa, linha por linha.

Os cursores somente leitura rápidos podem ser facilmente substituídos por um constructo de looping. Veja a seguir um exemplo simples. Este exemplo de código atualiza as estatísticas para cada tabela no banco de dados. Ao iterar sobre as tabelas no loop, cada comando é executado em sequência.

Primeiro, crie uma tabela temporária contendo um número de linha exclusivo usado para identificar as instruções individuais:

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

Em segundo lugar, inicialize as variáveis necessárias para executar o loop:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Agora, execute o loop sobre as instruções que as executam uma de cada vez:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Por fim, descarte a tabela temporária criada na primeira etapa

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Passos Seguintes
Para obter mais dicas de desenvolvimento, consulte [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

