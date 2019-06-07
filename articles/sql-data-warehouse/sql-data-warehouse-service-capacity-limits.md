---
title: Limites de capacidade - Azure SQL Data Warehouse | Documentos da Microsoft
description: Valores máximos permitidos para vários componentes do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/14/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 62213ca1910ff26287bcd398d89fe7f8caf3cfac
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514685"
---
# <a name="sql-data-warehouse-capacity-limits"></a>Limites de capacidade do SQL Data Warehouse
Valores máximos permitidos para vários componentes do Azure SQL Data Warehouse.

## <a name="workload-management"></a>Gestão de cargas de trabalho
| Category | Descrição | Máximo |
|:--- |:--- |:--- |
| [Unidades do Data Warehouse (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Máx. de DWU para um único armazém de dados SQL | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Unidades do Data Warehouse (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Padrão DTU por servidor |54,000<br></br>Por predefinição, cada SQL server (por exemplo, myserver.database.windows.net) tem uma Quota de DTU de 54,000, que permite até DW6000c. Esta quota é apenas um limite de segurança. Pode aumentar a quota por [criar um pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e selecionando *Quota* como o tipo de pedido.  Para calcular a DTU necessidades, multiplique 7,5 pelo total que necessário DWU ou multiplicar 9.0 pelo cDWU total necessário. Por exemplo:<br></br>DW6000 x 7.5 = 45.000 DTUs<br></br>DW6000c x 9.0 = 54,000 DTUs.<br></br>Pode ver o consumo de DTU atual da opção de servidor SQL no portal. Tanto as bases de dados em pausa como as que não estão em pausa contam para a quota de DTU. |
| Ligação de base de dados |Máxima simultâneas abra sessões |1024<br/><br/>O número de sessões abertas em simultâneo irá variar consoante a DWU selecionada. DWU600c e acima suporte um máximo de 1024 abra sessões. DWU500c e abaixo dar suporte a um limite de sessão aberta de em simultâneo máximo de 512. Tenha em atenção que existem limites no número de consultas que podem executar em simultâneo. Quando o limite de simultaneidade for excedido, o pedido vai para uma fila interna em que ele espera para serem processados. |
| Ligação de base de dados |Memória máxima para instruções preparadas |20 MB |
| [Gestão de cargas de trabalho](resource-classes-for-workload-management.md) |Consultas em simultâneo máximas |128<br/><br/> O SQL Data Warehouse pode executar um máximo de 128 consultas em simultâneo e filas restantes consultas.<br/><br/>O número de consultas em simultâneo pode diminuir quando os utilizadores são atribuídos aos mais classes de recursos ou quando o SQL Data Warehouse tem um inferior [unidade do armazém de dados](memory-and-concurrency-limits.md) definição. Algumas consultas, como consultas DMV, sempre têm permissão para executar e fazer não afeta o limite de consulta em simultâneo. Para obter mais detalhes sobre a execução de consultas em simultâneo, consulte a [valores máximos de simultaneidade](memory-and-concurrency-limits.md#concurrency-maximums) artigo. |
| [tempdb](sql-data-warehouse-tables-temporary.md) |GB máximo |GB, 399 por DW100. Por isso em DWU1000, tempdb é ajustado para 3.99 TB. |

## <a name="database-objects"></a>objetos de base de dados
| Category | Descrição | Máximo |
|:--- |:--- |:--- |
| Base de Dados |Tamanho máx. | Gen1: 240 TB compactado no disco. Este espaço é independente de espaço em tempdb ou de registo e, portanto, este espaço dedicado para tabelas permanentes.  Estima-se em 5 X compressão columnstore em cluster.  Esta compressão permite que a base de dados aumentar de aproximadamente 1 PB quando todas as tabelas columnstore em cluster (o tipo de tabela do padrão). <br/><br/> Gen2: 240TB para rowstore e armazenamento ilimitada para tabelas columnstore |
| Tabela |Tamanho máx. |60 TB compactado no disco |
| Tabela |Tabelas por base de dados | 100,000 |
| Tabela |Colunas por tabela |1024 colunas |
| Tabela |Bytes por coluna |Dependentes na coluna [tipo de dados](sql-data-warehouse-tables-data-types.md). Limite é 8000 para tipos de dados char, 4000 para nvarchar ou 2 GB para tipos de dados de máx. |
| Tabela |Bytes por linha, o tamanho definido |8060 bytes<br/><br/>O número de bytes por linha é calculado da mesma forma como está para o SQL Server com a compactação page. Como o SQL Server, SQL Data Warehouse suporta o armazenamento de estouro de linha, que permite **colunas de comprimento variável** para ser enviado fora da linha. Quando as linhas de comprimento variável são enviados por push fora da linha, apenas 24 bytes raiz é armazenado no registo principal. Para obter mais informações, consulte [exceder de dados de estouro de linha 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabela |Partições por tabela |15,000<br/><br/>Elevado desempenho, recomendamos minimizar o número de partições precisa enquanto ainda dar suporte a seus requisitos empresariais. À medida que cresce o número de partições, a sobrecarga para operações de linguagem DDL (Data Definition) e a linguagem de manipulação de dados (DML) cresce e faz com que o desempenho mais lento. |
| Tabela |Carateres por valor de limite de partição. |4000 |
| Índice |Índices não agrupados por tabela. |50<br/><br/>Aplica-se às tabelas de rowstore apenas. |
| Índice |Índices em cluster por tabela. |1<br><br/>Aplica-se às tabelas rowstore e columnstore. |
| Índice |Tamanho da chave de índice. |900 bytes.<br/><br/>Aplica-se apenas a índices rowstore.<br/><br/>Podem ser criados os índices em colunas varchar com um tamanho máximo de mais de 900 bytes se os dados existentes nas colunas não excederem 900 bytes quando o índice é criado. No entanto, mais tarde inserir ou ações de ATUALIZAÇÃO nas colunas que fazem com que o tamanho total exceder 900 bytes irão falhar. |
| Índice |Colunas de chave por índice. |16<br/><br/>Aplica-se apenas a índices rowstore. Índices columnstore em cluster incluem todas as colunas. |
| Estatísticas |Tamanho valores das colunas combinado. |900 bytes. |
| Estatísticas |Colunas por objeto de estatísticas. |32 |
| Estatísticas |Estatísticas criadas em colunas por tabela. |30,000 |
| Procedimentos Armazenados |Máximos níveis de aninhamento. |8 |
| Vista |Colunas por visualização |1,024 |

## <a name="loads"></a>Cargas
| Category | Descrição | Máximo |
|:--- |:--- |:--- |
| Carregamentos do Polybase |MB por linha |1<br/><br/>O Polybase carrega linhas que são mais pequenas do que 1 MB. Carregar tipos de dados LOB em tabelas com um índice de Columnstore em cluster (CCI) não é suportada.<br/><br/> |

## <a name="queries"></a>Consultas
| Category | Descrição | Máximo |
|:--- |:--- |:--- |
| Consulta |Consultas em fila em tabelas de utilizador. |1000 |
| Consulta |Consultas em simultâneo em vistas de sistema. |100 |
| Consulta |Consultas em fila nos modos de exibição do sistema |1000 |
| Consulta |Parâmetros máximos |2098 |
| Batch |Tamanho máximo |65,536*4096 |
| SELECIONADOS resultados |Colunas por linha |4096<br/><br/>Nunca pode ter mais de 4096 colunas por linha no resultado SELECIONE. Não é garantido que sempre pode ter 4096. Se o plano de consulta necessita de uma tabela temporária, as colunas de 1024 por tabela máxima podem ser aplicadas. |
| SELECIONAR |Subconsultas aninhadas |32<br/><br/>Nunca pode ter mais do que 32 subconsultas aninhadas numa instrução SELECT. Não é garantido que sempre pode ter 32. Por exemplo, uma JUNÇÃO pode introduzir uma subconsulta no plano de consulta. O número de subconsultas também pode ser limitado por memória disponível. |
| SELECIONAR |Colunas por associação |1024 colunas<br/><br/>Nunca pode ter mais de 1024 colunas na associação. Não é garantido que sempre pode ter 1024. Se o plano de JUNÇÃO necessita de uma tabela temporária com mais colunas do que o resultado da JUNÇÃO, o limite de 1024 aplica-se a tabela temporária. |
| SELECIONAR |Bytes por grupo por colunas. |8060<br/><br/>As colunas na cláusula GROUP BY podem ter um máximo de 8060 bytes. |
| SELECIONAR |Bytes por colunas ORDER BY |8060 bytes<br/><br/>As colunas na cláusula ORDER BY podem ter um máximo de 8060 bytes |
| Identificadores por instrução |Número de identificadores de referenciado |65,535<br/><br/>Armazém de dados SQL limita o número de identificadores que podem ser contidos numa única expressão de uma consulta. A exceder este número resulta em erro do SQL Server 8632. Para obter mais informações, consulte [erro interno: Foi atingido o limite de serviços uma expressão](https://support.microsoft.com/en-us/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Literais de cadeia de caracteres | Número de literais de cadeia de caracteres numa instrução | 20,000 <br/><br/>Armazém de dados SQL limita o número de constantes de cadeia de caracteres numa única expressão de uma consulta. A exceder este número resulta em erro do SQL Server 8632.|

## <a name="metadata"></a>Metadados
| Vista de sistema | Máximo de linhas |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Número total de trabalhos DMS para o mais recente 1000 pedidos de SQL. |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |Número total de passos para os pedidos SQL de 1000 mais recentes que estão armazenados no sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |O SQL mais recente de 1000 pedidos que são armazenados em sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Passos Seguintes
Para obter recomendações sobre como utilizar o SQL Data Warehouse, veja a [Trapacear folha](cheat-sheet.md).
