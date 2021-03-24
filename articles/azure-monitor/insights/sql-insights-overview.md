---
title: Monitorize as suas implementações SQL com insights SQL (pré-visualização)
description: Visão geral dos insights do SQL no Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: b5add466a60bc855e08917d02fecaf60a35deeb1
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889574"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Monitorize as suas implementações SQL com insights SQL (pré-visualização)
Os insights SQL monitorizam o desempenho e a saúde das suas implementações SQL.  Pode ajudar a fornecer desempenho previsível e disponibilidade de cargas de trabalho vitais que você construiu em torno de um backend SQL, identificando estrangulamentos de desempenho e problemas. A SQL insights armazena os seus dados em [Registos monitores Azure,](../logs/data-platform-logs.md)o que lhe permite fornecer agregação e filtragem poderosas e analisar as tendências de dados ao longo do tempo. Você pode ver estes dados do Azure Monitor nas vistas que enviamos como parte desta oferta e você pode mergulhar diretamente nos dados do Log para executar consultas e analisar tendências.

As informações SQL não instalam nada nas suas implementações SQL IaaS. Em vez disso, utiliza máquinas virtuais de monitorização dedicadas para recolher remotamente dados tanto para as implementações do SQL PaaS como para o SQL IaaS.  O perfil de monitorização de insights SQL permite-lhe gerir os conjuntos de dados a serem recolhidos com base no tipo de SQL, incluindo Azure SQL DB, Azure SQL Managed Instance e sql servidor em execução numa máquina virtual Azure.

## <a name="pricing"></a>Preços

Não há custos diretos para insights SQL, mas você é cobrado pela sua atividade no espaço de trabalho Log Analytics. Com base nos preços publicados na página de preços do [Azure Monitor,](https://azure.microsoft.com/pricing/details/monitor/)os insights SQL são faturados para:

- Dados ingeridos de agentes e armazenados no espaço de trabalho.
- Regras de alerta baseadas em dados de registo.
- Notificações enviadas de regras de alerta.

O tamanho do registo varia em função dos comprimentos de cadeia dos dados recolhidos, e pode aumentar com a quantidade de atividade da base de dados. 

## <a name="supported-versions"></a>Versões suportadas 
Os insights SQL suportam as seguintes versões do SQL Server:

- SQL Server 2012 e mais recente

Os insights SQL suportam o SQL Server em execução nos seguintes ambientes:

- Base de Dados SQL do Azure
- Instância Gerida do Azure SQL
- Azure SQL VMs
- VMs do Azure

Os insights SQL não têm suporte ou suporte limitado para os seguintes:

- O SQL Server que funciona em máquinas virtuais fora do Azure não está atualmente suportado.
- Piscinas elásticas Azure SQL Database: Suporte limitado durante a pré-visualização do público. Será totalmente apoiado na disponibilidade geral.  
- Implementações sem servidor Azure SQL: Tal como o Ative Geo-DR, os agentes de monitorização atuais impedirão a implementação sem servidor de adormecer. Isto pode causar custos mais elevados do que o esperado de implementações sem servidor.  
- Secundários legíveis: Atualmente, apenas serão suportados os tipos de implantação com um único ponto final secundário legível (Business Critical ou Hyperscale). Quando as implementações de Hiperescala suportam réplicas nomeadas, seremos capazes de suportar vários pontos finais secundários legíveis para uma única base de dados lógica.  
- Azure Ative Directies: Atualmente apenas apoiamos Logins SQL para o Agente de Monitorização. Planeamos apoiar o Azure Ative Directies numa próxima versão e não temos suporte atual para a autenticação SQL VM utilizando Diretórios Ativos num controlador de domínio personalizado.  


## <a name="open-sql-insights"></a>Introspeções SQL abertas
Introdem insights DE SQL selecionando **SQL (pré-visualização)** a partir da secção **Insights** do menu **Azure Monitor** no portal Azure. Clique num azulejo para carregar a experiência para o tipo de SQL que está a monitorizar.

:::image type="content" source="media/sql-insights/portal.png" alt-text="Insights SQL no portal Azure.":::


## <a name="enable-sql-insights"></a>Permitir insights SQL 
Consulte [os insights do SQL](sql-insights-enable.md) para o procedimento detalhado para permitir insights SQL, além de passos para a resolução de problemas.


## <a name="data-collected-by-sql-insights"></a>Dados recolhidos por insights SQL

Os insights SQL apenas suportam o método remoto de monitorização do SQL. Não instalamos quaisquer agentes nos VMs que estejam a executar o SQL Server. São necessários um ou mais VMs de monitorização dedicados que utilizamos para recolher remotamente dados dos seus recursos SQL. 

Cada um destes VMs de monitorização terá o [agente Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) instalado neles juntamente com a extensão de insights de carga de trabalho (WLI). 

A extensão WLI inclui o [agente telegraf de](https://www.influxdata.com/time-series-platform/telegraf/)código aberto .  Utilizamos regras de [recolha de dados](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) para configurar o plugin de entrada [sqlserver](https://www.influxdata.com/integration/microsoft-sql-server/) para especificar os dados a recolher a partir de Azure SQL DB, Azure SQL Managed Instance e SQL Server em execução num Azure VM. 

As seguintes tabelas resumem as seguintes:

- Nome da consulta no plugin telegrafa sqlserver
- Vistas dinâmicas geridas as chamadas de consulta
- Espaço de nome os dados aparecem na tabela *InsighstMetrics*
- Se os dados são recolhidos por padrão
- Quantas vezes os dados são recolhidos por padrão
 
Pode modificar quais as consultas executadas e a frequência de recolha de dados quando criar o seu perfil de monitorização. 

### <a name="azure-sql-db-data"></a>Dados do Azure SQL DB 

| Nome de consulta | DMV | Espaço de Nomes | Ativado por Predefinição | Frequência de recolha padrão |
|:---|:---|:---|:---|:---|
| AzureSQLDBWaitStats |  sys.dm_db_wait_stats | sqlserver_azuredb_waitstats | No | ND |
| AzureSQLDBResourceStats | sys.dm_db_resource_stats | sqlserver_azure_db_resource_stats | Sim | 60 segundos |
| AzuresQLDBResourceGovernance | sys.dm_user_db_resource_governance | sqlserver_db_resource_governance | Sim | 60 segundos |
| AzuresQLDBDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys.database_files | sqlserver_database_io | Sim | 60 segundos |
| AzuresQLDBServerProperties | sys.dm_os_job_object<br>sys.database_files<br>sys. [bases de dados]<br>sys. [database_service_objetives] | sqlserver_server_properties | Sim | 60 segundos |
| AzureSQLDBOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Sim | 60 segundos |
| AzureSQLDBMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Sim | 60 segundos |
| AzuresQLDBPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Sim | 60 segundos |
| AzureSQLDBRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | ND |
| AzuresQLDBSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | ND  |

### <a name="azure-sql-managed-instance-data"></a>Azure SQL geriu dados de instâncias 

| Nome de consulta | DMV | Espaço de Nomes | Ativado por Predefinição | Frequência de recolha padrão |
|:---|:---|:---|:---|:---|
| AzureSQLMIResourceStats | sys.server_resource_stats | sqlserver_azure_db_resource_stats | Sim | 60 segundos |
| AzuresQLMIResourceGovernance | sys.dm_instance_resource_governance | sqlserver_instance_resource_governance | Sim | 60 segundos |
| AzuresQLMIDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Sim | 60 segundos |
| AzuresQLMIServerProperties | sys.server_resource_stats | sqlserver_server_properties | Sim | 60 segundos |
| AzureSQLMIOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Sim | 60 segundos |
| AzureSQLMIMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Sim | 60 segundos |
| AzuresQLMIPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Sim | 60 segundos |
| AzureSQLMIRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | ND |
| AzuresQLMISchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | ND |

### <a name="sql-server-data"></a>Dados do SqL Server

| Nome de consulta | DMV | Espaço de Nomes | Ativado por Predefinição | Frequência de recolha padrão |
|:---|:---|:---|:---|:---|
| SQLServerPerformanceCounters | sys.dm_os_performance_counters | sqlserver_performance | Sim | 60 segundos |
| SQLServerWaitStatsCategorized | sys.dm_os_wait_stats | sqlserver_waitstats | Sim | 60 segundos | 
| SQLServerDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Sim | 60 segundos |
| SQLServerProperties | sys.dm_os_sys_info | sqlserver_server_properties | Sim | 60 segundos |
| SQLServerMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Sim | 60 segundos |
| SQLServerSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | ND |
| SQLServerRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | ND |
| SQLServerVolumeSpace | sys.master_files | sqlserver_volume_space | Sim | 60 segundos |
| SQLServerCpu | sys.dm_os_ring_buffers | sqlserver_cpu | Sim | 60 segundos |
| SQLServerAvailabilityReplicaStates | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | sqlserver_hadr_replica_states | | 60 segundos |
| SQLServerDatabaseReplicaStates | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | sqlserver_hadr_dbreplica_states | | 60 segundos |




## <a name="next-steps"></a>Passos seguintes

Consulte [os insights DO SQL](sql-insights-enable.md) para o procedimento detalhado para permitir insights SQL.
Consulte [perguntas frequentes](../faq.md#sql-insights-preview) para perguntas frequentes sobre insights SQL.
