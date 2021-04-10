---
title: Gestão de recursos em conjuntos elásticos densos
description: Gerir recursos computativos em piscinas elásticas Azure SQL Database com muitas bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 40b6c5a86184860cf3e7a9840f980706485ae977
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572233"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Gestão de recursos em conjuntos elásticos densos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

As [piscinas elásticas](./elastic-pool-overview.md) Azure SQL Database são uma solução rentável para gerir muitas bases de dados com uma utilização variada de recursos. Todas as bases de dados de uma piscina elástica partilham a mesma alocação de recursos, tais como CPU, memória, fios de trabalhadores, espaço de armazenamento, temporário, assumindo que **apenas um subconjunto de bases de dados na piscina utilizará recursos computacional a qualquer momento.** Esta suposição permite que piscinas elásticas sejam rentáveis. Em vez de pagarem todos os recursos que cada base de dados poderia potencialmente necessitar, os clientes pagam por um conjunto muito menor de recursos, partilhados entre todas as bases de dados da piscina.

## <a name="resource-governance"></a>Gestão de recursos

A partilha de recursos requer que o sistema controle cuidadosamente o uso de recursos para minimizar o efeito "vizinho barulhento", onde uma base de dados com elevado consumo de recursos afeta outras bases de dados na mesma piscina elástica. Ao mesmo tempo, o sistema deve fornecer recursos suficientes para funcionalidades como a elevada disponibilidade e recuperação de desastres (HADR), cópia de segurança e restauro, monitorização, loja de consultas, sintonização automática, etc. para funcionar de forma fiável.

A Azure SQL Database alcança estes objetivos utilizando múltiplos mecanismos de governação de recursos, incluindo o Windows [Job Objects](/windows/win32/procthread/job-objects) para a governação de recursos de nível de processo, o Gestor de Recursos do Servidor do Windows [(FSRM)](/windows-server/storage/fsrm/fsrm-overview) para a gestão de quotas de armazenamento, e uma versão modificada e alargada do Governador de [Recursos](/sql/relational-databases/resource-governor/resource-governor) do Servidor SQL para implementar a governação de recursos dentro da Base de Dados SQL.

O objetivo primário de design de piscinas elásticas é ser rentável. Por esta razão, o sistema permite intencionalmente que os clientes criem piscinas _densas,_ isto é, piscinas com o número de bases de dados que se aproximam ou no máximo permitido, mas com uma alocação moderada de recursos computacional. Pela mesma razão, o sistema não reserva todos os recursos potencialmente necessários para os seus processos internos, mas permite a partilha de recursos entre processos internos e cargas de trabalho dos utilizadores.

Esta abordagem permite que os clientes utilizem piscinas elásticas densas para alcançar um desempenho adequado e grandes economias de custos. No entanto, se a carga de trabalho contra muitas bases de dados numa piscina densa for suficientemente intensa, a contenção dos recursos torna-se significativa. A contenção de recursos reduz o desempenho da carga de trabalho do utilizador e pode impactar negativamente os processos internos.

> [!IMPORTANT]
> Em piscinas densas com muitas bases de dados ativas, pode não ser viável aumentar o número de bases de dados na piscina até aos máximos documentados para piscinas elásticas [DTU](resource-limits-dtu-elastic-pools.md) e [vCore.](resource-limits-vcore-elastic-pools.md)
>
> O número de bases de dados que podem ser colocadas em piscinas densas sem causar problemas de contenção de recursos e desempenho depende do número de bases de dados simultaneamente ativas e do consumo de recursos por cargas de trabalho dos utilizadores em cada base de dados. Este número pode mudar ao longo do tempo à medida que as cargas de trabalho do utilizador mudam.

Quando a contenção de recursos ocorre numa piscina densamente embalada, os clientes podem escolher uma ou mais das seguintes ações para mitigar:

- Afinar a carga de trabalho de consulta para reduzir o consumo de recursos ou espalhar o consumo de recursos em várias bases de dados ao longo do tempo.
- Reduza a densidade do pool movendo algumas bases de dados para outra piscina, ou tornando-as bases de dados autónomas.
- Dimensione a piscina para obter mais recursos.

Para sugestões sobre como implementar as duas últimas ações, consulte [recomendações operacionais](#operational-recommendations) mais tarde neste artigo. A redução da contenção de recursos beneficia tanto as cargas de trabalho dos utilizadores como os processos internos, e permite que o sistema mantenha de forma fiável o nível de serviço esperado.

## <a name="monitoring-resource-consumption"></a>Monitorização do consumo de recursos

Para evitar a degradação do desempenho devido à contenção de recursos, os clientes que utilizam piscinas elásticas densas devem monitorizar proactivamente o consumo de recursos e tomar medidas oportunas se a crescente contenção de recursos começar a afetar as cargas de trabalho. A monitorização contínua é importante porque a utilização de recursos numa piscina muda ao longo do tempo, devido a alterações na carga de trabalho do utilizador, alterações nos volumes de dados e distribuição, alterações na densidade do pool e alterações no serviço de Base de Dados Azure SQL.

A Azure SQL Database fornece várias métricas que são relevantes para este tipo de monitorização. Exceder o valor médio recomendado para cada métrica indica contenção de recursos na piscina, e deve ser abordado usando uma das ações mencionadas anteriormente.

|Nome da métrica|Description|Valor médio recomendado|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Utilização do CPU do processo SQL associado a uma piscina elástica, medida pelo sistema operativo subjacente. Disponível na [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vista em todas as bases de dados e na [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) vista na base `master` de dados. Esta métrica também é emitida para o Azure Monitor, onde é [nomeado](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `sqlserver_process_core_percent` , e pode ser vista no portal Azure. Este valor é o mesmo para cada base de dados na mesma piscina elástica.|Abaixo dos 70%. Picos curtos ocasionais até 90% podem ser aceitáveis.|
|`max_worker_percent`|[Utilização do fio]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) do trabalhador. Fornecido para cada base de dados na piscina, bem como para a própria piscina. Existem limites diferentes no número de fios de trabalhadores ao nível da base de dados, e ao nível da piscina, pelo que é recomendada a monitorização desta métrica em ambos os níveis. Disponível na [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vista em todas as bases de dados e na [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) vista na base `master` de dados. Esta métrica também é emitida para o Azure Monitor, onde é [nomeado](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `workers_percent` , e pode ser vista no portal Azure.|Abaixo dos 80%. Picos até 100% farão com que tentativas de ligação e consultas falhem.|
|`avg_data_io_percent`|Utilização do IOPS para leitura e escrita física IO. Fornecido para cada base de dados na piscina, bem como para a própria piscina. Existem limites diferentes no número de IOPS ao nível da base de dados, e ao nível da piscina, pelo que é recomendada a monitorização desta métrica em ambos os níveis. Disponível na [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vista em todas as bases de dados e na [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) vista na base `master` de dados. Esta métrica também é emitida para o Azure Monitor, onde é [nomeado](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `physical_data_read_percent` , e pode ser vista no portal Azure.|Abaixo dos 80%. Picos curtos ocasionais até 100% podem ser aceitáveis.|
|`avg_log_write_percent`|Utilizações de produção para registo de transações escrevem IO. Fornecido para cada base de dados na piscina, bem como para a própria piscina. Existem limites diferentes na produção de registo ao nível da base de dados, e ao nível da piscina, pelo que é recomendada a monitorização desta métrica em ambos os níveis. Disponível na [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vista em todas as bases de dados e na [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) vista na base `master` de dados. Esta métrica também é emitida para o Azure Monitor, onde é [nomeado](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `log_write_percent` , e pode ser vista no portal Azure. Quando esta métrica estiver perto de 100%, todas as modificações da base de dados (INSERT, UPDATE, DELETE, MERGE statements, SELECT ... INSERÇÃO A GRANEL, ETC.) será mais lento.|Abaixo dos 90%. Picos curtos ocasionais até 100% podem ser aceitáveis.|
|`oom_per_second`|A taxa de erros fora da memória (OOM) numa piscina elástica, que é um indicador de pressão de memória. Disponível na vista [sys.dm_resource_governor_resource_pools_history_ex.](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) Consulte [exemplos](#examples) para uma consulta de amostra para calcular esta métrica.|0|
|`avg_storage_percent`|Espaço de armazenamento total utilizado por dados em todas as bases de dados dentro de uma piscina elástica. Não inclui espaço vazio em ficheiros de base de dados. Disponível na [vista sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) na base de `master` dados. Esta métrica também é emitida para o Azure Monitor, onde é [nomeado](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `storage_percent` , e pode ser vista no portal Azure.|Abaixo dos 80%. Pode aproximar-se a 100% para piscinas sem crescimento de dados.|
|`avg_allocated_storage_percent`|Espaço de armazenamento total utilizado por ficheiros de base de dados em armazenamento em todas as bases de dados dentro de uma piscina elástica. Inclui espaço vazio em ficheiros de base de dados. Disponível na [vista sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) na base de `master` dados. Esta métrica também é emitida para o Azure Monitor, onde é [nomeado](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) `allocated_data_storage_percent` , e pode ser vista no portal Azure.|Abaixo dos 90%. Pode aproximar-se a 100% para piscinas sem crescimento de dados.|
|`tempdb_log_used_percent`|Utilização do espaço de registo de transações na `tempdb` base de dados. Embora os objetos temporários criados numa base de dados não sejam visíveis noutras bases de dados na mesma piscina elástica, `tempdb` é um recurso partilhado para todas as bases de dados na mesma piscina. Uma transação de longa duração ou órfão `tempdb` iniciada a partir de uma base de dados na piscina pode consumir uma grande parte do registo de transações, e causar falhas para consultas em outras bases de dados na mesma piscina. Derivado de [sys.dm_db_log_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) e [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) pontos de vista. Esta métrica também é emitida ao Azure Monitor, e pode ser vista no portal Azure. Consulte [exemplos](#examples) para uma consulta de amostra para devolver o valor atual desta métrica.|Abaixo dos 50%. Picos ocasionais até 80% são aceitáveis.|
|||

Além destas métricas, a Azure SQL Database fornece uma visão que devolve os limites reais de governação dos recursos, bem como pontos de vista adicionais que devolvem estatísticas de utilização de recursos ao nível do pool de recursos, e ao nível do grupo de carga de trabalho.

|Ver nome|Description|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Devolve as configurações reais e as definições de capacidade utilizadas pelos mecanismos de governação dos recursos na base de dados atual ou na piscina elástica.|
|[sys.dm_resource_governor_resource_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Devolve informações sobre o estado atual do pool de recursos, a configuração atual dos pools de recursos e as estatísticas acumuladas de conjunto de recursos.|
|[sys.dm_resource_governor_workload_groups](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Devolve as estatísticas acumuladas do grupo de carga de trabalho e a configuração atual do grupo de carga de trabalho. Esta vista pode ser acompanhada de sys.dm_resource_governor_resource_pools na `pool_id` coluna para obter informações sobre o pool de recursos.|
|[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Devolve estatísticas de utilização do pool de recursos nos últimos 32 minutos. Cada linha representa um intervalo de 20 segundos. As `delta_` colunas devolvem a alteração em cada estatística durante o intervalo.|
|[sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Devolve as estatísticas de utilização do grupo de carga de trabalho nos últimos 32 minutos. Cada linha representa um intervalo de 20 segundos. As `delta_` colunas devolvem a alteração em cada estatística durante o intervalo.|
|||

Estas vistas podem ser usadas para monitorizar a utilização de recursos e a resolução de problemas de contenção de recursos em tempo real. A carga de trabalho do utilizador nas réplicas secundárias primárias e legíveis, incluindo geo-réplicas, é classificada no `SloSharedPool1` grupo de recursos e grupo de carga de `UserPrimaryGroup.DBId[N]` trabalho, onde se encontra o valor `N` de ID da base de dados.

Além de monitorizar a utilização atual de recursos, os clientes que usam piscinas densas podem manter dados históricos de utilização de recursos numa loja de dados separada. Estes dados podem ser usados em análise preditiva para gerir proativamente a utilização de recursos com base em tendências históricas e sazonais.

## <a name="operational-recommendations"></a>Recomendações operacionais

**Deixe espaço suficiente para recursos**. Se ocorrer contenção de recursos e degradação do desempenho, a mitigação pode implicar a deslocação de algumas bases de dados para fora da piscina elástica afetada, ou o escalonamento da piscina, como já foi notado anteriormente. No entanto, estas ações requerem recursos de computação adicionais para ser concluída. Em particular, para os pools Premium e Business Critical, estas ações requerem a transferência de todos os dados para as bases de dados que estão a ser movidas, ou para todas as bases de dados na piscina elástica se o pool for dimensionado. A transferência de dados é uma operação de longa duração e intensiva de recursos. Se a piscina já estiver sob alta pressão de recursos, a própria operação atenuante irá degradar ainda mais o desempenho. Em casos extremos, pode não ser possível resolver a contenção de recursos através de movimento de base de dados ou de escala de piscina porque os recursos necessários não estão disponíveis. Neste caso, a redução temporária da carga de trabalho de consulta na piscina elástica afetada pode ser a única solução.

Os clientes que utilizam piscinas densas devem monitorizar de perto as tendências de utilização dos recursos, tal como descrito anteriormente, e tomar medidas atenuantes enquanto as métricas permanecem dentro das gamas recomendadas e ainda existem recursos suficientes na piscina elástica.

A utilização de recursos depende de múltiplos fatores que mudam ao longo do tempo para cada base de dados e cada piscina elástica. Alcançar a melhor relação preço/desempenho em piscinas densas requer monitorização e reequilíbrio contínuos, que está a mover bases de dados de piscinas mais utilizadas para piscinas menos utilizadas, e a criar novas piscinas, conforme necessário para acomodar o aumento da carga de trabalho.

**Não mova bases de dados "quentes".** Se a contenção de recursos ao nível da piscina for causada principalmente por um pequeno número de bases de dados altamente utilizadas, pode ser tentador mover estas bases de dados para um pool menos utilizado, ou torná-las bases de dados autónomas. No entanto, não é aconselhável fazê-lo enquanto uma base de dados permanece altamente utilizada, uma vez que a operação de movimento irá degradar ainda mais o desempenho, tanto para a base de dados que está a ser movida, como para toda a piscina. Em vez disso, espere até que a alta utilização diminua, ou mova bases de dados menos utilizadas em vez de aliviar a pressão dos recursos ao nível da piscina. Mas mover bases de dados com uma utilização muito baixa não proporciona qualquer benefício neste caso, porque não reduz substancialmente a utilização de recursos ao nível da piscina.

**Criar novas bases de dados numa piscina de "quarentena".** Em cenários em que novas bases de dados são criadas com frequência, como aplicações utilizando o modelo de arrendatário por base de dados, existe o risco de uma nova base de dados colocada num pool elástico existente consumir inesperadamente recursos significativos e afetar outras bases de dados e processos internos na piscina. Para mitigar este risco, crie uma piscina separada de "quarentena" com ampla alocação de recursos. Utilize este pool para novas bases de dados com padrões de consumo de recursos ainda desconhecidos. Uma vez que uma base de dados tenha permanecido neste pool para um ciclo de negócios, como uma semana ou um mês, e o seu consumo de recursos é conhecido, pode ser movido para uma piscina com capacidade suficiente para acomodar este uso adicional de recursos.

**Monitorize o espaço utilizado e atribuído.** Quando o espaço da piscina atribuído (tamanho total de todos os ficheiros de base de dados armazenados para todas as bases de dados de uma piscina) atinge o tamanho máximo da piscina, podem ocorrer erros fora do espaço. Se as tendências espaciais atribuídas forem elevadas e estiverem no caminho certo para atingir o tamanho máximo da piscina, as opções de mitigação incluem:
- Tire algumas bases de dados da piscina para reduzir o espaço total atribuído
- [Encolher](file-space-manage.md) ficheiros de base de dados para reduzir o espaço vazio atribuído em ficheiros
- Dimensione a piscina para um objetivo de serviço com um tamanho máximo de piscina maior

Se o espaço de piscina usado (tamanho total de dados em todas as bases de dados de uma piscina, sem incluir o espaço vazio em ficheiros) tendências altas e está no caminho certo para alcançar o tamanho máximo da piscina, as opções de mitigação incluem:
- Tire algumas bases de dados da piscina para reduzir o espaço total usado
- Mover (arquivo) dados para fora da base de dados, ou apagar os dados já não necessários
- Implementar [compressão de dados](/sql/relational-databases/data-compression/data-compression)
- Dimensione a piscina para um objetivo de serviço com um tamanho máximo de piscina maior

**Evite servidores excessivamente densos**. A Azure SQL Database [suporta](./resource-limits-logical-server.md) até 5000 bases de dados por servidor. Os clientes que usam piscinas elásticas com milhares de bases de dados podem considerar a colocação de várias piscinas elásticas num único servidor, com o número total de bases de dados até ao limite suportado. No entanto, servidores com muitos milhares de bases de dados criam desafios operacionais. As operações que requerem a enumeração de todas as bases de dados num servidor, por exemplo, a visualização de bases de dados no portal, serão mais lentas. Erros operacionais, tais como modificação incorreta de logins de nível do servidor ou regras de firewall, afetarão um maior número de bases de dados. A eliminação acidental do servidor necessitará da assistência do Microsoft Support para recuperar bases de dados no servidor eliminado e causará uma interrupção prolongada para todas as bases de dados afetadas.

Recomendamos limitar o número de bases de dados por servidor a um número inferior ao máximo suportado. Em muitos cenários, usar até 1000-2000 bases de dados por servidor é o ideal. Para reduzir a probabilidade de eliminação acidental do servidor, recomendamos a colocação de um bloqueio de [exclusão](../../azure-resource-manager/management/lock-resources.md) no servidor ou no seu grupo de recursos.

No passado, certos cenários que envolvem bases de dados móveis dentro, fora ou entre piscinas elásticas no mesmo servidor eram mais rápidos do que quando se moviam bases de dados entre servidores. Atualmente, todos os movimentos de base de dados executam à mesma velocidade, independentemente do servidor de origem e destino.

## <a name="examples"></a>Exemplos

### <a name="monitoring-memory-utilization"></a>Monitorização da utilização da memória

Esta consulta calcula a `oom_per_second` métrica de cada conjunto de recursos, ao longo dos últimos 32 minutos. Esta consulta pode ser executada em qualquer base de dados de uma piscina elástica.

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>Monitorização da `tempdb` utilização do espaço do log

Esta consulta devolve o valor atual da `tempdb_log_used_percent` métrica, mostrando a utilização relativa do registo de `tempdb` transações em relação ao seu tamanho máximo permitido. Esta consulta pode ser executada em qualquer base de dados de uma piscina elástica.

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>Passos seguintes

- Para uma introdução a piscinas elásticas, consulte [piscinas elásticas que o ajudam a gerir e escalar várias bases de dados na Base de Dados Azure SQL](./elastic-pool-overview.md).
- Para obter informações sobre a sintonização das cargas de trabalho para reduzir a utilização dos recursos, consulte [monitorização e afinação]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index), e [monitorização e afinação de desempenho](./monitor-tune-overview.md).