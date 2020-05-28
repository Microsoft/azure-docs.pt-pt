---
title: Gestão de recursos em conjuntos elásticos densos
description: Gerencie os recursos computacionais em piscinas elásticas azure SQL Database com muitas bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 03/13/2019
ms.openlocfilehash: 1db8eeecf411ae219474029e09cb866aaf0d5bbe
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045726"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Gestão de recursos em conjuntos elásticos densos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Os [pools elásticos](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) Azure SQL Database são uma solução rentável para gerir muitas bases de dados com uso de recursos variado. Todas as bases de dados de um pool elástico partilham a mesma alocação de recursos, tais como CPU, memória, fios de trabalhadores, espaço de armazenamento, tempdb, partindo do pressuposto de que **apenas um subconjunto de bases de dados na piscina utilizará recursos computacionais a qualquer momento.** Esta suposição permite que as piscinas elásticas sejam rentáveis. Em vez de pagar todos os recursos que cada base de dados poderia potencialmente necessitar, os clientes pagam por um conjunto muito menor de recursos, partilhados entre todas as bases de dados da piscina.

## <a name="resource-governance"></a>Governação de recursos

A partilha de recursos requer que o sistema controle cuidadosamente o uso de recursos para minimizar o efeito "vizinho barulhento", onde uma base de dados com elevado consumo de recursos afeta outras bases de dados no mesmo pool elástico. Ao mesmo tempo, o sistema deve fornecer recursos suficientes para funcionalidades como elevada disponibilidade e recuperação de desastres (HADR), backup e restauro, monitorização, Loja de Consulta, Afinação Automática, etc. para funcionar de forma fiável.

A Base de Dados Azure SQL alcança estes objetivos utilizando múltiplos mecanismos de governação de recursos, incluindo [objetos](https://docs.microsoft.com/windows/win32/procthread/job-objects) de trabalho do Windows para governação de recursos de nível de processo, Gestor de Recursos do Servidor de Ficheiros windows [(FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) para gestão de quotas de armazenamento, e uma versão modificada e estendida do SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) para implementar a governação de recursos dentro da Base de Dados SQL.

O principal objetivo de design das piscinas elásticas é ser rentável. Por esta razão, o sistema permite intencionalmente que os clientes criem piscinas _densas,_ ou seja, piscinas com o número de bases de dados que se aproximam ou no máximo permitido, mas com uma alocação moderada de recursos computacionais. Pela mesma razão, o sistema não reserva todos os recursos potencialmente necessários para os seus processos internos, mas permite a partilha de recursos entre processos internos e cargas de trabalho dos utilizadores.

Esta abordagem permite que os clientes utilizem piscinas elásticas densas para obter um desempenho adequado e grandes economias de custos. No entanto, se a carga de trabalho contra muitas bases de dados numa piscina densa for suficientemente intensa, a contenção dos recursos torna-se significativa. A contenção de recursos reduz o desempenho da carga de trabalho do utilizador e pode impactar negativamente os processos internos.

> [!IMPORTANT]
> Em piscinas densas com muitas bases de dados ativas, pode não ser possível aumentar o número de bases de dados na piscina até aos máximos documentados para piscinas elásticas [DTU](resource-limits-dtu-elastic-pools.md) e [vCore.](resource-limits-vcore-elastic-pools.md)
>
> O número de bases de dados que podem ser colocadas em piscinas densas sem causar a contenção de recursos e problemas de desempenho depende do número de bases de dados simultaneamente ativas e do consumo de recursos por cargas de trabalho dos utilizadores em cada base de dados. Este número pode mudar ao longo do tempo à medida que as cargas de trabalho dos utilizadores mudam.

Quando a contenção de recursos ocorre numa piscina densamente embalada, os clientes podem escolher uma ou mais das seguintes ações para mitigar:

- Sintonize a carga de trabalho para reduzir o consumo de recursos ou espalhe o consumo de recursos em várias bases de dados ao longo do tempo.
- Reduza a densidade da piscina movendo algumas bases de dados para outra piscina, ou tornando-as bases de dados autónomas.
- Aumentar a piscina para obter mais recursos.

Para sugestões sobre como implementar as duas últimas ações, consulte [recomendações operacionais](#operational-recommendations) mais tarde neste artigo. A redução da contenção de recursos beneficia tanto as cargas de trabalho dos utilizadores como os processos internos, e permite que o sistema mantenha de forma fiável o nível de serviço esperado.

## <a name="monitoring-resource-consumption"></a>Monitorização do consumo de recursos

Para evitar a degradação do desempenho devido à contenção de recursos, os clientes que usam piscinas elásticas densas devem monitorizar proativamente o consumo de recursos e tomar medidas atempadas se o aumento da contenção de recursos começar a afetar as cargas de trabalho. A monitorização contínua é importante porque o uso de recursos num pool muda ao longo do tempo, devido a alterações na carga de trabalho dos utilizadores, alterações nos volumes de dados e distribuição, alterações na densidade do pool e alterações no serviço de base de dados Azure SQL.

A Base de Dados Azure SQL fornece várias métricas que são relevantes para este tipo de monitorização. Exceder o valor médio recomendado para cada métrica indica a contenção de recursos na piscina, e deve ser abordado usando uma das ações mencionadas anteriormente.

|Nome da métrica|Descrição|Valor médio recomendado|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Utilização do CPU do processo SQL associado a um conjunto elástico, medido pelo sistema operativo subjacente. Disponível na base de dados dm_db_resource_stats [sys.dm_db_resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) em cada [base](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) de dados, e na elastic_pool_resource_stats. `master` Esta métrica também é emitida para o Monitor Azure, onde é [nomeado](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent` , e pode ser visto no portal Azure. Este valor é o mesmo para cada base de dados da mesma piscina elástica.|Abaixo dos 70%. Picos curtos ocasionais até 90% podem ser aceitáveis.|
|`max_worker_percent`|[Utilização]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) de fios de trabalhador. Fornecido para cada base de dados na piscina, bem como para a própria piscina. Existem limites diferentes no número de fios dos trabalhadores ao nível da base de dados e, portanto, ao nível da piscina, recomenda-se a monitorização desta métrica em ambos os níveis. Disponível na base de dados dm_db_resource_stats [sys.dm_db_resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) em cada [base](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) de dados, e na elastic_pool_resource_stats. `master` Esta métrica também é emitida para o Monitor Azure, onde é [nomeado](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent` , e pode ser visto no portal Azure.|Abaixo dos 80%. Picos até 100% causarão tentativas de ligação e consultas falhadas.|
|`avg_data_io_percent`|Utilização do IOPS para leitura e escrita física IO. Fornecido para cada base de dados na piscina, bem como para a própria piscina. Existem limites diferentes para o número de IOPS ao nível da base de dados, pelo que é recomendado monitorizar esta métrica em ambos os níveis. Disponível na base de dados dm_db_resource_stats [sys.dm_db_resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) em cada [base](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) de dados, e na elastic_pool_resource_stats. `master` Esta métrica também é emitida para o Monitor Azure, onde é [nomeado](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent` , e pode ser visto no portal Azure.|Abaixo dos 80%. Picos curtos ocasionais até 100% podem ser aceitáveis.|
|`avg_log_write_percent`|Utilizações de entrada para log de transações escrevem IO. Fornecido para cada base de dados na piscina, bem como para a própria piscina. Existem diferentes limites na entrada de log a nível da base de dados, e ao nível da piscina, recomenda-se, portanto, monitorizar esta métrica em ambos os níveis. Disponível na base de dados dm_db_resource_stats [sys.dm_db_resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) em cada [base](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) de dados, e na elastic_pool_resource_stats. `master` Esta métrica também é emitida para o Monitor Azure, onde é [nomeado](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent` , e pode ser visto no portal Azure. Quando esta métrica estiver perto de 100%, todas as modificações da base de dados (INSERT, UPDATE, DELETE, MERGE statements, SELECT ... INTO, INSERT A GRANEL, etc.) será mais lento.|Abaixo dos 90%. Picos curtos ocasionais até 100% podem ser aceitáveis.|
|`oom_per_second`|A taxa de erros fora de memória (OOM) num pool elástico, que é um indicador de pressão de memória. Disponível na vista [sys.dm_resource_governor_resource_pools_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) Consulte [exemplos](#examples) para uma consulta de amostra para calcular esta métrica.|0|
|`avg_storage_percent`|Utilização do espaço de armazenamento ao nível da piscina elástica. Disponível na base de dados [elastic_pool_resource_stats sys.elastic_pool_resource_stats.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) `master` Esta métrica também é emitida para o Monitor Azure, onde é [nomeado](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent` , e pode ser visto no portal Azure.|Abaixo dos 80%. Pode aproximar-se a 100% para piscinas sem crescimento de dados.|
|`tempdb_log_used_percent`|Utilização de espaço de registo de transações na base de `tempdb` dados. Embora os objetos temporários criados numa base de dados não sejam visíveis noutras bases de dados na mesma piscina elástica, é um recurso partilhado para todas as bases de `tempdb` dados da mesma piscina. Uma longa transação de execução ou ocioso `tempdb` iniciada a partir de uma base de dados na piscina pode consumir uma grande parte do registo de transações, e causar falhas em consultas em outras bases de dados no mesmo pool. Disponível na vista [sys.dm_db_log_space_usage.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) Esta métrica também é emitida para o Monitor Azure, e pode ser vista no portal Azure. Consulte [exemplos](#examples) para uma consulta de amostra para devolver o valor atual desta métrica.|Abaixo dos 50%. Picos ocasionais até 80% são aceitáveis.|
|||

Além destas métricas, a Azure SQL Database fornece uma visão que devolve os limites reais de governação dos recursos, bem como pontos de vista adicionais que devolvem as estatísticas de utilização de recursos ao nível do conjunto de recursos, e ao nível do grupo de carga de trabalho.

|Ver nome|Descrição|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Devolve as configurações reais de configuração e capacidade utilizadas pelos mecanismos de governação de recursos na base de dados atual ou no pool elástico.|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Devolve informações sobre o estado atual do conjunto de recursos, a configuração atual de conjuntos de recursos e estatísticas cumulativas de recursos pool.|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Devolve as estatísticas acumuladas do grupo de trabalho e a configuração atual do grupo de carga de trabalho. Esta vista pode ser unida com sys.dm_resource_governor_resource_pools na coluna para obter informações do conjunto de `pool_id` recursos.|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Devolve as estatísticas de utilização do conjunto de recursos nos últimos 32 minutos. Cada linha representa um intervalo de 20 segundos. As `delta_` colunas devolvem a mudança em cada estatística durante o intervalo.|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Devolve as estatísticas de utilização do grupo de trabalho nos últimos 32 minutos. Cada linha representa um intervalo de 20 segundos. As `delta_` colunas devolvem a mudança em cada estatística durante o intervalo.|
|||

Estas opiniões podem ser utilizadas para monitorizar a utilização de recursos e a resolução de recursos em quase tempo real. A carga de trabalho dos utilizadores nas réplicas secundárias primárias e legíveis, incluindo georéplicas, está classificada no grupo de recursos e carga de trabalho, onde se destaca o valor de ID da base de `SloSharedPool1` `UserPrimaryGroup.DBId[N]` `N` dados.

Além de monitorizar a utilização atual de recursos, os clientes que utilizam piscinas densas podem manter dados históricos de utilização de recursos numa loja de dados separada. Estes dados podem ser utilizados em análises preditivas para gerir proativamente a utilização de recursos com base em tendências históricas e sazonais.

## <a name="operational-recommendations"></a>Recomendações operacionais

**Deixe a sala de recursos suficiente**. Se ocorrer a contenção de recursos e a degradação do desempenho, a mitigação pode implicar a deslocação de algumas bases de dados para fora da piscina elástica afetada, ou a ampliação da piscina, como notado anteriormente. No entanto, estas ações requerem recursos computacionais adicionais para ser concluídas. Em particular, para os pools Premium e Business Critical, estas ações exigem a transferência de todos os dados para as bases de dados que estão a ser movidas, ou para todas as bases de dados do pool elástico se o pool estiver dimensionado. A transferência de dados é uma operação de longo funcionamento e de recursos intensivos. Se a piscina já estiver sob alta pressão de recursos, a operação mitigadora em si irá degradar ainda mais o desempenho. Em casos extremos, pode não ser possível resolver a contenção de recursos através de movimentos de base de dados ou de escala de piscina porque os recursos necessários não estão disponíveis. Neste caso, a redução temporária da carga de trabalho de consulta na piscina elástica afetada pode ser a única solução.

Os clientes que utilizam piscinas densas devem monitorizar de perto as tendências de utilização de recursos, tal como descrito anteriormente, e tomar medidas mitigadoras enquanto as métricas permanecem dentro das gamas recomendadas e ainda existem recursos suficientes no pool elástico.

A utilização de recursos depende de múltiplos fatores que mudam ao longo do tempo para cada base de dados e cada piscina elástica. Alcançar uma relação preço/desempenho ideal em piscinas densas requer monitorização e reequilíbrio contínuos, que está a mover bases de dados de piscinas mais utilizadas para piscinas menos utilizadas, e a criação de novos pools, conforme necessário para acomodar o aumento da carga de trabalho.

Não mova bases de **dados "quentes".** Se a contenção de recursos ao nível do pool for causada principalmente por um pequeno número de bases de dados altamente utilizadas, pode ser tentador mover estas bases de dados para um pool menos utilizado, ou torná-las bases de dados autónomas. No entanto, fazê-lo enquanto uma base de dados permanece altamente utilizada não é recomendada, porque a operação de movimento irá degradar ainda mais o desempenho, tanto para a base de dados que está a ser deslocada, como para todo o pool. Em vez disso, ou aguarde até que a utilização elevada diminua, ou mova bases de dados menos utilizadas em vez de aliviar a pressão dos recursos ao nível da piscina. Mas a movimentação de bases de dados com uma utilização muito baixa não beneficia, neste caso, porque não reduz materialmente a utilização de recursos ao nível da piscina.

**Crie novas bases de dados numa piscina de "quarentena".** Nos cenários em que novas bases de dados são criadas frequentemente, como aplicações que utilizam o modelo de inquilino-por-base de dados, existe o risco de uma nova base de dados colocada num conjunto elástico existente consumir inesperadamente recursos significativos e afetar outras bases de dados e processos internos na piscina. Para mitigar este risco, crie um pool separado de "quarentena" com ampla alocação de recursos. Utilize esta piscina para novas bases de dados com padrões de consumo de recursos ainda desconhecidos. Uma vez que uma base de dados tenha permanecido nesta piscina para um ciclo de negócios, como uma semana ou um mês, e o seu consumo de recursos é conhecido, pode ser transferido para uma piscina com capacidade suficiente para acomodar este uso adicional de recursos.

**Evite servidores excessivamente densos.** A Base de Dados Azure SQL [suporta](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) até 5000 bases de dados por servidor. Os clientes que usam piscinas elásticas com milhares de bases de dados podem considerar colocar várias piscinas elásticas num único servidor, com o número total de bases de dados até ao limite suportado. No entanto, servidores com muitos milhares de bases de dados criam desafios operacionais. As operações que requerem a enumeração de todas as bases de dados de um servidor, por exemplo, visualizando bases de dados no portal, serão mais lentas. Erros operacionais, tais como modificação incorreta de logins de nível de servidor ou regras de firewall, afetarão um maior número de bases de dados. A eliminação acidental do servidor necessitará de assistência do Microsoft Support para recuperar bases de dados no servidor eliminado e causará uma interrupção prolongada para todas as bases de dados afetadas.

Recomendamos limitar o número de bases de dados por servidor a um número inferior ao máximo suportado. Em muitos cenários, usar até 1000-2000 bases de dados por servidor é o ideal. Para reduzir a probabilidade de eliminação acidental do servidor, recomendamos que coloque um [bloqueio de exclusão](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) no servidor ou no seu grupo de recursos.

No passado, certos cenários que envolviam bases de dados em movimento dentro, fora ou entre piscinas elásticas no mesmo servidor eram mais rápidos do que quando moviam bases de dados entre servidores. Atualmente, todos os movimentos de base de dados executam na mesma velocidade, independentemente da fonte e do servidor de destino.

## <a name="examples"></a>Exemplos

### <a name="monitoring-memory-utilization"></a>Monitorização da utilização da memória

Esta consulta calcula a métrica para cada conjunto de `oom_per_second` recursos, ao longo dos últimos 32 minutos. Esta consulta pode ser executada em qualquer base de dados numa piscina elástica.

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>Monitorização da utilização do espaço de `tempdb` registo

Esta consulta devolve o valor atual da `tempdb_log_used_percent` métrica. Esta consulta pode ser executada em qualquer base de dados numa piscina elástica.

```sql
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>Próximos passos

- Para uma introdução a piscinas elásticas, consulte [piscinas elásticas que o ajudam a gerir e escalar várias bases](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)de dados na Base de Dados Azure SQL .
- Para obter informações sobre a sintonização das cargas de trabalho para reduzir a utilização dos recursos, consulte [monitorização e afinação]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)e [monitorização e afinação](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview)de desempenho.
