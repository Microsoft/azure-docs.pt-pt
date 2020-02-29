---
title: Limites de capacidade - Azure Synapse Analytics (anteriormente SQL DW)
description: Valores máximos permitidos para vários componentes do SQL Analytics em Azure Synapse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: a225c375d877ae44c2b21ea8e79e31f17db36878
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198189"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Limites de capacidade da Azure Synapse Analytics (anteriormente SQL DW)

Valores máximos permitidos para vários componentes do Azure Synapse.

## <a name="workload-management"></a>Gestão de cargas de trabalho

| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| [Unidades de Armazém de Dados (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Max DWU para uma única unidade de piscina SQL (armazém de dados) | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Unidades de Armazém de Dados (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |DTU padrão por servidor |54,000<br></br>Por padrão, cada servidor SQL (por exemplo, myserver.database.windows.net) tem uma quota DTU de 54.000, que permite até DW5000c. Esta quota é apenas um limite de segurança. Pode aumentar a sua quota [criando um bilhete](sql-data-warehouse-get-started-create-support-ticket.md) de apoio e selecionando a *Quota* como o tipo de pedido.  Para calcular as suas necessidades de DTU, multiplique o 7.5 pelo Total de DWU necessário, ou multiplique 9,5 pelo total de cDWU necessário. Por exemplo:<br></br>DW6000 x 7,5 = 45.000 DTUs<br></br>DW5000c x 9,5 = 47.500 DTUs.<br></br>Pode visualizar o seu consumo atual de DTU a partir da opção de servidor SQL no portal. Tanto as bases de dados em pausa como as que não estão em pausa contam para a quota de DTU. |
| Ligação de base de dados |Sessões abertas simultâneas máximas |1024<br/><br/>O número de sessões abertas simultâneas variará em função da DWU selecionada. DWU600c e acima suportam um máximo de 1024 sessões abertas. DWU500c e abaixo, suporte um limite máximo simultâneo de sessão aberta de 512. Note que existem limites no número de consultas que podem ser executadas simultaneamente. Quando o limite de moeda é ultrapassado, o pedido entra numa fila interna onde espera ser processado. |
| Ligação de base de dados |Memória máxima para declarações preparadas |20 MB |
| [Gestão de cargas de trabalho](resource-classes-for-workload-management.md) |Consultas simultâneas máximas |128<br/><br/>  Um máximo de 128 consultas simultâneas serão executadas e as restantes consultas serão em fila.<br/><br/>O número de consultas simultâneas pode diminuir quando os utilizadores são atribuídos a classes de recursos mais elevados ou quando a definição da unidade de armazém de [dados](memory-concurrency-limits.md) é reduzida. Algumas consultas, como consultas de DMV, são sempre autorizadas a correr e não impactam o limite de consulta simultânea. Para mais detalhes sobre a execução de consulta simultânea, consulte o artigo sobre os máximos de [moeda.](memory-concurrency-limits.md) |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Gb máximo |399 GB por DW100c. Portanto, em DWU1000c, a temperatura é dimensionada para 3,99 TB. |
||||

## <a name="database-objects"></a>Objetos de base de dados

| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| Base de Dados |Tamanho máximo | Gen1: 240 TB comprimido no disco. Este espaço é independente do espaço temporário ou de log, pelo que este espaço é dedicado a tabelas permanentes.  A compressão da loja de colunas agrupada é estimada em 5X.  Esta compressão permite que a base de dados cresça para aproximadamente 1 PB quando todas as tabelas estiverem agrupadas (o tipo de tabela predefinido). <br/><br/> Gen2: Armazenamento ilimitado para mesas de lojas de colunas.  A parte da loja de remo da base de dados ainda está limitada a 240 TB comprimidos no disco. |
| Tabela |Tamanho máximo |Tamanho ilimitado para mesas de loja de colunas. <br>60 TB para mesas de loja de remo comprimidos no disco. |
| Tabela |Tabelas por base de dados | 100 000 |
| Tabela |Colunas por tabela |1024 colunas |
| Tabela |Bytes por coluna |Dependente do tipo de [dados](sql-data-warehouse-tables-data-types.md)da coluna . Para tipos de dados de caracteres, o MAX Limit pode armazenar até 2 GB no armazenamento off page (row-overflow).  Os caracteres não-Unicode, como o limite de carvão ou varchar, são 8000 numa página de dados, caracteres Unicode como nchar ou nvarchar limit é de 4000 numa página de dados.  Utilize os tamanhos de armazenamento da página de dados para aumentar o desempenho. |
| Tabela |Bytes por linha, tamanho definido |8060 bytes<br/><br/>O número de bytes por linha é calculado da mesma forma que é para o SQL Server com compressão de página. Tal como o SQL Server, o armazenamento de transbordo de linha é suportado, o que permite que as colunas de **comprimento variável** sejam empurradas para fora da linha. Quando as linhas de comprimento variável são empurradas para fora da linha, apenas a raiz de 24 bytes é armazenada no registo principal. Para mais informações, consulte dados de [transbordo de linha superiores a 8-KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabela |Divisórias por mesa |15 000<br/><br/>Para um alto desempenho, recomendamos minimizar o número de divisórias que necessita, ao mesmo tempo que suporta os seus requisitos de negócio. À medida que o número de divisórias aumenta, as operações gerais para as operações de Linguagem de Definição de Dados (DDL) e Linguagem de Manipulação de Dados (DML) crescem e causam um desempenho mais lento. |
| Tabela |Caracteres por valor de fronteira de divisória. |4000 |
| Índice |Índices não agrupados por tabela. |50<br/><br/>Aplica-se apenas às mesas de loja de remo. |
| Índice |Índices agrupados por tabela. |1<br><br/>Aplica-se tanto às mesas da loja de remo como às mesas de lojas de colunas. |
| Índice |Tamanho da chave do índice. |900 bytes.<br/><br/>Aplica-se apenas aos índices de lojas de remo.<br/><br/>Os índices nas colunas de varchar com um tamanho máximo superior a 900 bytes podem ser criados se os dados existentes nas colunas não excederem 900 bytes quando o índice for criado. No entanto, posteriormente, as ações de INSIRA ou ATUALIZAÇÃO nas colunas que fazem com que o tamanho total ultrapasse os 900 bytes falharão. |
| Índice |Colunas-chave por índice. |16<br/><br/>Aplica-se apenas aos índices de lojas de remo. Os índices de lojas de colunas agrupadas incluem todas as colunas. |
| Estatísticas |Tamanho dos valores da coluna combinada. |900 bytes. |
| Estatísticas |Colunas por objeto estatístico. |32 |
| Estatísticas |Estatísticas criadas em colunas por tabela. |30,000 |
| Procedimentos Armazenados |Níveis máximos de nidificação. |8 |
| Ver |Colunas por vista |1,024 |
||||

## <a name="loads"></a>Cargas

| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| Cargas de Polibase |MB por linha |1<br/><br/>A polibase carrega linhas que são menores que 1 MB. Não é suportado o carregamento dos tipos de dados LOB em tabelas com um Índice de Colunas Agrupadas (CCI).<br/><br/> |
||||

## <a name="queries"></a>Consultas

| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| Consulta |Consultas na fila nas mesas dos utilizadores. |1000 |
| Consulta |Consultas simultâneas sobre as vistas do sistema. |100 |
| Consulta |Consultas em fila sobre as vistas do sistema |1000 |
| Consulta |Parâmetros máximos |2098 |
| Batch |Tamanho máximo |65,536*4096 |
| Selecione resultados |Colunas por linha |4096<br/><br/>Nunca poderá ter mais de 4096 colunas por linha no resultado SELECT. Não há garantiade que sempre se pode ter 4096. Se o plano de consulta necessitar de uma tabela temporária, podem aplicar-se as 1024 colunas por tabela máxima. |
| SELECIONAR |Subqueries aninhados |32<br/><br/>Nunca poderá ter mais de 32 subqueries aninhados numa declaração SELECT. Não há garantiade que sempre se pode ter 32. Por exemplo, um JOIN pode introduzir uma subqueria no plano de consulta. O número de subqueries também pode ser limitado pela memória disponível. |
| SELECIONAR |Colunas por JOIN |1024 colunas<br/><br/>Nunca poderá ter mais de 1024 colunas no JOIN. Não há garantiade que sempre se pode ter 1024. Se o plano JOIN necessitar de uma tabela temporária com mais colunas do que o resultado JOIN, o limite de 1024 aplica-se à tabela temporária. |
| SELECIONAR |Bytes por grupo por colunas. |8060<br/><br/>As colunas da cláusula GROUP BY podem ter um máximo de 8060 bytes. |
| SELECIONAR |Bytes por ORDEM Por colunas |8060 bytes<br/><br/>As colunas na cláusula ORDER BY podem ter um máximo de 8060 bytes |
| Identificadores por declaração |Número de identificadores referenciados |65,535<br/><br/> O número de identificadores que podem ser contidos numa única expressão de uma consulta é limitado. Exceder este número resulta no erro do Servidor SQL 8632. Para obter mais informações, consulte [Erro interno: Foi atingido um limite de serviços](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a)de expressão . |
| Cordas literais | Número de cordas literais em comunicado | 20,000 <br/><br/>O número de constantes de cordas numa única expressão de uma consulta é limitado. Exceder este número resulta no erro do Servidor SQL 8632.|
||||

## <a name="metadata"></a>Metadados

| Vista do sistema | Filas máximas |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Número total de trabalhadores em DMS para os mais recentes 1000 pedidos SQL. |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |Número total de etapas para os mais recentes 1000 pedidos SQL que são armazenados em sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |Os mais recentes 1000 pedidos SQL que são armazenados em sys.dm_pdw_exec_requests. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter recomendações sobre a utilização de Azure Synapse, consulte a [Folha de Batota](cheat-sheet.md).
