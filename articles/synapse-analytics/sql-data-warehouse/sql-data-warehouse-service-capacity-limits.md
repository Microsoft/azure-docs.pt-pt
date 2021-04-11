---
title: Limites de capacidade para piscina SQL dedicada
description: Valores máximos permitidos para vários componentes de piscina SQL dedicada em Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9bdc4b2fed40817c7173468180e34de1ed0506fb
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962684"
---
# <a name="capacity-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Limites de capacidade para piscina SQL dedicada em Azure Synapse Analytics

Valores máximos permitidos para vários componentes de piscina SQL dedicada em Azure Synapse Analytics.

## <a name="workload-management"></a>Gestão de cargas de trabalho

| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| [Unidades de Armazém de Dados (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Max DWU para uma única piscina SQL dedicada  | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Unidades de Armazém de Dados (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |DTU predefinido por servidor |54,000<br></br>Por padrão, cada servidor SQL (por exemplo, myserver.database.windows.net) tem uma quota DTU de 54.000, que permite até DW6000c. Esta quota é apenas um limite de segurança. Pode aumentar a sua quota [criando um bilhete de apoio](sql-data-warehouse-get-started-create-support-ticket.md) e selecionando *quota* como tipo de pedido.  Para calcular as suas necessidades de DTU, multiplique o 7.5 pelo DWU total necessário, ou multiplique 9 pelo total de cDWU necessário. Por exemplo:<br></br>DW6000 x 7,5 = 45.000 DTUs<br></br>DW7500c x 9 = 67.500 DTUs.<br></br>Pode ver o seu consumo atual de DTU a partir da opção do servidor SQL no portal. Tanto as bases de dados em pausa como as que não estão em pausa contam para a quota de DTU. |
| Ligação de base de dados |Sessões abertas concurrentes máximas |1024<br/><br/>O número de sessões abertas simultâneas variará em função do DWU selecionado. DWU600c e acima suportam um máximo de 1024 sessões abertas. DWU500c e abaixo, suporte um limite máximo de sessão aberta simultânea de 512. Nota: existem limites no número de consultas que podem ser executadas simultaneamente. Quando o limite de concordância é ultrapassado, o pedido entra numa fila interna onde espera ser processado. |
| Ligação de base de dados |Memória máxima para declarações preparadas |20 MB |
| [Gestão de cargas de trabalho](resource-classes-for-workload-management.md) |Consultas concurrentas máximas |128<br/><br/>  Um máximo de 128 consultas simultâneas serão executadas e as restantes consultas serão em fila.<br/><br/>O número de consultas simultâneas pode diminuir quando os utilizadores são atribuídos a classes de recursos mais elevadas ou quando a definição da [unidade de armazém de dados](memory-concurrency-limits.md) é reduzida. Algumas consultas, como consultas de DMV, são sempre permitidas a correr e não impactam o limite de consulta simultânea. Para obter mais informações sobre execução de consultas simultâneas, consulte o artigo [máximos de concordância.](memory-concurrency-limits.md) |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Máximo GB |399 GB por DW100c. No DWU1000c, a temperatura é dimensionada para 3,99 TB. |
||||

## <a name="database-objects"></a>Objetos de base de dados

| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| Base de Dados |Tamanho máximo | Gen1: 240 TB comprimido no disco. Este espaço é independente de espaço temporário ou log, e por isso este espaço é dedicado a mesas permanentes.  A compressão de colunas agrupadas é estimada em 5X.  Esta compressão permite que a base de dados cresça para aproximadamente 1 PB quando todas as tabelas estiverem agrupadas (o tipo de tabela predefinido). <br/><br/> Gen2: Armazenamento ilimitado para mesas de loja de colunas.  A parte da base de dados ainda está limitada a 240 TB comprimido no disco. |
| Tabela |Tamanho máximo |Tamanho ilimitado para mesas de loja de colunas. <br>60 TB para mesas de loja comprimidas no disco. |
| Tabela |Tabelas por base de dados | 100.000 |
| Tabela |Colunas por tabela |1024 colunas |
| Tabela |Bytes por coluna |Dependente do [tipo de dados](sql-data-warehouse-tables-data-types.md)de coluna. O limite é de 8000 para tipos de dados de char, 4000 para nvarchar ou 2 GB para tipos de dados MAX. |
| Tabela |Bytes por linha, tamanho definido |8060 bytes<br/><br/>O número de bytes por linha é calculado da mesma forma que para o SQL Server com compressão de página. Tal como o SQL Server, o armazenamento de transbordo de linha é suportado, o que permite que **colunas de comprimento variável** sejam empurradas para fora da linha. Quando as linhas de comprimento variável são empurradas para fora da linha, apenas a raiz de 24 bytes é armazenada no registo principal. Para obter mais informações, consulte [dados de transbordo de linha superiores a 8 KB](/previous-versions/sql/sql-server-2008-r2/ms186981(v=sql.105)). |
| Tabela |Divisórias por tabela |15 000<br/><br/>Para um alto desempenho, recomendamos minimizar o número de divisórias de que necessita, ao mesmo tempo que suporta os requisitos do seu negócio. À medida que o número de divisórias aumenta, a sobrecarga para as operações de Linguagem de Definição de Dados (DDL) e Linguagem de Manipulação de Dados (DML) cresce e causa um desempenho mais lento. |
| Tabela |Caracteres por valor limite de partição. |4000 |
| Índice |Índices não agrupados por tabela. |50<br/><br/>Aplica-se apenas a mesas de loja de filas. |
| Índice |Índices agrupados por tabela. |1<br><br/>Aplica-se tanto às tabelas de lojas de linha como às colunas. |
| Índice |Tamanho da chave do índice. |900 bytes.<br/><br/>Aplica-se apenas aos índices de loja de filas.<br/><br/>Podem ser criados índices em colunas varchar com um tamanho máximo superior a 900 bytes se os dados existentes nas colunas não excederem 900 bytes quando o índice é criado. No entanto, as ações posteriores de INSERT ou UPDATE nas colunas que fazem com que o tamanho total exceda 900 bytes falharão. |
| Índice |Colunas-chave por índice. |16<br/><br/>Aplica-se apenas aos índices de loja de filas. Os índices de loja de colunas agrupados incluem todas as colunas. |
| Estatísticas |Tamanho dos valores da coluna combinada. |900 bytes. |
| Estatísticas |Colunas por objeto estatístico. |32 |
| Estatísticas |Estatísticas criadas em colunas por tabela. |30,000 |
| Procedimentos Armazenados |Níveis máximos de nidificação. |8 |
| Vista |Colunas por vista |1,024 |
||||

## <a name="loads"></a>Cargas

| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| Cargas de Polibase |MB por linha |1<br/><br/>As cargas de polibase são inferiores a 1 MB. O carregamento de tipos de dados LOB em tabelas com um Índice de Loja de Colunas Agrupados (CCI) não é suportado.<br/> |
|Cargas de Polibase|Número total de ficheiros|1 000 000<br/><br/>As cargas de base poli-base não podem exceder mais de 1M ficheiros. Pode experimentar o seguinte erro: **A operação falhou como contagem de divisão superior ao limite superior de 10000000**.|

## <a name="queries"></a>Consultas

| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| Consulta |Consultas em fila nas mesas de utilizador. |1000 |
| Consulta |Consultas simultâneas sobre as vistas do sistema. |100 |
| Consulta |Consultas em fila sobre as vistas do sistema |1000 |
| Consulta |Parâmetros máximos |2098 |
| Batch |Tamanho máximo |65,536*4096 |
| Selecione resultados |Colunas por linha |4096<br/><br/>Nunca poderá ter mais de 4096 colunas por linha no resultado SELECT. Não há garantias de que sempre se pode ter 4096. Se o plano de consulta necessitar de uma tabela temporária, as colunas 1024 por tabela máxima podem ser aplicadas. |
| SELECIONAR |Subqueries aninhados |32<br/><br/>Nunca poderá ter mais de 32 subqueries aninhados numa declaração SELECT. Não há garantias de que sempre se pode ter 32. Por exemplo, um JOIN pode introduzir uma subquery no plano de consulta. O número de subqueries também pode ser limitado pela memória disponível. |
| SELECIONAR |Colunas por JOIN |1024 colunas<br/><br/>Nunca se pode ter mais de 1024 colunas no JOIN. Não há garantias de que possa sempre ter 1024. Se o plano JOIN necessitar de uma tabela temporária com mais colunas do que o resultado DO JOIN, o limite de 1024 aplica-se à tabela temporária. |
| SELECIONAR |Bytes por colunas GROUP BY. |8060<br/><br/>As colunas na cláusula GRUPO BY podem ter um máximo de 8060 bytes. |
| SELECIONAR |Bytes por ORDEM POR colunas |8060 bytes<br/><br/>As colunas na cláusula ORDER BY podem ter um máximo de 8060 bytes |
| Identificadores por declaração |Número de identificadores referenciados |65,535<br/><br/> O número de identificadores que podem ser contidos numa única expressão de uma consulta é limitado. Excedendo este número resulta no erro do SQL Server 8632. Para obter mais informações, consulte [Erro Interno: Foi atingido um limite de serviços de expressão](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Literais de cadeias | Número de cordas literais em comunicado | 20 000 <br/><br/>O número de constantes de cordas numa única expressão de uma consulta é limitado. Excedendo este número resulta no erro do SQL Server 8632.|
||||

## <a name="metadata"></a>Metadados

Os DMV's serão reiniciados quando uma piscina SQL dedicada for pausada ou quando for dimensionada.

| Vista do sistema | Filas máximas |
|:--- |:--- |
| [sys.dm_pdw_dms_cores](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-cores-transact-sql?view=azure-sqldw-latest&preserve-view=true) |100 |
| [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Número total de trabalhadores da DMS para os mais recentes 1000 pedidos SQL. |
| [sys.dm_pdw_errors](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-errors-transact-sql?view=azure-sqldw-latest&preserve-view=true) |10,000 |
| [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) |10,000 |
| [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?view=azure-sqldw-latest&preserve-view=true) |10,000 |
| [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Número total de etapas para os mais recentes 1000 pedidos SQL que são armazenados em sys.dm_pdw_exec_requests. |
| [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Os mais recentes pedidos de 1000 SQL que são armazenados em sys.dm_pdw_exec_requests. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter recomendações sobre a utilização do Azure Synapse, consulte a [Folha de Batota](cheat-sheet.md).