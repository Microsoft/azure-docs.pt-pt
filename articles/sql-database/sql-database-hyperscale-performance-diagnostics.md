---
title: Diagnóstico de desempenho em hiperescala
description: Este artigo descreve como solucionar problemas de desempenho de hiperescala no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: a7c64284c958fa8b3ec89c2b27515fe167a04011
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811153"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnóstico de solução de problemas de desempenho de hiperescala do SQL


Para solucionar problemas de desempenho em um banco de dados de hiperescala, as [metodologias gerais de ajuste de desempenho](sql-database-monitor-tune-overview.md) no nó de computação do banco de dados SQL do Azure é o ponto inicial de uma investigação de desempenho. No entanto, considerando a [arquitetura distribuída](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) de hiperescala, diagnósticos adicionais foram adicionados para auxiliar. Este artigo descreve dados de diagnóstico específicos de hiperescala.


## <a name="log-rate-throttling-waits"></a>Esperas de limitação de taxa de log


Cada nível de serviço do banco de dados SQL do Azure tem limites de taxa de geração de log impostos por meio da [governança de taxa de log](sql-database-resource-limits-database-server.md#transaction-log-rate-governance). Em hiperescala, o limite de geração de log está definido atualmente como 100 MB/s, independentemente do nível de serviço. No entanto, há ocasiões em que a taxa de geração de log na réplica de computação primária deve ser limitada para manter os SLAs de recuperação. Essa limitação ocorre quando um [servidor de página ou outra réplica de computação](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) é significativamente por trás da aplicação de novos registros de log do serviço de log.

Os seguintes tipos de espera (em [Sys. dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) descrevem os motivos pelos quais a taxa de log pode ser limitada na réplica de computação primária:

|Tipo de espera    |Descrição                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Ocorre quando uma taxa de geração de log do nó de computação primário do banco de dados de hiperescala está sendo limitada devido ao consumo de log atrasado no (s) servidor (es) de página.         |
|RBIO_RG_DESTAGE        | Ocorre quando uma taxa de geração de log do nó de computação do banco de dados de hiperescala está sendo limitada devido ao consumo de log atrasado pelo armazenamento de log de longo prazo.         |
|RBIO_RG_REPLICA        | Ocorre quando uma taxa de geração de log do nó de computação do banco de dados de hiperescala está sendo limitada devido ao consumo de log atrasado pelas réplicas secundárias legíveis.         |
|RBIO_RG_LOCALDESTAGE   | Ocorre quando uma taxa de geração de log do nó de computação do banco de dados de hiperescala está sendo limitada devido ao consumo de log atrasado pelo serviço de log.         |


## <a name="page-server-reads"></a>Leituras de servidor de página

As réplicas de computação não armazenam em cache uma cópia completa do banco de dados localmente. Os dados locais para a réplica de computação são armazenados no pool de buffers (na memória) e no cache RBPEX (extensão de pool de buffers resilientes) que é um cache parcial (não abrangendo) de páginas de dados. Esse cache RBPEX local é dimensionado proporcionalmente ao tamanho da computação e é 3 vezes a memória da camada de computação. RBPEX é semelhante ao pool de buffers, pois ele tem os dados acessados com mais frequência. Por outro lado, cada servidor de página tem um cache RBPEX de cobertura para a parte do banco de dados que ele mantém.
 
Quando uma leitura é emitida em uma réplica de computação, se os dados não existirem no pool de buffers ou no cache RBPEX local, uma chamada de função GetPage (PageId, LSN) será emitida e a página será buscada no servidor de páginas correspondente. As leituras de servidores de páginas são leituras remotas e, portanto, são mais lentas do que as leituras do RBPEX local. Ao solucionar problemas de desempenho relacionados a e/s, precisamos saber quantos IOs foram feitos por meio de leituras de servidor de página remota relativamente mais lentas.

Várias DMVs e eventos estendidos têm colunas e campos que especificam o número de leituras remotas de um servidor de páginas que podem ser comparadas com o total de leituras. 

- As leituras de colunas para o servidor de página de relatório estão disponíveis em DMVs de execução, como:
    - [sys. dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys. dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys. dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys. dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
- As leituras de servidor de página são adicionadas aos seguintes eventos estendidos:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - store_execution_runtime_info de consulta
- ActualPageServerReads/ActualPageServerReadAheads são adicionados ao XML do plano de consulta para planos reais. Por exemplo:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Para exibir esses atributos na janela Propriedades do plano de consulta no SSMS, você precisará do SSMS 18,3 ou posterior.


## <a name="virtual-file-stats-and-io-accounting"></a>Estatísticas de arquivo virtual e estatísticas de e/s

No banco de dados SQL do Azure, a Dmf [Sys. dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) é a principal maneira de monitorar SQL Server e/s. As características de e/s em hiperescala são diferentes devido à sua [arquitetura distribuída](sql-database-service-tier-hyperscale.md#distributed-functions-architecture). Nesta seção, nos concentramos na e/s (leituras e gravações) nos arquivos de dados, como visto nessa DMF. Em hiperescala, cada arquivo de dados visível nessa DMF corresponde a um servidor de página remoto. O cache RBPEX mencionado aqui é um cache local baseado em SSD que é um cache sem cobertura na réplica de computação.


### <a name="local-rbpex-cache-usage"></a>Uso do cache RBPEX local

O cache RBPEX local existe no nó de computação no armazenamento SSD local. Portanto, a e/s nesse cache RBPEX é mais rápida do que a e/s em servidores de página remota. Atualmente, [Sys. dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) em um banco de dados de hiperescala tem uma linha especial relatando a e/s feita no cache RBPEX local na réplica de computação. Essa linha tem o valor 0 para as colunas `database_id` e `file_id`. Por exemplo, a consulta abaixo retorna estatísticas de uso de RBPEX desde a inicialização do banco de dados.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Uma taxa de leituras feitas em RBPEX para leituras agregadas feitas em todos os outros arquivos de dados fornece a taxa de acertos do cache RBPEX.


### <a name="data-reads"></a>Leituras de dados

- Quando as leituras são emitidas pelo mecanismo de SQL Server em uma réplica de computação, elas podem ser servidas pelo cache RBPEX local ou por servidores de página remota, ou por uma combinação dos dois, se estiver lendo várias páginas.
- Quando a réplica de computação lê algumas páginas de um arquivo específico, por exemplo file_id 1, se esses dados residem exclusivamente no cache RBPEX local, toda a e/s para essa leitura é contabilizada em file_id 0 (RBPEX). Se alguma parte desses dados estiver no cache RBPEX local e alguma parte estiver em um servidor de página remoto, a e/s será contabilizada em relação a file_id 0 para a parte servida de RBPEX, e a parte servida do servidor de página remoto será contabilizada em direção a file_id 1. 
- Quando uma réplica de computação solicitar uma página em um determinado [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) de um servidor de página, se o servidor de página não tiver sido detectado até o LSN solicitado, a leitura na réplica de computação aguardará até que o servidor de página seja exibido antes que a página seja retornada para a réplica de computação. Para qualquer leitura de um servidor de página na réplica de computação, você verá o tipo de espera PAGEIOLATCH_ * se ele estiver aguardando nessa e/s. Esse tempo de espera inclui o tempo para acompanhar a página solicitada no servidor de página para o LSN necessário e o tempo necessário para transferir a página do servidor de página para a réplica de computação.
- Leituras grandes, como Read-Ahead, geralmente são feitas usando [leituras de "dispersão"](/sql/relational-databases/reading-pages/). Isso permite leituras de até 4 MB de páginas por vez, consideradas uma única leitura no mecanismo de SQL Server. No entanto, quando os dados que estão sendo lidos estiverem em RBPEX, essas leituras serão contadas como várias leituras individuais de 8 KB, pois o pool de buffers e RBPEX sempre usarão páginas de 8 KB. Como resultado, o número de IOs de leitura visto em relação a RBPEX pode ser maior do que o número real de IOs executado pelo mecanismo.


### <a name="data-writes"></a>Gravações de dados

- A réplica de computação primária não grava diretamente em servidores de página. Em vez disso, os registros de log do serviço de log são reproduzidos nos servidores de página correspondentes. 
- As gravações que acontecem na réplica de computação são, predominantemente, gravações no RBPEX local (file_id 0). Para gravações em arquivos lógicos maiores que 8 KB, ou seja, aquelas feitas usando o [gather-Write](/sql/relational-databases/writing-pages/), cada operação de gravação é convertida em várias gravações individuais de 8 KB em RBPEX, já que o pool de buffers e RBPEX sempre usam oito páginas de 8 KB. Como resultado, o número de IOs de gravação visto em relação a RBPEX pode ser maior do que o número real de IOs executado pelo mecanismo.
- Arquivos não RBPEX ou arquivos de dados diferentes de file_id 0 que correspondem a servidores de página, também mostram gravações. Na camada de serviço de hiperescala, essas gravações são simuladas, pois as réplicas de computação nunca gravam diretamente em servidores de página. O IOPS de gravação e a taxa de transferência são contabilizados à medida que ocorrem na réplica de computação, mas a latência de arquivos de dados diferentes de file_id 0 não reflete a latência real das gravações do servidor de página.

### <a name="log-writes"></a>Gravações de log

- Na computação primária, uma gravação de log é contabilizada no file_id 2 de sys. dm_io_virtual_file_stats. Uma gravação de log na computação principal é uma gravação na zona de aterrissagem de log.
- Os registros de log não são protegidos na réplica secundária em uma confirmação. Em hiperescala, o log é aplicado pelo serviço xlog às réplicas remotas. Como as gravações de log não ocorrem na verdade em réplicas secundárias, todas as estatísticas de e/s de log nas réplicas secundárias são apenas para fins de acompanhamento.

## <a name="additional-resources"></a>Recursos Adicionais

- Para limites de recursos vCore para um banco de dados individual de hiperescala, consulte [limites vCore da camada de serviço de hiperescala](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- Para ajuste de desempenho do banco de dados SQL do Azure, consulte [desempenho de consulta no banco de dados SQL do Azure](sql-database-performance-guidance.md)
- Para ajuste de desempenho usando Repositório de Consultas, consulte [monitoramento de desempenho usando o repositório de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- Para scripts de monitoramento DMV, consulte [monitorando o desempenho do banco de dados SQL do Azure usando exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md)
