---
title: Monitorize as suas implementações SQL com insights SQL (pré-visualização)
description: Visão geral dos insights do SQL no Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d0bb5c55d3f7ba0573dfe9b511f4d31dcc64ed85
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567836"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Monitorize as suas implementações SQL com insights SQL (pré-visualização)
A SQL insights é uma solução abrangente para monitorizar qualquer produto da [família Azure SQL.](../../azure-sql/index.yml) Os insights SQL utilizam [pontos de vista dinâmicos de gestão](../../azure-sql/database/monitoring-with-dmvs.md) para expor os dados necessários para monitorizar a saúde, diagnosticar problemas e afinar o desempenho.  

As informações SQL executam toda a monitorização remotamente. Os agentes de monitorização em máquinas virtuais dedicadas ligam-se aos seus recursos SQL e recolhem remotamente dados. Os dados recolhidos são armazenados em [Registos monitores Azure,](../logs/data-platform-logs.md)permitindo fácil agregação, filtragem e análise de tendências. Pode ver os dados recolhidos a partir do modelo de [livro](../visualize/workbooks-overview.md)de insights SQL, ou pode aprofundar-se diretamente nos dados utilizando [consultas de registo](../logs/get-started-queries.md).

## <a name="pricing"></a>Preços
Não há custo direto para insights SQL. Todos os custos são incorridos pelas máquinas virtuais que recolhem os dados, os espaços de trabalho do Log Analytics que armazenam os dados, e quaisquer regras de alerta configuradas nos dados. 

**Máquinas virtuais**

Para máquinas virtuais, é cobrado com base nos preços publicados na página de preços das [máquinas virtuais.](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/) O número de máquinas virtuais necessárias variará em função do número de cadeias de ligação que pretende monitorizar. Recomendamos alocar 1 máquina virtual de tamanho Standard_B2s para cada 100 cordas de ligação. Consulte [os requisitos da máquina virtual Azure](sql-insights-enable.md#azure-virtual-machine-requirements) para obter mais detalhes.

**Áreas de trabalho do Log Analytics**

Para os espaços de trabalho do Log Analytics, é cobrado com base nos preços publicados na página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Os espaços de trabalho log Analytics utilizados por insights SQL incorrerão em custos para a ingestão de dados, retenção de dados e (opcionalmente) exportação de dados. Os encargos exatos variarão em função da quantidade de dados ingeridos, retidos e exportados. A quantidade destes dados variará posteriormente com base na sua atividade de base de dados e nas definições de recolha definidas nos seus [perfis de monitorização.](sql-insights-enable.md#create-sql-monitoring-profile)

**Regras de alertas**

Para regras de alerta no Azure Monitor, é cobrado com base nos preços publicados na página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Se optar por [criar alertas com insights SQL,](sql-insights-alerts.md)é-lhe cobrado qualquer regra de alerta criada e quaisquer notificações enviadas.

## <a name="supported-versions"></a>Versões suportadas 
Os insights SQL suportam as seguintes versões do SQL Server:
- SQL Server 2012 e mais recente

Os insights SQL suportam o SQL Server em execução nos seguintes ambientes:
- Base de Dados SQL do Azure
- Instância Gerida do Azure SQL
- SQL Server em Azure Virtual Machines (SQL Server em execução em máquinas virtuais registadas com o fornecedor [de máquinas virtuais SQL)](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)
- Azure VMs (SQL Server em execução em máquinas virtuais não registadas com o fornecedor [de máquinas virtuais SQL)](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)

Os insights SQL não têm suporte ou suporte limitado para os seguintes:
- **Casos não-Azure**: O Servidor SQL que funciona em máquinas virtuais fora do Azure não são suportados
- **Piscinas elásticas Azure SQL Database**: As métricas não podem ser recolhidas para piscinas elásticas. As métricas não podem ser recolhidas para bases de dados dentro de piscinas elásticas.
- Bases de **dados Azure SQL baixos níveis de serviço**: As métricas não podem ser recolhidas para bases de dados nos [níveis básicos,](../../azure-sql/database/resource-limits-dtu-single-databases.md) S0, S1 e S2
- **Nível sem servidor Azure SQL Database**: As métricas podem ser recolhidas para bases de dados utilizando o nível de computação sem servidor. No entanto, o processo de recolha de métricas irá redefinir o temporizador de atraso de pausa automática, impedindo que a base de dados insira um estado de pausa automática
- **Réplicas secundárias**: As métricas só podem ser recolhidas para uma única réplica secundária por base de dados. Se uma base de dados tiver mais de 1 réplica secundária, apenas 1 pode ser monitorizada.
- **Autenticação com Diretório Ativo Azure**: O único método suportado de [autenticação](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) para monitorização é a autenticação SQL. Para o SQL Server em Azure VM, a autenticação utilizando o Ative Directory num controlador de domínio personalizado não é suportada.  

## <a name="open-sql-insights"></a>Introspeções SQL abertas
Introdem insights DE SQL selecionando **SQL (pré-visualização)** a partir da secção **Insights** do menu **Azure Monitor** no portal Azure. Clique num azulejo para carregar a experiência para o tipo de SQL que está a monitorizar.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Insights SQL no portal Azure.":::

## <a name="enable-sql-insights"></a>Permitir insights SQL 
Consulte [instruções de 22 posições](sql-insights-enable.md) sobre a ativação de insights SQL.

## <a name="troubleshoot-sql-insights"></a>Insights SQL de resolução de problemas 
Consulte [os insights do SQL de resolução de problemas](sql-insights-troubleshoot.md) para obter instruções sobre a resolução de insights SQL de resolução de problemas.

## <a name="data-collected-by-sql-insights"></a>Dados recolhidos por insights SQL
As informações SQL executam toda a monitorização remotamente. Não instalamos quaisquer agentes nas máquinas virtuais que executam o SQL Server. 

Os insights SQL utilizam máquinas virtuais de monitorização dedicadas para recolher remotamente dados dos seus recursos SQL. Cada máquina virtual de monitorização terá o [agente Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) e a extensão de insights de carga de trabalho (WLI). A extensão WLI inclui o [agente Telegraf](https://www.influxdata.com/time-series-platform/telegraf/)de código aberto . Os insights SQL utilizam [regras de recolha de dados](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) para especificar as definições de recolha de dados para [o plugin SQL Server](https://www.influxdata.com/integration/microsoft-sql-server/)da Telegraf .

Diferentes conjuntos de dados estão disponíveis para Azure SQL Database, Azure SQL Managed Instance e SQL Server. As tabelas abaixo descrevem os dados disponíveis. Pode personalizar quais os conjuntos de dados a recolher e a frequência de recolha quando [criar um perfil de monitorização](sql-insights-enable.md#create-sql-monitoring-profile).

As tabelas abaixo têm as seguintes colunas:
- **Nome amigável**: Nome da consulta como mostrado no portal Azure ao criar um perfil de monitorização
- **Nome de configuração**: Nome da consulta tal como mostrado no portal Azure ao editar um perfil de monitorização
- **Espaço de** nome : Nome da consulta encontrada num espaço de trabalho log analytics. Este identificador aparece na tabela **InsighstMetrics** na `Namespace` propriedade na `Tags` coluna
- **DMVs**: As vistas dinâmicas geridas utilizadas para produzir o conjunto de dados
- **Ativado por Predefinição**: Se os dados são recolhidos por padrão
- **Frequência de recolha padrão**: Com que frequência os dados são recolhidos por padrão

### <a name="data-for-azure-sql-database"></a>Dados para Azure SQL Database 
| Nome amigável | Nome de configuração | Espaço de Nomes | DMVs | Ativado por Predefinição | Frequência de recolha padrão |
|:---|:---|:---|:---|:---|:---|
| Estatísticas de espera dB | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | No | ND |
| Estatísticas de espera do DBO | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Yes | 60 segundos |
| Distribuições de memória | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 segundos |
| E/S de bases de dados | AzuresQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys.database_files | Yes | 60 segundos |
| Propriedades do servidor | AzuresQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>sys. [bases de dados]<br>sys. [database_service_objetives] | Yes | 60 segundos |
| Contadores de desempenho | AzuresQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Yes | 60 segundos |
| Estatísticas de recursos | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Yes | 60 segundos |
| Gestão de recursos | AzuresQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Yes | 60 segundos |
| Pedidos | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | ND |
| Agendadores| AzuresQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | ND  |

### <a name="data-for-azure-sql-managed-instance"></a>Dados para Azure SQL Caso Gerido 

| Nome amigável | Nome de configuração | Espaço de Nomes | DMVs | Ativado por Predefinição | Frequência de recolha padrão |
|:---|:---|:---|:---|:---|:---|
| Estatísticas de espera | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 segundos |
| Distribuições de memória | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 segundos |
| E/S de bases de dados | AzuresQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 segundos |
| Propriedades do servidor | AzuresQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Yes | 60 segundos |
| Contadores de desempenho | AzuresQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Yes | 60 segundos |
| Estatísticas de recursos | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Yes | 60 segundos |
| Gestão de recursos | AzuresQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Yes | 60 segundos |
| Pedidos | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | ND |
| Agendadores | AzuresQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | ND |

### <a name="data-for-sql-server"></a>Dados para O Servidor SQL

| Nome amigável | Nome de configuração | Espaço de Nomes | DMVs | Ativado por Predefinição | Frequência de recolha padrão |
|:---|:---|:---|:---|:---|:---|
| Estatísticas de espera | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 segundos | 
| Distribuições de memória | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 segundos |
| E/S de bases de dados | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 segundos |
| Propriedades do servidor | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Yes | 60 segundos |
| Contadores de desempenho | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Yes | 60 segundos |
| Espaço de volume | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Yes | 60 segundos |
| CPU do servidor SQL | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Yes | 60 segundos |
| Agendadores | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | ND |
| Pedidos | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | ND |
| Estados de réplica de disponibilidade | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | No | 60 segundos |
| Réplicas da base de dados de disponibilidade | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | No | 60 segundos |

## <a name="next-steps"></a>Passos seguintes

- Consulte [introduções do SQL](sql-insights-enable.md) para obter instruções sobre a ativação de insights SQL
- Consulte [perguntas frequentes](../faq.md#sql-insights-preview) para perguntas frequentes sobre insights SQL
