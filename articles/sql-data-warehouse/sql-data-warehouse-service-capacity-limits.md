---
title: Limites de capacidade – Azure Synapse Analytics (anteriormente conhecido como SQL DW)
description: Valores máximos permitidos para vários componentes do SQL Analytics no Azure Synapse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: e3661797ea408f219a67a1862901fee7c27a1d58
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123913"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Limites de capacidade do Azure Synapse Analytics (anteriormente conhecido como SQL DW)

Valores máximos permitidos para vários componentes do Azure Synapse.

## <a name="workload-management"></a>Gestão de cargas de trabalho

| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| [Unidades de data warehouse (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |DWU Max para uma única unidade de pool de SQL (data warehouse) | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Unidades de data warehouse (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |DTU padrão por servidor |54,000<br></br>Por padrão, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma cota de DTU de 54.000, que permite até DW5000c. Esta quota é apenas um limite de segurança. Você pode aumentar sua cota [criando um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e selecionando *cota* como o tipo de solicitação.  Para calcular suas necessidades de DTU, multiplique o 7,5 pelo total de DWU necessário ou multiplique 9,5 pelo cDWU total necessário. Por exemplo:<br></br>DW6000 x 7,5 = 45.000 DTUs<br></br>DW5000c x 9,5 = 47.500 DTUs.<br></br>Você pode exibir o consumo atual de DTU na opção SQL Server no Portal. Tanto as bases de dados em pausa como as que não estão em pausa contam para a quota de DTU. |
| Conexão de banco de dados |Máximo de sessões abertas simultâneas |1024<br/><br/>O número de sessões abertas simultâneas irá variar com base no DWU selecionado. O DWU600c e superior dão suporte a um máximo de 1024 sessões abertas. DWU500c e abaixo, dão suporte a um limite máximo de sessão de abertura simultânea de 512. Observe que há limites no número de consultas que podem ser executadas simultaneamente. Quando o limite de simultaneidade for excedido, a solicitação entrará em uma fila interna onde aguardará o processamento. |
| Conexão de banco de dados |Memória máxima para instruções preparadas |20 MB |
| [Gestão de cargas de trabalho](resource-classes-for-workload-management.md) |Máximo de consultas simultâneas |128<br/><br/>  Um máximo de 128 consultas simultâneas será executado e as consultas restantes serão enfileiradas.<br/><br/>O número de consultas simultâneas pode diminuir quando os usuários são atribuídos a classes de recursos mais altas ou quando a configuração de [unidade de data warehouse](memory-concurrency-limits.md) é reduzida. Algumas consultas, como consultas DMV, sempre podem ser executadas e não afetam o limite de consultas simultâneas. Para obter mais detalhes sobre a execução de consultas simultâneas, consulte o artigo de [máximos de simultaneidade](memory-concurrency-limits.md) . |
| [tempdb](sql-data-warehouse-tables-temporary.md) |GB máximo |399 GB por DW100. Portanto, em DWU1000, o tempdb é dimensionado para 3,99 TB. |

## <a name="database-objects"></a>Objetos de banco de dados
| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| Base de Dados |Tamanho máximo | Gen1:240 TB compactados em disco. Esse espaço é independente do espaço de log ou do tempdb e, portanto, esse espaço é dedicado a tabelas permanentes.  A compactação columnstore clusterizada é estimada em 5X.  Essa compactação permite que o banco de dados cresça para aproximadamente 1 PB quando todas as tabelas são columnstore clusterizado (o tipo de tabela padrão). <br/><br/> Gen2:240TB para repositório de armazenamento e ilimitado para tabelas columnstore |
| Tabela |Tamanho máximo |60 TB compactados em disco |
| Tabela |Tabelas por banco de dados | 100 000 |
| Tabela |Colunas por tabela |1024 colunas |
| Tabela |Bytes por coluna |Dependente do [tipo de dados](sql-data-warehouse-tables-data-types.md)da coluna. O limite é 8000 para tipos de dados char, 4000 para nvarchar ou 2 GB para tipos de dados MAX. |
| Tabela |Bytes por linha, tamanho definido |8060 bytes<br/><br/>O número de bytes por linha é calculado da mesma maneira que é para SQL Server com compactação de página. Assim como SQL Server, o armazenamento de estouro de linha tem suporte, o que permite que **colunas de comprimento variável** sejam empurradas para fora da linha. Quando linhas de comprimento variável são empurradas para fora da linha, somente a raiz de 24 bytes é armazenada no registro principal. Para obter mais informações, consulte [dados de estouro de linha que excedem 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabela |Partições por tabela |15 000<br/><br/>Para alto desempenho, recomendamos minimizar o número de partições que você precisa ao mesmo tempo em que oferece suporte às suas necessidades de negócios. À medida que aumenta o número de partições, a sobrecarga das operações DDL (linguagem de definição de dados) e DML (linguagem de manipulação de dados) aumenta e causa um desempenho mais lento. |
| Tabela |Caracteres por valor de limite de partição. |4000 |
| Índice |Índices não clusterizados por tabela. |50<br/><br/>Aplica-se somente a tabelas de armazenamento. |
| Índice |Índices clusterizados por tabela. |1<br><br/>Aplica-se a ambas as tabelas de repositório de armazenamento e columnstore. |
| Índice |Tamanho da chave de índice. |900 bytes.<br/><br/>Aplica-se somente a índices de armazenamento.<br/><br/>Os índices em colunas varchar com um tamanho máximo de mais de 900 bytes poderão ser criados se os dados existentes nas colunas não excederem 900 bytes quando o índice for criado. No entanto, as ações de inserção ou atualização posteriores nas colunas que fazem com que o tamanho total exceda 900 bytes falharão. |
| Índice |Colunas de chave por índice. |16<br/><br/>Aplica-se somente a índices de armazenamento. Os índices columnstore clusterizados incluem todas as colunas. |
| Estatísticas |Tamanho dos valores de coluna combinados. |900 bytes. |
| Estatísticas |Colunas por objeto de estatísticas. |32 |
| Estatísticas |Estatísticas criadas em colunas por tabela. |30,000 |
| Procedimentos Armazenados |Níveis máximos de aninhamento. |8 |
| Vista |Colunas por exibição |1,024 |

## <a name="loads"></a>Inúmeras
| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| Cargas do polybase |MB por linha |1<br/><br/>O polybase carrega linhas menores que 1 MB. Não há suporte para o carregamento de tipos de dados LOB em tabelas com um CCI (índice Columnstore clusterizado).<br/><br/> |

## <a name="queries"></a>Consultas
| Categoria | Descrição | Máximo |
|:--- |:--- |:--- |
| Consulta |Consultas em fila em tabelas de usuário. |1000 |
| Consulta |Consultas simultâneas em exibições do sistema. |100 |
| Consulta |Consultas em fila em exibições do sistema |1000 |
| Consulta |Parâmetros máximos |2098 |
| Batch |Tamanho máximo |65,536*4096 |
| SELECIONAR resultados |Colunas por linha |4096<br/><br/>Você nunca pode ter mais de 4096 colunas por linha no resultado de SELECT. Não há nenhuma garantia de que você sempre pode ter 4096. Se o plano de consulta exigir uma tabela temporária, o máximo de 1024 colunas por tabela poderá ser aplicado. |
| SELECIONAR |Subconsultas aninhadas |32<br/><br/>Você nunca pode ter mais de 32 subconsultas aninhadas em uma instrução SELECT. Não há nenhuma garantia de que você sempre pode ter 32. Por exemplo, uma junção pode introduzir uma subconsulta no plano de consulta. O número de subconsultas também pode ser limitado pela memória disponível. |
| SELECIONAR |Colunas por junção |1024 colunas<br/><br/>Você nunca pode ter mais de 1024 colunas na junção. Não há nenhuma garantia de que você sempre pode ter 1024. Se o plano de junção exigir uma tabela temporária com mais colunas do que o resultado da junção, o limite de 1024 se aplicará à tabela temporária. |
| SELECIONAR |Bytes por colunas agrupar por. |8060<br/><br/>As colunas na cláusula GROUP BY podem ter um máximo de 8060 bytes. |
| SELECIONAR |Bytes por colunas ORDER BY |8060 bytes<br/><br/>As colunas na cláusula ORDER BY podem ter um máximo de 8060 bytes |
| Identificadores por instrução |Número de identificadores referenciados |65,535<br/><br/> O número de identificadores que podem estar contidos em uma única expressão de uma consulta é limitado. Exceder esse número resulta na SQL Server do erro 8632. Para obter mais informações, consulte [erro interno: um limite dos serviços de expressão foi atingido](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Literais de cadeia de caracteres | Número de literais de cadeia de caracteres em uma instrução | 20,000 <br/><br/>O número de constantes de cadeia de caracteres em uma única expressão de uma consulta é limitado. Exceder esse número resulta na SQL Server do erro 8632.|

## <a name="metadata"></a>Metadados
| Exibição do sistema | Máximo de linhas |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Número total de trabalhos DMS para as solicitações de 1000 do SQL mais recentes. |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |Número total de etapas para as solicitações SQL mais recentes de 1000 que são armazenadas em sys. dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |As solicitações SQL mais recentes de 1000 que são armazenadas em sys. dm_pdw_exec_requests. |

## <a name="next-steps"></a>Passos seguintes
Para obter recomendações sobre como usar o Azure Synapse, consulte a [folha](cheat-sheet.md)de consulta.
