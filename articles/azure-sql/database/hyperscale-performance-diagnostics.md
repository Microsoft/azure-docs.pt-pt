---
title: Diagnósticos de desempenho em Hiperescala
description: Este artigo descreve como resolver problemas de desempenho de hiperescala na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019 sqldbrb=1
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: ed31ff5d77b258d141a77fc174c2d5452adf7d01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791720"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL Hiperscale desempenho problemas de resolução de diagnósticos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Para resolver problemas de desempenho numa base de dados de Hiperescala, [as metodologias gerais de afinação](monitor-tune-overview.md) do desempenho no nó de computação Azure SQL Database são o ponto de partida de uma investigação de desempenho. No entanto, dada a [arquitetura distribuída](service-tier-hyperscale.md#distributed-functions-architecture) da Hyperscale, foram adicionados diagnósticos adicionais para ajudar. Este artigo descreve dados de diagnóstico específicos de hiperescala.

## <a name="log-rate-throttling-waits"></a>Esperas de estrangulamento da taxa de registo

Cada nível de serviço de dados de base de dados Azure SQL tem limites de taxa de geração de registos aplicados através da [governação da taxa de registo](resource-limits-logical-server.md#transaction-log-rate-governance). Em Hyperscale, o limite de geração de log está atualmente definido para 100 MB/seg, independentemente do nível de serviço. No entanto, há momentos em que a taxa de geração de registos na réplica do cálculo primário tem de ser acelerada para manter a recuperabilidade das ANS. Este estrangulamento ocorre quando um [servidor de página ou outra réplica de computação](service-tier-hyperscale.md#distributed-functions-architecture) está significativamente por trás da aplicação de novos registos de registo a partir do serviço de registo.

Os seguintes tipos de espera (em [sys.dm_os_wait_stats)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)descrevem as razões pelas quais a taxa de registo pode ser acelerada na réplica do cálculo primário:

|Tipo de Espera    |Description                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Ocorre quando uma taxa de geração de registo de nó de nó de computação primária de hiperescala está a ser acelerada devido ao consumo retardado de registo no(s) servidor de página.         |
|RBIO_RG_DESTAGE        | Ocorre quando uma taxa de geração de registo de nó de nó de cálculo de hiperescala está a ser acelerada devido ao consumo retardado de registo pelo armazenamento de registos a longo prazo.         |
|RBIO_RG_REPLICA        | Ocorre quando uma taxa de geração de registo de nó de nó de cálculo de hiperescala está a ser acelerada devido ao consumo retardado de registos pela(s) réplicas secundárias legíveis.         |
|RBIO_RG_LOCALDESTAGE   | Ocorre quando uma taxa de geração de registo de nó de nó de cálculo de hiperescala está a ser acelerada devido ao consumo retardado de registo pelo serviço de registo.         |

## <a name="page-server-reads"></a>Leituras de servidor de página

As réplicas do cálculo não cache uma cópia completa da base de dados localmente. Os dados locais para a réplica do cálculo são armazenados no pool tampão (na memória) e na cache local de extensão de piscina de tampão resiliente (RBPEX) que é uma cache parcial (não-cobertura) de páginas de dados. Esta cache rbpex local é dimensionada proporcionalmente ao tamanho do cálculo e é três vezes a memória do nível de cálculo. O RBPEX é semelhante ao conjunto tampão, na medida em que tem os dados mais acedidos. Cada servidor de página, por outro lado, tem uma cache RBPEX de cobertura para a parte da base de dados que mantém.

Quando uma leitura é emitida numa réplica de computação, se os dados não existirem no pool tampão ou na cache local RBPEX, é emitida uma chamada de função getPage (pageId, LSN) e a página é recolhida a partir do servidor de página correspondente. As leituras dos servidores de página são leituras remotas e são, portanto, mais lentas do que as leituras do RBPEX local. Ao resolver problemas de desempenho relacionados com iO, precisamos ser capazes de dizer quantos IOs foram feitos através de leituras de servidores de página remotas relativamente mais lentos.

Várias vistas dinâmicas geridas (DMVs) e eventos estendidos têm colunas e campos que especificam o número de leituras remotas de um servidor de página, que pode ser comparado com o total de leituras. A loja de consultas também captura leituras remotas como parte das estatísticas de tempo de execução da consulta.

- As colunas para reportar leituras de servidores de página estão disponíveis em DMVs de execução e vistas de catálogo, tais como:

  - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
  - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
  - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
  - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
  - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- As leituras do servidor de página são adicionadas aos seguintes eventos estendidos:
  - sql_statement_completed
  - sp_statement_completed
  - sql_batch_completed
  - rpc_completed
  - scan_stopped
  - query_store_begin_persist_runtime_stat
  - consulta-store_execution_runtime_info
- Os ActuaPageServerReads/ActualPageServerReadAheads são adicionados ao plano de consulta XML para planos reais. Por exemplo:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Para visualizar estes atributos na janela de propriedades do plano de consulta, é necessário SSMS 18.3 ou mais tarde.

## <a name="virtual-file-stats-and-io-accounting"></a>Estatísticas de ficheiros virtuais e contabilidade de IO

Na Base de Dados Azure SQL, o [DMF sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) É a principal forma de monitorizar a Base de Dados SQL IO. As características de IO em Hiperescala são diferentes devido à sua [arquitetura distribuída.](service-tier-hyperscale.md#distributed-functions-architecture) Nesta secção, focamo-nos em IO (leituras e escritos) para ficheiros de dados como visto neste DMF. Em Hyperscale, cada ficheiro de dados visível neste DMF corresponde a um servidor de página remota. A cache RBPEX mencionada aqui é uma cache local baseada em SSD, que é uma cache não-cobrindo na réplica do cálculo.

### <a name="local-rbpex-cache-usage"></a>Utilização local de cache RBPEX

Cache RBPEX local existe na réplica do cálculo, no armazenamento SSD local. Assim, iO contra esta cache é mais rápido do que IO contra servidores de página remota. Atualmente, sys.dm_io_virtual_file_stats numa base de [dados](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) de Hiperescala tem uma linha especial reportando o IO contra a cache RBPEX local na réplica do cálculo. Esta linha tem o valor de 0 para ambos `database_id` e `file_id` colunas. Por exemplo, a consulta abaixo devolve as estatísticas de utilização RBPEX desde o arranque da base de dados.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Uma relação de leituras feitas no RBPEX com leituras agregadas feitas em todos os outros ficheiros de dados fornece o rácio de sucesso de cache RBPEX.

### <a name="data-reads"></a>Leituras de dados

- Quando as leituras são emitidas pelo motor de base de dados SQL Server numa réplica de computação, podem ser servidas quer pela cache RBPEX local, quer por servidores de página remota, quer por uma combinação das duas se lerem várias páginas.
- Quando a réplica do cálculo lê algumas páginas de um ficheiro específico, por exemplo file_id 1, se estes dados residem exclusivamente na cache RBPEX local, todo o IO para esta leitura é contabilizado contra file_id 0 (RBPEX). Se uma parte desses dados estiver na cache local do RBPEX, e alguma parte estiver num servidor de página remota, então o IO é contabilizado para file_id 0 para a peça servida a partir do RBPEX, e a parte servida a partir do servidor de página remota é contabilizada para file_id 1.
- Quando uma réplica computacional solicita uma página num [determinado LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) a partir de um servidor de página, se o servidor de página não tiver apanhado o LSN solicitado, a leitura na réplica do cálculo aguarda até que o servidor de página se apanhe antes de a página ser devolvida à réplica do cálculo. Para qualquer leitura de um servidor de página na réplica do cálculo, verá o tipo de espera PAGEIOLATCH_* se estiver à espera desse IO. Em Hyperscale, este tempo de espera inclui tanto o tempo para recuperar a página solicitada no servidor de página para o LSN necessário, como o tempo necessário para transferir a página do servidor de página para a réplica do cálculo.
- Grandes leituras como leitura antecipada são muitas vezes feitas usando ["Scatter-Gather" Lê-se.](/sql/relational-databases/reading-pages/) Isto permite leituras de até 4 MB de páginas de cada vez, consideradas uma única leitura no motor de base de dados SQL Server. No entanto, quando os dados que estão a ser lidos estão no RBPEX, estas leituras são contabilizadas como várias leituras individuais 8-KB, uma vez que o pool tampão e o RBPEX usam sempre páginas de 8 KB. Como resultado, o número de IOs lidos contra o RBPEX pode ser maior do que o número real de IOs realizados pelo motor.

### <a name="data-writes"></a>Os dados escrevem

- A réplica principal do cálculo não escreve diretamente para servidores de página. Em vez disso, os registos de registo do serviço de registo são reproduzidos nos servidores de página correspondentes.
- As gravações que acontecem na réplica do cálculo são predominantemente escritas para o RBPEX local (file_id 0). Para escritas em ficheiros lógicos que sejam maiores do que 8 KB, ou seja, as que são feitas usando [a recolha-escrita,](/sql/relational-databases/writing-pages/)cada operação de escrita é traduzida em várias 8-KB individualmente escreve para RBPEX uma vez que o pool tampão e RBPEX usam sempre páginas de 8 KB. Como resultado, o número de IOs de escrita visto contra o RBPEX pode ser maior do que o número real de IOs realizados pelo motor.
- Ficheiros não RBPEX, ou ficheiros de dados que não file_id 0 que correspondem a servidores de página, também mostram escritas. No nível de serviço Hyperscale, estas escritas são simuladas, porque as réplicas do cálculo nunca escrevem diretamente para servidores de página. Escrever IOPS e produção são contabilizados à medida que ocorrem na réplica do cálculo, mas a latência para ficheiros de dados que não file_id 0 não reflete a latência real das escritas do servidor de página.

### <a name="log-writes"></a>Log escreve

- No cálculo primário, uma gravação é contabilizada em file_id 2 de sys.dm_io_virtual_file_stats. Um registo escrito no cálculo primário é uma escrita para a Zona de Aterragem de log.
- Os registos de registo não são endurecidos na réplica secundária de um compromisso. Em Hyperscale, o log é aplicado pelo serviço de registo às réplicas secundárias assíncroneamente. Como as gravações não ocorrem em réplicas secundárias, qualquer contabilidade de IOs de registo nas réplicas secundárias é apenas para fins de rastreamento.

## <a name="data-io-in-resource-utilization-statistics"></a>IO de dados nas estatísticas de utilização de recursos

Numa base de dados não-Hyperscale, a leitura combinada e a escrita de IOPS contra ficheiros de dados, relativamente ao limite de dados de [governação](./resource-limits-logical-server.md#resource-governance) de recursos IOPS, são reportados em [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) pontos de vista, na `avg_data_io_percent` coluna. O mesmo valor é reportado no portal Azure como _Data IO Percentagem_.

Numa base de dados de Hiperescala, esta coluna reporta sobre a utilização de dados IOPS relativamente ao limite de armazenamento local apenas na réplica do cálculo, especificamente IO contra RBPEX e `tempdb` . Um valor de 100% nesta coluna indica que a governação de recursos está a limitar o IOPS de armazenamento local. Se isto estiver correlacionado com um problema de desempenho, afina a carga de trabalho para gerar menos IO ou aumentar o objetivo do serviço de base de dados para aumentar o [limite](resource-limits-vcore-single-databases.md)de governação de recursos _Max Data IOPS_ . Para a governação de recursos do RBPEX lê e escreve, o sistema conta com iOs individuais de 8-KB, em vez de IOs maiores que podem ser emitidos pelo motor de base de dados SQL Server.

O IO de dados contra servidores de páginas remotas não é reportado em visualizações de utilização de recursos ou no portal, mas é reportado no [DMF sys.dm_io_virtual_file_stats,](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) como referido anteriormente.

## <a name="additional-resources"></a>Recursos adicionais

- Para limites de recursos vCore para uma base de dados única de hiperescala ver [limites de nível de serviço de hiperescala vCore](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)
- Para afinação de desempenho da base de dados Azure SQL, consulte [o desempenho da consulta na Base de Dados Azure SQL](performance-guidance.md)
- Para afinar desempenhos utilizando a Loja de Consultas, consulte [a monitorização de desempenho utilizando a loja de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- Para scripts de monitorização de DMV, consulte [a base de dados Azure SQL de desempenho de monitorização utilizando vistas dinâmicas de gestão](monitoring-with-dmvs.md)