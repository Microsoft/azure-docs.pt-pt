---
title: Migrar o código SQL ao SQL Data Warehouse | Documentos da Microsoft
description: Dicas para migrar seu código SQL para o Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: fae3ae16ee0100ad446c0b6c7851553a3376bb4f
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400980"
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrar o código SQL ao SQL Data Warehouse

Este artigo explica as alterações de código que provavelmente terá de tomar ao migrar o seu código de outra base de dados para o SQL Data Warehouse. Algumas funcionalidades do SQL Data Warehouse podem significativamente melhorar o desempenho como eles foram concebidos para funcionar de uma maneira distribuída. No entanto, para manter o desempenho e dimensionamento, algumas funcionalidades também não estão disponíveis.

## <a name="common-t-sql-limitations"></a>Limitações de T-SQL comuns

A lista seguinte resume as funcionalidades mais comuns que não oferece suporte a SQL Data Warehouse. As ligações levam-o para soluções alternativas para as funcionalidades não suportadas:

* [Associações de ANSI em atualizações][ANSI joins on updates]
* [Associações de ANSI em eliminações][ANSI joins on deletes]
* [instrução Merge][merge statement]
* associações entre bases de dados
* [Cursores][cursors]
* [INSERT..EXEC][INSERT..EXEC]
* cláusula output
* funções definidas pelo utilizador de inline
* funções com múltiplas instruções
* expressões de tabela comuns
* [recursiva expressões de tabela comuns (CTE)] (#Recursive-common-table-expressions-(CTE)
* Funções do CLR e procedimentos
* função de $partition
* variáveis de tabela
* parâmetros de valor de tabela
* Transações distribuídas
* consolidação / reversão de trabalho
* guardar a transação
* contextos de execução (EXECUTE AS)
* [Agrupar por cláusula com rollup / de cubo / conjuntos de opções de agrupamento][group by clause with rollup / cube / grouping sets options]
* [níveis de aninhamento além 8][nesting levels beyond 8]
* [a atualizar nas vistas][updating through views]
* [Não existem dados de máx. Escreva para cadeias de caracteres SQL dinâmicas][no MAX data type for dynamic SQL strings]

Felizmente pode ser contornada maioria dessas limitações. Explicações são fornecidas nos artigos de desenvolvimento relevantes mencionados acima.

## <a name="supported-cte-features"></a>Funcionalidades suportadas da CTE

Tabela (CTEs expressões comuns) são parcialmente suportadas no SQL Data Warehouse.  As seguintes funcionalidades CTE atualmente são suportadas:

* Uma CTE pode ser especificada numa instrução SELECT.
* Uma CTE pode ser especificada numa instrução CREATE VIEW.
* Uma CTE pode ser especificada numa instrução CREATE TABLE AS SELECT (CTAS).
* Uma CTE pode ser especificada numa instrução de criar remoto tabela AS SELECIONE (CRTAS).
* Uma CTE pode ser especificada numa instrução de criar externo tabela AS SELECIONE (CETAS).
* Uma tabela remota pode ser referenciada a partir de uma CTE.
* Uma tabela externa pode ser referenciada a partir de uma CTE.
* Várias definições de consulta da CTE podem ser definidas numa CTE.

## <a name="cte-limitations"></a>Limitações da CTE

Expressões de tabela comuns tem algumas limitações no SQL Data Warehouse, incluindo:

* Uma CTE deve ser seguida de uma única instrução SELECT. INSERT, UPDATE, DELETE, e instruções de intercalação não são suportadas.
* Uma expressão de tabela comum, que incluem as referências a próprio (uma expressão comum recursiva tabela) não é suportada (consulte abaixo a secção).
* Não é permitido especificar mais de uma cláusula WITH numa CTE. Por exemplo, se um CTE_query_definition contiver uma subconsulta, esse subconsulta não pode conter uma aninhada com uma cláusula que define a outra CTE.
* Uma cláusula ORDER BY não é possível utilizar o CTE_query_definition, exceto se for especificada uma cláusula TOP.
* Quando uma CTE é utilizada numa instrução que faz parte de um lote, a instrução antes que ele deve ser seguida de um ponto e vírgula.
* Quando usadas em instruções preparadas pelo sp_prepare, as CTEs irão comportar-se da mesma forma que outras instruções SELECT em PDW. No entanto, se as CTEs são utilizadas como parte da CETAS preparado pelo sp_prepare, o comportamento pode diferir do SQL Server e outras instruções PDW por conta da forma de enlace é implementado para sp_prepare. Se SELECIONAR que referências a que CTE está a utilizar uma coluna errada que não existe no CTE, o sp_prepare passará sem detectar o erro, mas o erro será gerado durante a sp_execute em vez disso.

## <a name="recursive-ctes"></a>CTEs recursivas

CTEs recursivas não são suportadas no SQL Data Warehouse.  A migração de uma CTE recursiva pode ser um pouco complexa e o melhor processo é quebrá-lo em vários passos. Normalmente, pode usar um loop e preencher uma tabela temporária como iterar as consultas de provisória recursiva. Depois da tabela temporária é preenchida, em seguida, pode retornar os dados como um conjunto único resultado. Foi utilizada uma abordagem semelhante para resolver `GROUP BY WITH CUBE` no [Agrupar por cláusula com rollup / de cubo / conjuntos de opções de agrupamento] [ group by clause with rollup / cube / grouping sets options] artigo.

## <a name="unsupported-system-functions"></a>Funções de sistema não suportado

Também há algumas funções de sistema que não são suportadas. Alguns dos nossos principais recursos que poderão ser normalmente utilizados no armazém de dados são:

* NEWSEQUENTIALID)
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Alguns desses problemas podem ser solucionadas.

## <a name="rowcount-workaround"></a>@@ROWCOUNT solução

Para solucionar a falta de suporte para @@ROWCOUNT, crie um procedimento armazenado que irá recuperar a última contagem de linhas do pdw_request_steps e, em seguida, executar `EXEC LastRowCount` após uma instrução DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista completa de todas as declarações do T-SQL suportadas, consulte [tópicos de Transact-SQL][Transact-SQL topics].

<!--Image references-->

<!--Article references-->
[ANSI joins on updates]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joins on deletes]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge statement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL topics]: ./sql-data-warehouse-reference-tsql-statements.md

[cursors]: ./sql-data-warehouse-develop-loops.md
[group by clause with rollup / cube / grouping sets options]: ./sql-data-warehouse-develop-group-by-options.md
[nesting levels beyond 8]: ./sql-data-warehouse-develop-transactions.md
[updating through views]: ./sql-data-warehouse-develop-views.md
[use of select for variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[no MAX data type for dynamic SQL strings]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
