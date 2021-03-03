---
title: Compreender e resolver problemas de bloqueio do Azure SQL
titleSuffix: Azure SQL Database
description: Uma visão geral de tópicos específicos da base de dados Azure SQL sobre bloqueio e resolução de problemas.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/24/2021
ms.openlocfilehash: b829d7045ac520cfe908c3c8809ae17702d6175d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691438"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Compreender e resolver problemas de bloqueio da base de dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Objetivo

O artigo descreve o bloqueio nas bases de dados Azure SQL e demonstra como resolver problemas e resolver o bloqueio. 

Neste artigo, o termo ligação refere-se a uma única sessão iniciada na base de dados. Cada ligação aparece como um ID de sessão (SPID) ou session_id em muitos DMVs. Cada um destes SPIDs é frequentemente referido como um processo, embora não seja um contexto de processo separado no sentido habitual. Pelo contrário, cada SPID consiste nos recursos do servidor e nas estruturas de dados necessárias para atender os pedidos de uma única ligação a um determinado cliente. Uma única aplicação de cliente pode ter uma ou mais ligações. Do ponto de vista da Base de Dados Azure SQL, não existe diferença entre múltiplas ligações de uma única aplicação de cliente num único computador cliente e múltiplas ligações a partir de múltiplas aplicações de clientes ou vários computadores clientes; são atómicos. Uma ligação pode bloquear outra ligação, independentemente do cliente de origem.

> [!NOTE]
> **Este conteúdo está focado na Base de Dados Azure SQL.** O Azure SQL Database baseia-se na versão mais recente e estável do motor de base de dados do Microsoft SQL Server, pelo que grande parte do conteúdo é similar, embora as opções e ferramentas de resolução de problemas possam diferir. Para obter mais informações sobre o bloqueio no SQL Server, consulte [Compreender e resolver problemas de bloqueio do SQL Server](/troubleshoot/sql/performance/understand-resolve-blocking).

## <a name="understand-blocking"></a>Compreender o bloqueio 
 
O bloqueio é uma característica inevitável e por design de qualquer sistema relacional de gestão de bases de dados (RDBMS) com concordância baseada em bloqueio. Como mencionado anteriormente, no SQL Server, o bloqueio ocorre quando uma sessão segura um bloqueio num recurso específico e um segundo SPID tenta adquirir um tipo de bloqueio conflituoso no mesmo recurso. Normalmente, o prazo para o qual o primeiro SPID bloqueia o recurso é pequeno. Quando a sessão proprietária libertar o bloqueio, a segunda ligação é livre para adquirir o seu próprio bloqueio no recurso e continuar a processar. Este é um comportamento normal e pode acontecer muitas vezes ao longo de um dia sem nenhum efeito percetível no desempenho do sistema.

A duração e o contexto de transação de uma consulta determinam a duração dos seus cadeados e, assim, o seu efeito noutras consultas. Se a consulta não for executada dentro de uma transação (e não forem utilizadas sugestões de bloqueio), as fechaduras para declarações SELECT só serão mantidas num recurso no momento em que estiver realmente a ser lida, e não durante a consulta. Para as declarações de INSERT, UPDATE e DELETE, os bloqueios são mantidos durante a consulta, tanto para a consistência dos dados como para permitir que a consulta seja revirada se necessário.

Para consultas executadas dentro de uma transação, a duração para a qual os cadeados são mantidos é determinada pelo tipo de consulta, o nível de isolamento da transação e se as indicações de bloqueio são utilizadas na consulta. Para uma descrição dos níveis de bloqueio, bloqueio e isolamento de transações, consulte os seguintes artigos:

* [Bloqueio no Motor de Base de Dados](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [Personalização de bloqueio e versão de linha](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [Modos de bloqueio](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [Compatibilidade de bloqueio](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [Transações](/sql/t-sql/language-elements/transactions-transact-sql)

Ao travar e bloquear persiste ao ponto de haver um efeito prejudicial no desempenho do sistema, deve-se a uma das seguintes razões:

* Um SPID mantém os bloqueios num conjunto de recursos por um longo período de tempo antes de os libertar. Este tipo de bloqueio resolve-se ao longo do tempo, mas pode causar degradação do desempenho.

* Um SPID mantém as fechaduras num conjunto de recursos e nunca os liberta. Este tipo de bloqueio não se resolve sozinho e impede o acesso indefinidamente aos recursos afetados.

No primeiro cenário, a situação pode ser muito fluida, uma vez que diferentes SPIDs causam bloqueios em diferentes recursos ao longo do tempo, criando um alvo em movimento. Estas situações são difíceis de resolver usando [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) para reduzir o problema a consultas individuais. Em contrapartida, a segunda situação resulta num estado consistente que pode ser mais fácil de diagnosticar.

## <a name="applications-and-blocking"></a>Aplicações e bloqueios

Pode haver uma tendência para se focar na afinação do lado do servidor e problemas de plataforma quando enfrenta um problema de bloqueio. No entanto, a atenção dada apenas à base de dados pode não conduzir a uma resolução, podendo absorver o tempo e a energia melhor direcionados para examinar a aplicação do cliente e as consultas que apresenta. Independentemente do nível de visibilidade que a aplicação expõe relativamente às chamadas de base de dados que estão a ser feitas, um problema de bloqueio requer, no entanto, frequentemente a inspeção das declarações exatas do SQL apresentadas pela aplicação e o comportamento exato da aplicação no que diz respeito ao cancelamento de consultas, gestão de ligações, busca de todas as linhas de resultados, e assim por diante. Se a ferramenta de desenvolvimento não permitir o controlo explícito sobre a gestão da ligação, cancelamento de consulta, tempo de consulta, obtenção de resultados, e assim por diante, problemas de bloqueio podem não ser resolúveis. Este potencial deve ser examinado de perto antes de selecionar uma ferramenta de desenvolvimento de aplicações para a Base de Dados Azure SQL, especialmente para ambientes OLTP sensíveis ao desempenho. 

Preste atenção ao desempenho da base de dados durante a fase de conceção e construção da base de dados e aplicação. Em especial, o consumo de recursos, o nível de isolamento e o comprimento da trajetória de transação devem ser avaliados para cada consulta. Cada consulta e transação devem ser o mais leves possível. Uma boa disciplina de gestão de ligação deve ser exercida, sem ela, a aplicação pode parecer ter um desempenho aceitável em um número reduzido de utilizadores, mas o desempenho pode degradar-se significativamente à medida que o número de utilizadores escala para cima. 

Com um design adequado de aplicação e consulta, o Azure SQL Database é capaz de suportar muitos milhares de utilizadores simultâneos num único servidor, com pouco bloqueio.

> [!Note]
> Para obter mais orientações sobre o desenvolvimento de aplicações, consulte [problemas de conectividade de resolução de problemas e outros erros com a Base de Dados Azure SQL e Azure SQL Gestão de Instâncias](troubleshoot-common-errors-issues.md) e [Tratamento de Falhas Transitórias](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling).

## <a name="troubleshoot-blocking"></a>Bloqueio de resolução de problemas

Independentemente da situação de bloqueio em que estamos, a metodologia para a resolução de problemas é a mesma. Estas separações lógicas são o que ditará o resto da composição deste artigo. O conceito é encontrar o bloqueador de cabeça e identificar o que essa consulta está a fazer e porque está a bloquear. Uma vez identificada a consulta problemática (isto é, o que está a segurar fechaduras para o período prolongado), o próximo passo é analisar e determinar por que o bloqueio está acontecendo. Depois de entendermos o porquê, podemos então fazer alterações redesenhar a consulta e a transação.

Passos na resolução de problemas:

1. Identifique a sessão de bloqueio principal (bloqueador de cabeça)

2. Encontre a consulta e a transação que está a causar o bloqueio (o que está a segurar fechaduras por um período prolongado)

3. Analisar/entender por que o bloqueio prolongado ocorre

4. Resolver problemas de bloqueio redesenhar consulta e transação

Agora vamos mergulhar para discutir como identificar a sessão de bloqueio principal com uma captura de dados apropriada.

## <a name="gather-blocking-information"></a>Recolha informação de bloqueio

Para contrariar a dificuldade de resolver problemas de bloqueio de problemas, um administrador de base de dados pode usar scripts SQL que monitorizam constantemente o estado de bloqueio e bloqueio na base de dados Azure SQL. Para recolher estes dados, existem essencialmente dois métodos. 

A primeira é consultar objetos de gestão dinâmica (DMOs) e armazenar os resultados para comparação ao longo do tempo. Alguns objetos referenciados neste artigo são pontos de vista dinâmicos de gestão (DMVs) e alguns são funções de gestão dinâmica (DMFs). O segundo método é usar XEvents para capturar o que está a executar. 


## <a name="gather-information-from-dmvs"></a>Recolher informações dos DMVs

A referência aos DMVs para o bloqueio de resolução de problemas tem como objetivo identificar o SPID (ID da sessão) na cabeça da cadeia de bloqueio e na Declaração SQL. Procurem as vítimas que estão a ser bloqueadas. Se algum SPID estiver a ser bloqueado por outro SPID, então investigue o SPID que possui o recurso (o SPID de bloqueio). O dono da SPID também está a ser bloqueado? Pode andar na corrente para encontrar o bloqueador de cabeça e depois investigar por que razão mantém a fechadura.

Lembre-se de executar cada um destes scripts na base de dados target Azure SQL.

* Os comandos sp_who e sp_who2 são comandos mais antigos para mostrar todas as sessões atuais. O DMV sys.dm_exec_sessions retorna mais dados num conjunto de resultados mais fácil de consultar e filtrar. Encontrará sys.dm_exec_sessions no centro de outras consultas. 

* Se já tiver uma sessão específica identificada, pode usar `DBCC INPUTBUFFER(<session_id>)` para encontrar a última declaração que foi submetida por uma sessão. Resultados semelhantes podem ser devolvidos com a função de gestão dinâmica sys.dm_exec_input_buffer (DMF), num conjunto de resultados mais fácil de consultar e filtrar, proporcionando a session_id e a request_id. Por exemplo, devolver a consulta mais recente apresentada por session_id 66 e request_id 0:

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Consulte a sys.dm_exec_requests e consulte a coluna blocking_session_id. Quando blocking_session_id = 0, uma sessão não está a ser bloqueada. Embora sys.dm_exec_requests lista apenas os pedidos atualmente executados, qualquer ligação (ativa ou não) será listada em sys.dm_exec_sessions. Baseie-se nesta união comum entre sys.dm_exec_requests e sys.dm_exec_sessions na próxima consulta.

* Execute esta consulta de amostra para encontrar as consultas de execução ativa e o seu texto de lote SQL atual ou texto tampão de entrada, utilizando os DMVs [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) ou [sys.dm_exec_input_buffer.](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) Se os dados devolvidos pelo `text` campo de sys.dm_exec_sql_text forem NUS, a consulta não está atualmente a ser executada. Nesse caso, o `event_info` campo de sys.dm_exec_input_buffer conterá a última corda de comando passada para o motor SQL. Esta consulta também pode ser usada para identificar sessões que bloqueiam outras sessões, incluindo uma lista de session_ids bloqueadas por session_id. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Execute esta consulta de amostra mais elaborada, fornecida pelo Microsoft Support, para identificar o chefe de uma cadeia de bloqueio de múltiplas sessões, incluindo o texto de consulta das sessões envolvidas numa cadeia de bloqueio.

```sql
WITH cteHead ( session_id,request_id,wait_type,wait_resource,last_wait_type,is_user_process,request_cpu_time
,request_logical_reads,request_reads,request_writes,wait_time,blocking_session_id,memory_usage
,session_cpu_time,session_reads,session_writes,session_logical_reads
,percent_complete,est_completion_time,request_start_time,request_status,command
,plan_handle,sql_handle,statement_start_offset,statement_end_offset,most_recent_sql_handle
,session_status,group_id,query_hash,query_plan_hash) 
AS ( SELECT sess.session_id, req.request_id, LEFT (ISNULL (req.wait_type, ''), 50) AS 'wait_type'
    , LEFT (ISNULL (req.wait_resource, ''), 40) AS 'wait_resource', LEFT (req.last_wait_type, 50) AS 'last_wait_type'
    , sess.is_user_process, req.cpu_time AS 'request_cpu_time', req.logical_reads AS 'request_logical_reads'
    , req.reads AS 'request_reads', req.writes AS 'request_writes', req.wait_time, req.blocking_session_id,sess.memory_usage
    , sess.cpu_time AS 'session_cpu_time', sess.reads AS 'session_reads', sess.writes AS 'session_writes', sess.logical_reads AS 'session_logical_reads'
    , CONVERT (decimal(5,2), req.percent_complete) AS 'percent_complete', req.estimated_completion_time AS 'est_completion_time'
    , req.start_time AS 'request_start_time', LEFT (req.status, 15) AS 'request_status', req.command
    , req.plan_handle, req.[sql_handle], req.statement_start_offset, req.statement_end_offset, conn.most_recent_sql_handle
    , LEFT (sess.status, 15) AS 'session_status', sess.group_id, req.query_hash, req.query_plan_hash
    FROM sys.dm_exec_sessions AS sess
    LEFT OUTER JOIN sys.dm_exec_requests AS req ON sess.session_id = req.session_id
    LEFT OUTER JOIN sys.dm_exec_connections AS conn on conn.session_id = sess.session_id 
    )
, cteBlockingHierarchy (head_blocker_session_id, session_id, blocking_session_id, wait_type, wait_duration_ms,
wait_resource, statement_start_offset, statement_end_offset, plan_handle, sql_handle, most_recent_sql_handle, [Level])
AS ( SELECT head.session_id AS head_blocker_session_id, head.session_id AS session_id, head.blocking_session_id
    , head.wait_type, head.wait_time, head.wait_resource, head.statement_start_offset, head.statement_end_offset
    , head.plan_handle, head.sql_handle, head.most_recent_sql_handle, 0 AS [Level]
    FROM cteHead AS head
    WHERE (head.blocking_session_id IS NULL OR head.blocking_session_id = 0)
    AND head.session_id IN (SELECT DISTINCT blocking_session_id FROM cteHead WHERE blocking_session_id != 0)
    UNION ALL
    SELECT h.head_blocker_session_id, blocked.session_id, blocked.blocking_session_id, blocked.wait_type,
    blocked.wait_time, blocked.wait_resource, h.statement_start_offset, h.statement_end_offset,
    h.plan_handle, h.sql_handle, h.most_recent_sql_handle, [Level] + 1
    FROM cteHead AS blocked
    INNER JOIN cteBlockingHierarchy AS h ON h.session_id = blocked.blocking_session_id and h.session_id!=blocked.session_id --avoid infinite recursion for latch type of blocking
    WHERE h.wait_type COLLATE Latin1_General_BIN NOT IN ('EXCHANGE', 'CXPACKET') or h.wait_type is null
    )
SELECT bh.*, txt.text AS blocker_query_or_most_recent_query 
FROM cteBlockingHierarchy AS bh 
OUTER APPLY sys.dm_exec_sql_text (ISNULL ([sql_handle], most_recent_sql_handle)) AS txt;
```

* Para travar transações de longa duração ou não autorizadas, utilize outro conjunto de DMVs para visualização de transações abertas em vigor, incluindo [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)e sys.dm_exec_sql_text. Existem vários DMVs associados a operações de rastreio, ver mais [DMVs em transações](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) aqui. 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* Referência [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) que está na camada thread/tarefa de SQL. Isto devolve informações sobre o tipo de espera SQL que o pedido está a passar. Tal como sys.dm_exec_requests, apenas os pedidos ativos são devolvidos por sys.dm_os_waiting_tasks. 

> [!Note]
> Para muito mais sobre os tipos de espera, incluindo estatísticas de espera agregadas ao longo do tempo, consulte o DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database). Este DMV devolve estatísticas de espera agregadas apenas para a base de dados atual.

* Utilize o DMV [sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) para obter mais informações granulares sobre as fechaduras colocadas por consultas. Este DMV pode devolver grandes quantidades de dados num SQL Server de produção, e é útil para diagnosticar quais os bloqueios que estão atualmente detidos. 

Devido à JOIN INNER on sys.dm_os_waiting_tasks, a seguinte consulta restringe a saída de sys.dm_tran_locks apenas a pedidos atualmente bloqueados, o seu estado de espera e as suas fechaduras:

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* Com os DMVs, armazenar os resultados da consulta ao longo do tempo fornecerá pontos de dados que lhe permitirão rever o bloqueio durante um intervalo de tempo especificado para identificar bloqueios ou tendências persistentes. 

## <a name="gather-information-from-extended-events"></a>Recolher informações de eventos estendidos

Além das informações acima, é muitas vezes necessário capturar um traço das atividades no servidor para investigar cuidadosamente um problema de bloqueio na Base de Dados Azure SQL. Por exemplo, se uma sessão executar várias declarações dentro de uma transação, apenas a última declaração que foi submetida será representada. No entanto, uma das declarações anteriores pode ser a razão pela qual os bloqueios ainda estão detidos. Um rastreio permitir-lhe-á ver todos os comandos executados por uma sessão dentro da transação atual.

Existem duas formas de capturar vestígios no SQL Server; Eventos Estendidos (XEvents) e Rastreios de Perfis. No entanto, [o SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) é uma tecnologia de traços precotadas não suportada para a Base de Dados Azure SQL. [A Extended Events](/sql/relational-databases/extended-events/extended-events) é a mais recente tecnologia de rastreio que permite mais versatilidade e menos impacto no sistema observado, e a sua interface está integrada no SQL Server Management Studio (SSMS). 

Consulte o documento que explica como utilizar o [Assistente de Nova Sessão de Eventos Estendidos](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) em SSMS. No entanto, para as bases de dados Azure SQL, o SSMS fornece uma subclasse de Eventos Estendidos em cada base de dados no Object Explorer. Utilize um assistente de sessão de Eventos Estendidos para capturar estes eventos úteis: 

-   Erros de categoria:
    -   Atenção
    -   Error_reported  
    -   Execution_warning

-   Avisos de categoria: 
    -   Missing_join_predicate

-   Execução da categoria:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Bloquear
    -   Lock_deadlock

-   Sessão
    -   Existing_connection
    -   Iniciar sessão
    -   Terminar sessão

## <a name="identify-and-resolve-common-blocking-scenarios"></a>Identificar e resolver cenários comuns de bloqueio

Ao examinar as informações acima, pode determinar a causa da maioria dos problemas de bloqueio. O resto deste artigo é uma discussão sobre como usar esta informação para identificar e resolver alguns cenários comuns de bloqueio. Esta discussão pressupõe que usou os scripts de bloqueio (referenciados anteriormente) para capturar informações sobre os SPIDs de bloqueio e capturou a atividade da aplicação usando uma sessão de XEvent.

## <a name="analyze-blocking-data"></a>Analisar dados de bloqueio 

* Examinar a saída dos DMVs sys.dm_exec_requests e sys.dm_exec_sessions para determinar as cabeças das cadeias de bloqueio, utilizando blocking_these e session_id. Isto identificará claramente quais os pedidos que estão bloqueados e que estão a ser bloqueados. Veja mais adiante as sessões que estão bloqueadas e bloqueadas. Existe uma raiz comum ou raiz para a cadeia de bloqueio? Eles provavelmente partilham uma mesa comum, e uma ou mais das sessões envolvidas numa cadeia de bloqueio está a realizar uma operação de escrita. 

* Examinar a saída dos DMVs sys.dm_exec_requests e sys.dm_exec_sessions para obter informações sobre os SPIDs na cabeça da cadeia de bloqueio. Procure os seguintes campos: 

    -    `sys.dm_exec_requests.status`  
    Esta coluna mostra o estado de um pedido particular. Normalmente, um estado de sono indica que o SPID completou a execução e aguarda que o pedido envie outra consulta ou lote. Um estado de execução ou execução é executado que o SPID está atualmente a processar uma consulta. A tabela seguinte dá breves explicações sobre os vários valores de estado.

    | Estado | Significado |
    |:-|:-|
    | Fundo | O SPID está a executar uma tarefa de fundo, como deteção de impasse, escritor de registos ou posto de controlo. |
    | Dormir | O SPID não está atualmente a executar. Isto geralmente indica que o SPID aguarda um comando da aplicação. |
    | Em Execução | O SPID está atualmente a funcionar num programador. |
    | Runnable | O SPID está na fila runnable de um agendador e à espera de ter tempo de agendamento. |
    | Suspenso | O SPID está à espera de um recurso, como um cadeado ou um trinco. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    Este campo indica-lhe o número de transações abertas nesta sessão. Se este valor for superior a 0, o SPID encontra-se dentro de uma transação aberta e pode estar a deter fechaduras adquiridas por qualquer declaração dentro da transação.

    -    `sys.dm_exec_requests.open_transaction_count`  
    Da mesma forma, este campo indica-lhe o número de transações abertas neste pedido. Se este valor for superior a 0, o SPID encontra-se dentro de uma transação aberta e pode estar a deter fechaduras adquiridas por qualquer declaração dentro da transação.

    -   `sys.dm_exec_requests.wait_type`, `wait_time` e `last_wait_type`  
    Se o  `sys.dm_exec_requests.wait_type`   for NU, o pedido não está neste momento à espera de nada e o  `last_wait_type`   valor indica o último que  `wait_type`   o pedido encontrou. Para obter mais informações  `sys.dm_os_wait_stats` sobre e uma descrição dos tipos de espera mais comuns, consulte [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql). O  `wait_time`   valor pode ser usado para determinar se o pedido está a progredir. Quando uma consulta contra a tabela sys.dm_exec_requests devolve um valor na  `wait_time`   coluna inferior ao valor de uma  `wait_time`   consulta anterior de sys.dm_exec_requests, isto indica que o bloqueio prévio foi adquirido e libertado e está agora à espera de um novo bloqueio (assumindo não-zero). `wait_time` Isto pode ser verificado comparando a `wait_resource`   saída entre sys.dm_exec_requests, que exibe o recurso para o qual o pedido está à espera.

    -   `sys.dm_exec_requests.wait_resource` Este campo indica o recurso que um pedido bloqueado está à espera. A tabela que se segue lista  `wait_resource`   os formatos comuns e o seu significado:

    | Recurso | Formato | Exemplo | Explicação | 
    |:-|:-|:-|:-|
    | Tabela | Base de dadosID:ObjectID:IndexID | TAB: 5:261575970:1 | Neste caso, a base de dados ID 5 é a base de dados de amostras de pubs e o objeto ID 261575970 é a tabela de títulos e 1 é o índice agrupado. |
    | Página | Base de dadosID:FileID:PageID | PÁGINA: 5:1:104 | Neste caso, a base de dados ID 5 é pubs, o ficheiro ID 1 é o ficheiro de dados primário, e a página 104 é uma página pertencente à tabela de títulos. Para identificar as object_id a página pertence, utilize a função de gestão dinâmica [sys.dm_db_page_info,](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql)passando no DatabaseID, FileId, PageId a partir do `wait_resource` . | 
    | Chave | Base-dadoID:Hobt_id (valor de hash para chave de índice) | CHAVE: 5:7205759404284928 (3300a4f361aa) | Neste caso, a base de dados ID 5 é Pubs, Hobt_ID 72057594044284928 corresponde a index_id 2 para object_id 261575970 (tabela de títulos). Utilize a vista do catálogo sys.partitions para associar o hobt_id a um index_id e object_id específicos. Não há como fazer o hash da chave de índice para um valor-chave específico. |
    | Linha | Base de dadosID:FileID:PageID:Slot (linha) | 5:104:3 | Neste caso, a base de dados ID 5 é pubs, o ficheiro ID 1 é o ficheiro de dados primário, a página 104 é uma página pertencente à tabela de títulos, e a ranhura 3 indica a posição da linha na página. |
    | Compilar  | Base de dadosID:FileID:PageID:Slot (linha) | 5:104:3 | Neste caso, a base de dados ID 5 é pubs, o ficheiro ID 1 é o ficheiro de dados primário, a página 104 é uma página pertencente à tabela de títulos, e a ranhura 3 indica a posição da linha na página. |

    -    `sys.dm_tran_active_transactions` O [DMV sys.dm_tran_ative_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) contém dados sobre transações abertas que podem ser associados a outros DMVs para uma imagem completa das transações que aguardam o seu compromisso ou reversão. Utilize a seguinte consulta para devolver informações sobre transações abertas, juntando-se a outros DMVs, incluindo [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql). Considere o estado atual de uma `transaction_begin_time` transação, e outros dados situacionais para avaliar se pode ser uma fonte de bloqueio.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   Outras colunas

        As colunas restantes em [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) e [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) podem fornecer informações sobre a raiz de um problema também. A sua utilidade varia consoante as circunstâncias do problema. Por exemplo, pode determinar se o problema ocorre apenas a partir de determinados clientes (nome de anfitrião), em certas bibliotecas de rede (net_library), quando o último lote submetido por um SPID estava `last_request_start_time` em sys.dm_exec_sessions, quanto tempo um pedido estava a ser usado `start_time` em sys.dm_exec_requests, e assim por diante.


## <a name="common-blocking-scenarios"></a>Cenários comuns de bloqueio

A tabela abaixo mapeia sintomas comuns às suas causas prováveis.  

As `wait_type` `open_transaction_count` colunas , e `status` colunas referem-se às informações devolvidas por [sys.dm_exec_request,](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)outras colunas podem ser devolvidas [por sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql). O "Resolve?" a coluna indica se o bloqueio resolverá por si só, ou se a sessão deve ser eliminada através do `KILL` comando. Para obter mais informações, consulte [KILL (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql).

| Scenario | Tipo de espera | Open_Tran | Estado | Resolve? | Outros Sintomas |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NÃO NULO | >= 0 | runnable | Sim, quando a consulta terminar. | Em sys.dm_exec_sessions, **lê-se,** **cpu_time** e/ou **colunas memory_usage** aumentarão ao longo do tempo. A duração da consulta será elevada quando concluída. |
| 2 | NULL | \>0 | dormir | Não, mas a SPID pode ser morta. | Pode ser visto um sinal de atenção na sessão do Evento Alargado para este SPID, indicando que ocorreu uma hora de tempo de consulta ou cancelamento. |
| 3 | NULL | \>= 0 | runnable | N.º Não resolverá até que o cliente pegue todas as linhas ou feche a ligação. O SPID pode ser morto, mas pode levar até 30 segundos. | Se open_transaction_count = 0, e o SPID mantiver fechaduras enquanto o nível de isolamento de transação é padrão (READ COMMMITTED), esta é uma causa provável. |  
| 4 | Varia | \>= 0 | runnable | N.º Não resolverá até que o cliente cancele consultas ou feche as ligações. Os SPIDs podem ser mortos, mas podem levar até 30 segundos. | A coluna **de nome de anfitrião** em sys.dm_exec_sessions para o SPID na cabeça de uma cadeia de bloqueio será a mesma que uma das SPID que está bloqueando. |  
| 5 | NULL | \>0 | reversão | Sim. | Pode ser visto um sinal de atenção na sessão de Eventos Prolongados para este SPID, indicando que ocorreu uma hora de tempo de consulta ou cancelamento, ou simplesmente foi emitida uma declaração de reversão. |  
| 6 | NULL | \>0 | dormir | Eventualmente. Quando o Windows NT determinar que a sessão já não está ativa, a ligação Azure SQL Database será quebrada. | O `last_request_start_time` valor em sys.dm_exec_sessions é muito mais cedo do que o tempo atual. |

Os seguintes cenários irão expandir-se nestes cenários. 

1.  Bloqueio causado por uma consulta normalmente em execução com um longo tempo de execução

    **Resolução**: A solução para este tipo de problema de bloqueio é procurar formas de otimizar a consulta. Na verdade, esta classe de problema de bloqueio pode ser apenas um problema de desempenho, e exigir que você o prossiga como tal. Para obter informações sobre a resolução de problemas de uma consulta específica de marcha lenta, consulte [Como resolver consultas de correção lenta no SQL Server](/troubleshoot/sql/performance/troubleshoot-slow-running-queries). Para obter mais informações, consulte [Monitor e Tune for Performance](/sql/relational-databases/performance/monitor-and-tune-for-performance). 

    Os relatórios da [Loja de Consultas](/sql/relational-databases/performance/best-practice-with-the-query-store) em SSMS são também uma ferramenta altamente recomendada e valiosa para identificar as consultas mais dispendiosas, planos de execução subóptimas. Reveja também a secção de [Desempenho Inteligente](intelligent-insights-overview.md) do portal Azure para a base de dados Azure SQL, incluindo o Insight de Desempenho [de Consulta](query-performance-insight-use.md).

    Se tiver uma consulta de longa duração que bloqueie outros utilizadores e não possa ser otimizada, considere movê-la de um ambiente OLTP para um sistema de reporte dedicado, uma [réplica sincronizada apenas de leitura da base de dados.](read-scale-out.md)

1.  Bloqueio causado por um SPID adormecido que tem uma transação não comprometida

    Este tipo de bloqueio pode muitas vezes ser identificado por um SPID que está a dormir ou à espera de um comando, mas cujo nível de nidificação de transações `@@TRANCOUNT` (, a partir de sys.dm_exec_requests) é superior a `open_transaction_count` zero. Isto pode ocorrer se a aplicação experimentar um tempo de consulta, ou emitir um cancelamento sem também emitir o número necessário de declarações rollback e/ou COMMIT. Quando um SPID recebe uma consulta de tempo ou um cancelamento, terminará a consulta e o lote atuais, mas não retroceda automaticamente ou efetirá a transação. A aplicação é responsável por isso, uma vez que a Base de Dados Azure SQL não pode assumir que toda uma transação deve ser revertida devido ao cancelamento de uma única consulta. O tempo de tempo de consulta ou cancelamento aparecerá como um evento de sinal de atenção para o SPID na sessão do Evento Alargado.

    Para demonstrar uma transação explícita não comprometida, emita a seguinte consulta:

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    Em seguida, execute esta consulta na mesma janela:
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    A produção da segunda consulta indica que o nível de nidificação de transações é um. Todos os cadeados adquiridos na transação são mantidos até que a transação seja efe a í.5.000 ou revertida. Se os pedidos abrirem e cometerem transações explicitamente, uma comunicação ou outro erro poderá deixar a sessão e a sua transação em estado aberto. 

    Utilize o script acima com base em sys.dm_tran_ative_transactions para identificar transações atualmente não comprometidas.

    **Resoluções:**

     -   Além disso, esta classe de problema de bloqueio também pode ser um problema de desempenho, e exige que você o prossiga como tal. Se o tempo de execução da consulta puder ser diminuído, o tempo de consulta ou cancelamento não ocorrerá. É importante que a aplicação seja capaz de lidar com os cenários de tempo ou cancelamento caso surjam, mas também pode beneficiar de examinar o desempenho da consulta. 
     
     -    As aplicações devem gerir adequadamente os níveis de nidificação de transações, ou podem causar um problema de bloqueio após o cancelamento da consulta desta forma. Considere o seguinte:  

            *    No manipulador de erros da aplicação do cliente, execute na sequência de `IF @@TRANCOUNT > 0 ROLLBACK TRAN` qualquer erro, mesmo que a aplicação do cliente não acredite que uma transação esteja aberta. É necessário verificar as transações abertas, uma vez que um procedimento armazenado chamado durante o lote poderia ter iniciado uma transação sem o conhecimento da aplicação do cliente. Certas condições, como o cancelamento da consulta, impedem que o procedimento execute para além da declaração atual, pelo que mesmo que o procedimento tenha lógica para verificar `IF @@ERROR <> 0` e abortar a transação, este código de reversão não será executado nesses casos.  
            *    Se estiver a ser utilizado um pooling de ligação numa aplicação que abra a ligação e executa um pequeno número de consultas antes de libertar a ligação de volta à piscina, como uma aplicação baseada na Web, a desativação temporária do pooling de ligação pode ajudar a aliviar o problema até que a aplicação do cliente seja modificada para lidar adequadamente com os erros. Ao desativar o agrupamento de ligações, a libertação da ligação provocará uma desconexão física da ligação Azure SQL Database, resultando na reversão de quaisquer transações abertas.  
            *    Utilize `SET XACT_ABORT ON` para a ligação, ou em quaisquer procedimentos armazenados que iniciem transações e não estejam a limpar na sequência de um erro. Em caso de erro de tempo de execução, esta definição abortará quaisquer transações abertas e devolverá o controlo ao cliente. Para mais informações, [reveja o SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql).
    > [!NOTE]
    > A ligação não é reiniciada até ser reutilizada a partir do conjunto de ligação, pelo que é possível que um utilizador possa abrir uma transação e, em seguida, libertar a ligação para o pool de ligação, mas pode não ser reutilizada durante alguns segundos, período durante o qual a transação permaneceria aberta. Se a ligação não for reutilizada, a transação será abortada quando a ligação estiver fora e for removida do conjunto de ligação. Assim, é ideal para a aplicação do cliente abortar transações no seu manipulador de erros ou usar `SET XACT_ABORT ON` para evitar este potencial atraso.

    > [!CAUTION]
    > `SET XACT_ABORT ON`Seguintes, as declarações da T-SQL na sequência de uma declaração que causa um erro não serão executadas. Isto pode afetar o fluxo pretendido do código existente.

1.  Bloqueio causado por um SPID cuja aplicação de cliente correspondente não levou todas as linhas de resultados à conclusão

    Depois de enviar uma consulta para o servidor, todas as aplicações devem imediatamente levar todas as linhas de resultados para a conclusão. Se uma aplicação não conseguir todas as linhas de resultados, as fechaduras podem ser deixadas nas mesas, bloqueando outros utilizadores. Se estiver a utilizar uma aplicação que envie declarações DE SQL de forma transparente ao servidor, a aplicação deve recolher todas as linhas de resultados. Se não o fizer (e se não puder ser configurado para o fazer), poderá não conseguir resolver o problema do bloqueio. Para evitar o problema, pode restringir as aplicações mal comportadas a uma base de dados de relatórios ou de apoio à decisão.
    
    > [!NOTE]
    > Consulte [a orientação para a lógica de re-tentar](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) para aplicações que se ligam à Base de Dados Azure SQL. 
    
    **Resolução**: O pedido deve ser reescrito para que todas as linhas do resultado sejam concluídas. Isto não exclui a utilização de [OFFSET e FETCH na cláusula ORDER by](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) de uma consulta para realizar a paging do lado do servidor.

1.  Bloqueio causado por um SPID que está em estado de reversão

    Uma consulta de modificação de dados que seja KILLed, ou cancelada fora de uma transação definida pelo utilizador, será revertida. Isto também pode ocorrer como um efeito colateral da sessão de rede do cliente desligando, ou quando um pedido é selecionado como a vítima do impasse. Isto pode ser identificado frequentemente observando a saída de sys.dm_exec_requests, o que pode indicar o **comando** ROLLBACK , e a **coluna percent_complete** pode mostrar progresso. 

    Graças à [funcionalidade de Recuperação acelerada da Base de Dados](../accelerated-database-recovery.md) introduzida em 2019, as reversãos prolongadas devem ser raras.
    
    **Resolução**: Aguarde que o SPID termine de reverter as alterações que foram feitas. 

    Para evitar esta situação, não efetue grandes operações de escrita de lotes ou operações de criação ou manutenção de índices durante horas movimentadas em sistemas OLTP. Se possível, efetue tais operações durante períodos de baixa atividade.

1.  Bloqueio causado por uma ligação órfã

    Se a aplicação do cliente prender erros ou a estação de trabalho do cliente for reiniciada, a sessão de rede para o servidor pode não ser imediatamente cancelada sob algumas condições. Do ponto de vista da Base de Dados Azure SQL, o cliente ainda parece estar presente, e quaisquer fechaduras adquiridas podem ainda ser mantidas. Para obter mais informações, consulte [Como resolver as ligações órfãs no SQL Server](/sql/t-sql/language-elements/kill-transact-sql#remarks). 

    **Resolução**: Se a aplicação do cliente tiver sido desligada sem limpar adequadamente os seus recursos, pode encerrar o SPID utilizando o `KILL` comando. O `KILL` comando toma o valor SPID como entrada. Por exemplo, para matar o SPID 99, emita o seguinte comando:

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>Ver também

* [Otimização da monitorização e do desempenho na Base de Dados SQL do Azure e no Azure SQL Managed Instance](./monitor-tune-overview.md)
* [Monitorização do desempenho utilizando a Loja de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [Guia de Controlo de Versão de Linha e Bloqueio de Transações](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [NÍVEL DE ISOLAMENTO DE TRANSAÇÃO DEFINIDO](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [Início Rápido: Eventos expandidos no SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Insights Inteligentes usando IA para monitorizar e resolver problemas de desempenho da base de dados](intelligent-insights-overview.md)

## <a name="learn-more"></a>Saber mais

* [Base de Dados Azure SQL: Melhorar a afinação de desempenho com afinação automática](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Melhorar o desempenho da base de dados Azure SQL com afinação automática](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Fornecer um desempenho consistente com o SQL do Azure](/learn/modules/azure-sql-performance/)
* [Problemas de conectividade de resolução de problemas e outros erros com Azure SQL Database e Azure SQL Managed Instance](troubleshoot-common-errors-issues.md)
* [Tratamento transitório de falhas](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
