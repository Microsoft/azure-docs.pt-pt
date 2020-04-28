---
title: Diagnósticos de desempenho em Hiperescala
description: Este artigo descreve como resolver problemas de desempenho em Hiperescala na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 26bd6ddb9d8255b8e2510133fc4b6aa645f89f68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75615070"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnósticos de resolução de problemas de desempenho da SQL Hyperscale

Para resolver problemas de desempenho numa base de dados de Hiperescala, [metodologias gerais de afinação](sql-database-monitor-tune-overview.md) de desempenho no nó de cálculo da base de dados Azure SQL é o ponto de partida de uma investigação de desempenho. No entanto, dada a [arquitetura distribuída](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) da Hiperescala, foram adicionados diagnósticos adicionais para ajudar. Este artigo descreve dados de diagnóstico específicos da Hiperescala.

## <a name="log-rate-throttling-waits"></a>Taxa de log throttling espera

Todos os níveis de serviço de base de dados Azure SQL têm limites de taxa de geração de registos aplicados através da [governação da taxa de registo](sql-database-resource-limits-database-server.md#transaction-log-rate-governance). Em Hiperescala, o limite de geração de registos está atualmente definido para 100 MB/seg, independentemente do nível de serviço. No entanto, há momentos em que a taxa de produção de registo na réplica do cálculo primário tem de ser acelerada para manter a recuperabilidade dos SLAs. Esta estrangulamento acontece quando um servidor de página ou outra réplica de [computação](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) está significativamente por trás da aplicação de novos registos de registo do serviço Log.

Os seguintes tipos de espera (em [sys.dm_os_wait_stats)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)descrevem as razões pelas quais a taxa de registo pode ser acelerada na réplica do cálculo primário:

|Tipo de espera    |Descrição                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Ocorre quando uma taxa de produção primária de log de dados de base de dados de Hiperscale está a ser acelerada devido ao consumo de registo atrasado no servidor da página.         |
|RBIO_RG_DESTAGE        | Ocorre quando uma taxa de produção de nó de base de dados de hiperescala está a ser acelerada devido ao atraso no consumo de registo pelo armazenamento de registo a longo prazo.         |
|RBIO_RG_REPLICA        | Ocorre quando uma taxa de produção de nó de base de dados de hiperescala está a ser acelerada devido ao consumo de registo atrasado pela réplica secundária legível.         |
|RBIO_RG_LOCALDESTAGE   | Ocorre quando uma taxa de geração de log de dados de base de dados de hiperescala está a ser acelerada devido ao atraso no consumo de registo pelo serviço de registo.         |

## <a name="page-server-reads"></a>Leituras do servidor de página

As réplicas computacionais não cache uma cópia completa da base de dados localmente. Os dados locais para a réplica da computação são armazenados no Buffer Pool (na memória) e na cache local Resilient Buffer Pool Extension (RBPEX) que é uma cache parcial (não-cobertura) de páginas de dados. Esta cache RBPEX local é dimensionada proporcionalmente ao tamanho da computação e é três vezes superior à memória do nível de computação. O RBPEX é semelhante ao Pool Tampão na medida em que tem os dados mais frequentemente acedidos. Cada servidor de página, por outro lado, tem uma cache RBPEX de cobertura para a parte da base de dados que mantém.
 
Quando uma leitura é emitida numa réplica de cálculo, se os dados não existirem no Buffer Pool ou na cache RBPEX local, é emitida uma chamada de função getPage (pageId, LSN) e a página é recolhida a partir do servidor de página correspondente. As leituras dos servidores da página são leituras remotas e são, portanto, mais lentas do que as leituras do RBPEX local. Ao resolver problemas de desempenho relacionados com o IO, precisamos de ser capazes de saber quantos IOs foram feitos através de leituras relativamente lentas do servidor de página remota.

Vários DMVs e eventos estendidos têm colunas e campos que especificam o número de leituras remotas de um servidor de página, que pode ser comparado com o total de leituras. A loja de consultas também captura leituras remotas como parte das estatísticas de tempo de execução da consulta.

- Colunas para relatar leituras de servidores de página estão disponíveis em DMVs de execução e vistas de catálogo, tais como:
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- As leituras do servidor da página são adicionadas aos seguintes eventos alargados:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - consulta-store_execution_runtime_info
- As Leituras do PageServer/RealPageServerReadAheads são adicionadas ao plano de consulta XML para planos reais. Por exemplo:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Para ver estes atributos na janela de propriedades do plano de consulta, é necessário SSMS 18.3 ou posteriormente.

## <a name="virtual-file-stats-and-io-accounting"></a>Estatísticas de ficheiros virtuais e contabilidade iO

Na Base de Dados Azure SQL, o DMF [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) é a principal forma de monitorizar o SQL Server IO. As características iO em Hiperescala são diferentes devido à sua [arquitetura distribuída.](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) Nesta secção, focamo-nos na IO (lê e escreve) em ficheiros de dados como visto neste DMF. Em Hiperescala, cada ficheiro de dados visível neste DMF corresponde a um servidor de página remota. A cache RBPEX mencionada aqui é uma cache local baseada em SSD, que é uma cache não-cobertura na réplica da computação.

### <a name="local-rbpex-cache-usage"></a>Utilização local da cache RBPEX

A cache RBPEX local existe na réplica da computação, no armazenamento local de SSD. Assim, iO contra esta cache é mais rápido que IO contra servidores de página remota. Atualmente, [a sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) numa base de dados de Hiperescala tem uma linha especial que reporta a IO contra a cache RBPEX local na réplica do cálculo. Esta linha tem o valor `database_id` `file_id` de 0 para ambas e colunas. Por exemplo, a consulta abaixo devolve as estatísticas de utilização do RBPEX desde o arranque da base de dados.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Uma relação de leituras feitas em RBPEX para leituras agregadas feitas em todos os outros ficheiros de dados fornece a relação de impacto da cache RBPEX.

### <a name="data-reads"></a>Leituras de dados

- Quando as leituras são emitidas pelo motor SQL Server numa réplica de cálculo, podem ser servidas pelo cache RBPEX local, ou por servidores de página remota, ou por uma combinação dos dois se lerem várias páginas.
- Quando a réplica computacional lê algumas páginas de um ficheiro específico, por exemplo file_id 1, se estes dados residirem exclusivamente na cache RBPEX local, tudo o que é iO para esta leitura é contabilizado contra file_id 0 (RBPEX). Se alguma parte desses dados estiver na cache RBPEX local, e alguma parte estiver num servidor de página remota, então a IO é contabilizada para file_id 0 para a parte servida a partir do RBPEX, e a parte servida a partir do servidor de página remota é contabilizada para file_id 1. 
- Quando uma réplica computacional solicita uma página num [determinado LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) a partir de um servidor de página, se o servidor de página não tiver apanhado o LSN solicitado, a leitura na réplica do cálculo aguardará até que o servidor da página se recupere antes de a página ser devolvida à réplica do cálculo. Para qualquer leitura de um servidor de página na réplica da computação, você verá o tipo de espera PAGEIOLATCH_* se estiver à espera desse IO. Em Hyperscale, este tempo de espera inclui tanto o tempo para recuperar a página solicitada no servidor de página para o LSN necessário, como o tempo necessário para transferir a página do servidor da página para a réplica da computação.
- Grandes leituras como a leitura à frente são muitas vezes feitas usando [leituras "Scatter-Gather".](/sql/relational-databases/reading-pages/) Isto permite leituras de até 4 MB de páginas de cada vez, consideradauma única leitura no motor SQL Server. No entanto, quando os dados que estão a ser lidos estão no RBPEX, estas leituras são contabilizadas como leituras individuais múltiplas de 8 KB, uma vez que o pool tampão e o RBPEX usam sempre 8 páginas KB. Como resultado, o número de IOs lidos observados contra rBPEX pode ser maior do que o número real de IOs realizados pelo motor.

### <a name="data-writes"></a>Data escreve

- A réplica principal do cálculo não escreve diretamente para servidores de página. Em vez disso, os registos de registo do serviço Log são reproduzidos nos servidores de página correspondentes. 
- Escreve que acontecem na réplica da computação são predominantemente escritos para o RBPEX local (file_id 0). Para escritos em ficheiros lógicos superiores a 8 KB, ou seja, aqueles feitos usando [a escrita de Gather](/sql/relational-databases/writing-pages/), cada operação de escrita é traduzida em múltiplas 8 KB escritas individuais para RBPEX uma vez que o pool tampão e RBPEX usam sempre 8 páginas KB. Como resultado, o número de IOs de escrita vistos contra rBPEX pode ser maior do que o número real de IOs realizados pelo motor.
- Ficheiros não RBPEX, ou ficheiros de dados que não sejam file_id 0 que correspondam a servidores de página, também mostram escritos. No nível de serviço hyperscale, estas escritas são simuladas, porque as réplicas computacionais nunca escrevem diretamente para servidores de página. Escreva IOPS e a produção são contabilizadas à medida que ocorrem na réplica do cálculo, mas a latência para ficheiros de dados que não o file_id 0 não reflete a latência real das escritas do servidor da página.

### <a name="log-writes"></a>Log escreve

- No cálculo primário, um log write é contabilizado em file_id 2 de sys.dm_io_virtual_file_stats. Um log write no cálculo primário é uma escrita para a Zona de Aterragem de Registo.
- Os registos de registos não são endurecidos na réplica secundária num compromisso. Em Hiperescala, o log é aplicado pelo serviço Log às réplicas secundárias assincronicamente. Como os registos não ocorrem em réplicas secundárias, qualquer contabilidade de IOs de Log nas réplicas secundárias é apenas para fins de rastreio.

## <a name="data-io-in-resource-utilization-statistics"></a>Dados IO nas estatísticas de utilização de recursos

Numa base de dados não hiperdimensionada, a leitura combinada e a escrita de IOPS contra ficheiros de dados, relativos ao `avg_data_io_percent` limite de dados de [governação](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance) de recursos IOPS, são relatados em [visões sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats,](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) na coluna. O mesmo valor é reportado no portal como _Percentagem de Dados IO_. 

Numa base de dados de Hiperescala, esta coluna reporta sobre a utilização de dados IOPS relativamente `tempdb`ao limite para armazenamento local apenas na réplica de cálculo, especificamente IO contra RBPEX e . Um valor de 100% nesta coluna indica que a governação dos recursos está a limitar o IOPS de armazenamento local. Se isto estiver correlacionado com um problema de desempenho, sintonizar a carga de trabalho para gerar menos IO, ou aumentar o objetivo do serviço de base de dados para aumentar o [limite](sql-database-vcore-resource-limits-single-databases.md)de governação de recursos _Max Data IOPS_ . Para a governação de recursos da RBPEX lê e escreve, o sistema conta com 8 KB IOs individuais, em vez de IOs maiores que podem ser emitidos pelo motor SQL Server. 

Os dados IO contra servidores de páginas remotas não são reportados nas vistas de utilização de recursos ou no portal, mas são relatados no DMF [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) como referido anteriormente.


## <a name="additional-resources"></a>Recursos adicionais

- Para limites de recursos vCore para uma base de dados única de hiperescala ver nível de [serviço de hiperescala vLimiteS](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- Para afinação de desempenho da Base de Dados Azure SQL, consulte o desempenho da Consulta na Base de [Dados Azure SQL](sql-database-performance-guidance.md)
- Para afinação de desempenho utilizando a Consulta Store, consulte [a monitorização do desempenho utilizando a Loja Query](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- Para scripts de monitorização de DMV, consulte [Monitorização do desempenho Azure SQL Database utilizando pontos](sql-database-monitoring-with-dmvs.md) de vista dinâmicos de gestão
