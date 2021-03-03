---
title: Limites de recursos para servidores lógicos em Azure
description: Este artigo fornece uma visão geral dos limites de recursos para o servidor lógico em Azure usado pela Azure SQL Database e Azure Synapse Analytics. Também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou ultrapassados.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 02/02/2021
ms.openlocfilehash: 34613633b6b27fc3387e6a9fa63caf4a194ba963
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691234"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Limites de recursos para a Azure SQL Database e para os servidores Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo fornece uma visão geral dos limites de recursos para o servidor lógico utilizado pela Azure SQL Database e Azure Synapse Analytics. Fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou ultrapassados e descreve os mecanismos de governação dos recursos utilizados para impor esses limites.

> [!NOTE]
> Para os limites de instância gerida do Azure SQL, consulte [os limites de recursos da Base de Dados SQL para instâncias geridas](../managed-instance/resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites máximos de recursos

| Recurso | Limite |
| :--- | :--- |
| Bases de dados por servidor | 5000 |
| Número predefinido de servidores por subscrição em qualquer região | 20 |
| Número máximo de servidores por subscrição em qualquer região | 200 |  
| Quota de DTU /eDTU por servidor | 54,000 |  
| vCore quota por servidor/instância | 540 |
| Piscinas max por servidor | Limitado por número de DTUs ou vCores. Por exemplo, se cada piscina for de 1000 DTUs, então um servidor pode suportar 54 piscinas.|
|||

> [!IMPORTANT]
> À medida que o número de bases de dados se aproxima do limite por servidor, pode ocorrer o seguinte:
>
> - Aumentando a latência em consultas de execução contra a base de dados principal.  Isto inclui pontos de vista de estatísticas de utilização de recursos, como sys.resource_stats.
> - Aumento da latência nas operações de gestão e dos pontos de vista do portal de renderização que envolvem enumerar bases de dados no servidor.

> [!NOTE]
> Para obter mais quota DTU/eDTU, quota vCore, ou mais servidores do que o valor padrão, envie um novo pedido de suporte no portal Azure. Para obter mais informações, consulte [os aumentos de quota de pedido para a Base de Dados Azure SQL](quota-increase-request.md).

### <a name="storage-size"></a>Tamanho do armazenamento

Para tamanhos de armazenamento de recursos de bases de dados individuais, consulte os [limites de recursos baseados em DTU](resource-limits-dtu-single-databases.md) ou [os limites de recursos baseados em vCore](resource-limits-vcore-single-databases.md) para os limites de tamanho de armazenamento por nível de preços.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>O que acontece quando os limites de recursos da base de dados são atingidos

### <a name="compute-cpu"></a>Compute CPU

Quando a utilização do CPU computacional da base de dados se torna elevada, a latência da consulta aumenta, e as consultas podem mesmo esgotar-se. Nestas condições, as consultas podem ser submetidas à fila pelo serviço e são fornecidas recursos para a execução à medida que os recursos se tornam gratuitos.
Ao encontrar uma alta utilização computacional, as opções de mitigação incluem:

- Aumentar o tamanho do cálculo da base de dados ou piscina elástica para fornecer à base de dados mais recursos compute. Consulte [os recursos de base de dados únicos escala](single-database-scale.md) e recursos de piscina elástica em [escala.](elastic-pool-scale.md)
- Otimizar consultas para reduzir a utilização de recursos cpu de cada consulta. Para obter mais informações, veja [Ajuste/Sugestões de Consultas](performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Armazenamento

Quando o espaço de base de dados utilizado atinge o limite máximo de tamanho, a base de dados insere e atualizações que aumentam a falha do tamanho dos dados e os clientes recebem uma [mensagem de erro](troubleshoot-common-errors-issues.md). As declarações SELECT e DELETE continuam a ter sucesso.

Ao encontrar uma utilização de alto espaço, as opções de mitigação incluem:

- Aumentando o tamanho máximo da base de dados ou piscina elástica, ou adicionando mais armazenamento. Consulte [os recursos de base de dados únicos escala](single-database-scale.md) e recursos de piscina elástica em [escala.](elastic-pool-scale.md)
- Se a base de dados estiver numa piscina elástica, então, em alternativa, a base de dados pode ser movida para fora da piscina para que o seu espaço de armazenamento não seja partilhado com outras bases de dados.
- Encolher uma base de dados para recuperar o espaço não used. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).
- Verifique se a utilização do espaço é devido a um pico no tamanho da Loja de Versão Persistente (PVS). O PVS faz parte de cada base de dados e é usado para implementar a [Recuperação acelerada da Base de Dados.](../accelerated-database-recovery.md) Para determinar o tamanho atual do PVS, consulte [a resolução de problemas do PVS](/sql/relational-databases/accelerated-database-recovery-management#troubleshooting). Uma razão comum para o tamanho de PVS grande é uma transação que está aberta por um longo período de tempo (horas), impedindo a limpeza de versões mais antigas em PVS.

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhadores (pedidos)

O número máximo de sessões e trabalhadores é determinado pelo nível de serviço e tamanho do cálculo (DTUs/eDTUs ou vCores). Os novos pedidos são rejeitados quando se alcançam os limites da sessão ou do trabalhador e os clientes recebem uma mensagem de erro. Embora o número de ligações disponíveis possa ser controlado pela aplicação, o número de trabalhadores simultâneos é muitas vezes mais difícil de estimar e controlar. Isto é especialmente verdade durante os períodos de carga de pico quando os limites de recursos da base de dados são atingidos e os trabalhadores se acumulam devido a consultas mais longas, grandes cadeias de bloqueio ou paralelismo de consulta excessiva.

Ao encontrar alta sessão ou utilização do trabalhador, as opções de mitigação incluem:

- Aumentar o nível de serviço ou o tamanho do cálculo da base de dados ou piscina elástica. Consulte [os recursos de base de dados únicos escala](single-database-scale.md) e recursos de piscina elástica em [escala.](elastic-pool-scale.md)
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa do aumento da utilização do trabalhador se deve à contenção dos recursos computacional. Para obter mais informações, veja [Ajuste/Sugestões de Consultas](performance-guidance.md#query-tuning-and-hinting).
- Redução da regulação [MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (grau máximo de paralelismo).
- Otimização da carga de trabalho de consulta para reduzir o número de ocorrências e duração do bloqueio de consultas. Para mais informações, consulte [Compreender e resolver problemas de bloqueio do Azure SQL](understand-resolve-blocking.md).

### <a name="memory"></a>Memória

Ao contrário de outros recursos (CPU, trabalhadores, armazenamento), atingir o limite de memória não tem um impacto negativo no desempenho da consulta, e não causa erros e falhas. Conforme descrito em detalhe no [Guia de Arquitetura de Gestão de Memória,](/sql/relational-databases/memory-management-architecture-guide)o motor de base de dados SQL Server utiliza frequentemente toda a memória disponível, por design. A memória é usada principalmente para caching dados, para evitar um acesso de armazenamento mais caro. Assim, uma utilização mais elevada da memória geralmente melhora o desempenho da consulta devido a leituras mais rápidas da memória, em vez de leituras mais lentas do armazenamento.

Após o arranque do motor da base de dados, à medida que a carga de trabalho começa a ler dados do armazenamento, o motor da base de dados caches agressivamente dados na memória. Após este período inicial de arranque, é comum e espera-se que as `avg_memory_usage_percent` `avg_instance_memory_percent` colunas e colunas em [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) sejam próximas ou iguais a 100%, especialmente para bases de dados que não estão inativas, e não se encaixam totalmente na memória.

Além da cache de dados, a memória é usada noutros componentes do motor da base de dados. Quando há procura de memória e toda a memória disponível foi utilizada pela cache de dados, o motor da base de dados reduzirá dinamicamente o tamanho da cache de dados para disponibilizar a memória a outros componentes, e irá dinamicamente aumentar a cache de dados quando outros componentes libertarem a memória.

Em casos raros, uma carga de trabalho suficientemente exigente pode causar uma condição de memória insuficiente, levando a erros fora da memória. Isto pode acontecer em qualquer nível de utilização da memória entre 0% e 100%. Isto é mais propenso a ocorrer em tamanhos de computação mais pequenos que têm limites de memória proporcionalmente menores, e/ou com cargas de trabalho usando mais memória para processamento de consultas, como em [piscinas elásticas densas](elastic-pool-resource-management.md).

Ao encontrar erros fora da memória, as opções de mitigação incluem:
- Aumentar o nível de serviço ou o tamanho do cálculo da base de dados ou piscina elástica. Consulte [os recursos de base de dados únicos escala](single-database-scale.md) e recursos de piscina elástica em [escala.](elastic-pool-scale.md)
- Otimização de consultas e configuração para reduzir a utilização da memória. As soluções comuns são descritas no quadro seguinte.

|Solução|Descrição|
| :----- | :----- |
|Reduzir o tamanho das bolsas de memória|Para obter mais informações sobre subsídios de memória, consulte o post de blog [de concessão de memória Understanding SQL Server.](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595) Uma solução comum para evitar subsídios de memória excessivamente grandes é manter [as estatísticas](/sql/relational-databases/statistics/statistics) atualizadas. Isto resulta em estimativas mais precisas do consumo de memória pelo motor de consulta, evitando subvenções de memória desnecessariamente grandes.</br></br>Em bases de dados utilizando o nível de compatibilidade 140 e posteriormente, o motor da base de dados pode ajustar automaticamente o tamanho do subsídio de memória utilizando [o feedback do subsídio de memória do modo Lote](/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback). Em bases de dados utilizando o nível de compatibilidade 150 e posteriormente, o motor da base de dados utiliza igualmente [o feedback do subsídio de memória do modo Row](/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback), para consultas de modo de linha mais comuns. Esta funcionalidade incorporada ajuda a evitar erros fora da memória devido a grandes subsídios de memória desnecessariamente grandes.|
|Reduzir o tamanho da cache do plano de consulta|O motor de base de dados caches planos de consulta na memória, para evitar compilar um plano de consulta para cada execução de consulta. Para evitar o inchaço do cache do plano de consulta causado por planos de cache que só são utilizados uma vez, ative a configuração OTIMIZE_FOR_AD_HOC_WORKLOADS [com âmbito de base de dados](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).|
|Reduzir o tamanho da memória de bloqueio|O motor da base de dados utiliza memória para [fechaduras.](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine) Quando possível, evite grandes transações que possam adquirir um grande número de fechaduras e causar um elevado consumo de memória de bloqueio.|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Consumo de recursos por cargas de trabalho do utilizador e processos internos

O consumo de CPU e de memória por cargas de trabalho dos utilizadores em cada base de dados é relatado nas vistas [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats,](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dentro `avg_cpu_percent` e `avg_memory_usage_percent` colunas. Para piscinas elásticas, o consumo de recursos ao nível da piscina é relatado na vista [sys.elastic_pool_resource_stats.](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) O consumo de CPU de carga de trabalho do utilizador também é reportado através da `cpu_percent` métrica Azure Monitor, para [bases de dados únicas](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) e [piscinas elásticas](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) ao nível da piscina.

A Azure SQL Database requer recursos de cálculo para implementar funcionalidades de serviço principal, tais como alta disponibilidade e recuperação de desastres, backup de bases de dados e restauro, monitorização, Loja de Consultas, Sintonização Automática, etc. O sistema reserva uma certa parte limitada dos recursos globais para estes processos internos utilizando mecanismos de governação de [recursos,](#resource-governance) disponibilizando o restante dos recursos para as cargas de trabalho dos utilizadores. Nas alturas em que os processos internos não estão a utilizar recursos computacional, o sistema coloca-os à disposição das cargas de trabalho dos utilizadores.

O consumo total de CPU e memória por cargas de trabalho do utilizador e processos internos é relatado nas [vistas sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats,](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dentro `avg_instance_cpu_percent` e `avg_instance_memory_percent` colunas. Estes dados também são reportados através das `sqlserver_process_core_percent` `sqlserver_process_memory_percent` métricas e Azure Monitor, para [bases de dados individuais](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) e [piscinas elásticas](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) ao nível da piscina.

Uma desagregação mais detalhada do consumo recente de recursos por parte das cargas de trabalho dos utilizadores e dos processos internos é reportada nas [opiniões sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) e [sys.dm_resource_governor_workload_groups_history_ex.](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) Para obter detalhes sobre os conjuntos de recursos e grupos de carga de trabalho referenciados nestes pontos de vista, consulte [a governação dos recursos.](#resource-governance) Estas opiniões reportam sobre a utilização de recursos por cargas de trabalho dos utilizadores e processos internos específicos nos grupos de recursos associados e grupos de carga de trabalho.

No contexto da monitorização do desempenho e da resolução de problemas, é importante considerar tanto o consumo de CPU dos **utilizadores** como o consumo total de `avg_cpu_percent` `cpu_percent` **CPU** por cargas de trabalho dos utilizadores e processos internos `avg_instance_cpu_percent` (, `sqlserver_process_core_percent` ).

**O consumo de CPU do utilizador** é calculado em percentagem dos limites de carga de trabalho do utilizador em cada objetivo de serviço. **A utilização** do CPU do utilizador a 100% indica que a carga de trabalho do utilizador atingiu o limite do objetivo de serviço. No entanto, quando o consumo total de **CPU** atinge a gama de 70-100%, é possível ver a carga de trabalho dos utilizadores a abrandar e a consulta a aumentar, mesmo que o consumo de **CPU** de utilizador reportado se mantenha significativamente abaixo dos 100%. Isto é mais provável quando se utilizam objetivos de serviço mais pequenos com uma alocação moderada de recursos computacional, mas cargas de trabalho relativamente intensas, como em [piscinas elásticas densas.](elastic-pool-resource-management.md) Isto também pode ocorrer com objetivos de serviço mais pequenos quando os processos internos requerem temporariamente recursos adicionais, por exemplo, ao criar uma nova réplica da base de dados.

Quando **o consumo total de CPU** é elevado, as opções de mitigação são as mesmas que as notadas anteriormente e incluem aumento objetivo de serviço e/ou otimização da carga de trabalho do utilizador.

## <a name="resource-governance"></a>Gestão de recursos

Para impor limites de recursos, a Azure SQL Database utiliza uma implementação de governação de recursos baseada no [Governador de Recursos do](/sql/relational-databases/resource-governor/resource-governor)Servidor SQL, modificado e estendido para funcionar na Base de Dados Azure SQL. Na Base de [Dados](/sql/relational-databases/resource-governor/resource-governor-resource-pool) SQL, múltiplos conjuntos de recursos e [grupos de carga de trabalho,](/sql/relational-databases/resource-governor/resource-governor-workload-group)com limites de recursos definidos tanto a nível de piscina como de grupo, proporcionam uma [Base de Dados equilibrada como serviço.](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/) A carga de trabalho do utilizador e as cargas de trabalho internas são classificadas em conjuntos de recursos separados e grupos de carga de trabalho. A carga de trabalho do utilizador nas réplicas secundárias primárias e legíveis, incluindo geo-réplicas, é classificada no `SloSharedPool1` grupo de recursos e grupo de carga de `UserPrimaryGroup.DBId[N]` trabalho, onde se encontra o valor `N` de ID da base de dados. Além disso, existem múltiplos pools de recursos e grupos de carga de trabalho para várias cargas de trabalho internas.

Além de utilizar o Governador de Recursos para governar recursos dentro do processo SQL, a Azure SQL Database também utiliza o Windows [Job Objects](/windows/win32/procthread/job-objects) para a governação de recursos de nível de processo, e o Gestor de Recursos do Servidor de Ficheiros do Windows [(FSRM)](/windows-server/storage/fsrm/fsrm-overview) para a gestão de quotas de armazenamento.

A governação dos recursos da Base de Dados Azure SQL é de natureza hierárquica. De cima para baixo, os limites são aplicados ao nível do SO e ao nível do volume de armazenamento utilizando mecanismos de governação de recursos do sistema operativo e governador de recursos, em seguida, ao nível do pool de recursos usando o Governador de Recursos, e, em seguida, ao nível do grupo de carga de trabalho usando o Governador de Recursos. Os limites de governação dos recursos em vigor para a base de dados atual ou piscina elástica são surgidos na visão [sys.dm_user_db_resource_governance.](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)

### <a name="data-io-governance"></a>Governação do IO de Dados

A governação do IO de Dados é um processo na Base de Dados Azure SQL usado para limitar a leitura e a escrita de IO físicos contra ficheiros de dados de uma base de dados. Os limites do IOPS são definidos para cada nível de serviço para minimizar o efeito "vizinho barulhento", para proporcionar equidade na atribuição de recursos no serviço multi-inquilino, e para permanecer dentro das capacidades do hardware e armazenamento subjacentes.

Para bases de dados individuais, os limites do grupo de carga de trabalho são aplicados a todas as IO de armazenamento contra a base de dados, enquanto os limites do conjunto de recursos se aplicam a todas as bases de dados de armazenamento no mesmo pool de SQL dedicado, incluindo a base de `tempdb` dados. Para piscinas elásticas, os limites do grupo de carga de trabalho aplicam-se a cada base de dados da piscina, enquanto o limite de reserva de recursos se aplica a toda a piscina elástica, incluindo a `tempdb` base de dados, que é partilhada entre todas as bases de dados da piscina. Em geral, os limites do conjunto de recursos podem não ser alcançáveis pela carga de trabalho contra uma base de dados (única ou agrupada), uma vez que os limites do grupo de carga de trabalho são inferiores aos limites do conjunto de recursos e limitam o IOPS/produção mais cedo. No entanto, os limites de piscina podem ser alcançados pela carga de trabalho combinada contra várias bases de dados na mesma piscina.

Por exemplo, se uma consulta gerar 1000 IOPS sem qualquer governação de recursos IO, mas o limite máximo de IOPS do grupo de carga de trabalho está definido para 900 IOPS, a consulta não será capaz de gerar mais de 900 IOPS. No entanto, se o limite máximo de IOPS do conjunto de recursos for fixado em 1500 IOPS, e o IO total de todos os grupos de trabalho associados ao conjunto de recursos exceder 1500 IOPS, então o IO da mesma consulta pode ser reduzido abaixo do limite de grupo de trabalho de 900 IOPS.

Os valores IOPS e de produção min/max devolvidos pela [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) visão funcionam como limites/tampas, e não como garantias. Além disso, a governação dos recursos não garante qualquer latência específica de armazenamento. A melhor latência alcançável, IOPS, e produção para uma determinada carga de trabalho do utilizador dependem não só dos limites de governação dos recursos IO, mas também da mistura de tamanhos de IO utilizados, e das capacidades do armazenamento subjacente. A BASE de Dados SQL utiliza iOs que variam de tamanho entre 512 KB e 4 MB. Para efeitos de aplicação dos limites do IOPS, todas as IO são contabilizadas independentemente do seu tamanho, com exceção das bases de dados com ficheiros de dados no Azure Storage. Nesse caso, os IOs maiores do que 256 KB são contabilizados como múltiplos IOs de 256 KB, para alinhar com a contabilidade IO de armazenamento Azure.

Para bases de dados Básicas, Standard e Finalidade Geral, que utilizam ficheiros de dados no Azure Storage, o `primary_group_max_io` valor pode não ser alcançável se uma base de dados não tiver ficheiros de dados suficientes para fornecer cumulativamente este número de IOPS, ou se os dados não forem distribuídos uniformemente por ficheiros, ou se o nível de desempenho das bolhas subjacentes limitar o IOPS/produção abaixo dos limites de governação dos recursos. Da mesma forma, com pequenos IOs de registo gerados por transações frequentes, o `primary_max_log_rate` valor pode não ser alcançável por uma carga de trabalho devido ao limite de IOPS na bolha de armazenamento Azure subjacente. Para bases de dados que utilizem o Azure Premium Storage, a Azure SQL Database utiliza bolhas de armazenamento suficientemente grandes para obter iops/produção necessárias, independentemente do tamanho da base de dados. Para bases de dados maiores, são criados vários ficheiros de dados para aumentar a capacidade total de IOPS/produção.

Os valores de utilização dos recursos, tais `avg_data_io_percent` como, `avg_log_write_percent` relatados no [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database),  [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database), e [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) pontos de vista, são calculados em percentagens dos limites máximos de governação dos recursos. Assim, quando fatores que não o limite de governação de recursos IOPS/produção, é possível ver o IOPS/throughputando aplanar e as latências aumentarem à medida que a carga de trabalho aumenta, embora a utilização de recursos reportada se mantenha abaixo dos 100%.

Para ver ler e escrever IOPS, produção e latência por ficheiro de base de dados, utilize a função [sys.dm_io_virtual_file_stats().](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) Esta função surge em toda a IO contra a base de dados, incluindo iO de fundo que não é contabilizado para `avg_data_io_percent` , mas usa IOPS e produção do armazenamento subjacente, e pode impactar a latência de armazenamento observada. A função apresenta latência adicional que pode ser introduzida pela governação de recursos da OI para leituras e escritas, `io_stall_queued_read_ms` `io_stall_queued_write_ms` respectivamente.

### <a name="transaction-log-rate-governance"></a>Governação da taxa de registo de transações

A governação da taxa de registo de transações é um processo na Base de Dados Azure SQL usada para limitar altas taxas de ingestão para cargas de trabalho como inserção a granel, SELECT INTO e edifícios de índices. Estes limites são rastreados e aplicados ao nível do subsegundo até à taxa de geração de registo de registo, limitando a produção independentemente de quantos IOs podem ser emitidos contra ficheiros de dados.  As taxas de geração de registo de transações atualmente escalam linearmente até um ponto que é dependente de hardware, com a taxa máxima de registo permitida a ser de 96 MB/s com o modelo de compra vCore.

> [!NOTE]
> Os IOs físicos reais aos ficheiros de registo de transações não são regidos ou limitados.

As taxas de registo são definidas de forma a que possam ser alcançadas e sustentadas em diversos cenários, enquanto o sistema global pode manter a sua funcionalidade com um impacto minimizado na carga do utilizador. A governação da taxa de registo garante que as cópias de segurança do registo de transações permaneçam dentro das SLAs de recuperação publicadas.  Esta governação também impede um atraso excessivo nas réplicas secundárias.

À medida que os registos de registo são gerados, cada operação é avaliada e avaliada para saber se deve ser retardada, a fim de manter uma taxa máxima de registo desejada (MB/s por segundo). Os atrasos não são adicionados quando os registos de registo são lavados para armazenamento, em vez disso, a governação da taxa de registo é aplicada durante a própria geração de taxa de registo.

As taxas reais de geração de log impostas no tempo de execução também podem ser influenciadas por mecanismos de feedback, reduzindo temporariamente as taxas de registo admissíveis para que o sistema possa estabilizar. Registar a gestão do espaço do ficheiro, evitando o escorramento das condições de espaço de registo e os mecanismos de replicação do Grupo Availability podem diminuir temporariamente os limites globais do sistema.

A formação do tráfego do governador da taxa de registo é emergida através dos seguintes tipos de espera (expostos nas [vistas sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_os_wait_stats):](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)

| Tipo de Espera | Notas |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitação da base de dados |
| POOL_LOG_RATE_GOVERNOR | Limitação da piscina |
| INSTANCE_LOG_RATE_GOVERNOR | Limitação do nível de instância |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controlo de feedback, replicação física do grupo de disponibilidade em Premium/Business Critical não acompanhar |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controlo de feedback, limitando as taxas para evitar uma condição fora do espaço de log |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Controlo de feedback de geoprolicância, limitando a taxa de registo para evitar elevada latência de dados e indisponibilidade de geoss secundários|
|||

Ao encontrar um limite de taxa de registo que esteja a dificultar a escalabilidade desejada, considere as seguintes opções:

- Dimensione até um nível de serviço mais elevado para obter a taxa máxima de registo de 96 MB/s, ou mudar para um nível de serviço diferente. O nível de serviço [Hyperscale](service-tier-hyperscale.md) fornece uma taxa de registo de 100 MB/s, independentemente do nível de serviço escolhido.
- Se os dados que estão a ser carregados forem transitórios, como os dados de paragem num processo ETL, podem ser carregados em temperatura (que é minimamente registado).
- Para cenários analíticos, carregue numa mesa coberta de colunas agrupadas. Isto reduz a taxa de registo necessária devido à compressão. Esta técnica aumenta a utilização do CPU e só é aplicável a conjuntos de dados que beneficiam de índices de lojas de colunas agrupados.

### <a name="storage-space-governance"></a>Governação do espaço de armazenamento

Nos níveis de serviço Premium e Business Critical, os ficheiros de registo de dados e transações são armazenados no volume SSD local da máquina que alberga a base de dados ou piscina elástica. Isto fornece iops elevado e produção, e baixa latência IO. O tamanho deste volume local depende das capacidades de hardware, e é finito. Numa determinada máquina, o espaço de volume local é consumido pelas bases de dados `tempdb` dos clientes, incluindo, o sistema operativo, o software de gestão, os dados de monitorização, registos, etc. À medida que as bases de dados são criadas, eliminadas e aumentam/diminuem o seu uso do espaço, o consumo de espaço local numa máquina flutua ao longo do tempo. 

Se o sistema detetar que o espaço livre disponível numa máquina é baixo e uma base de dados ou piscina elástica está em risco de ficar sem espaço, deslocará a base de dados ou piscina elástica para uma máquina diferente com espaço livre suficiente, permitindo o crescimento até aos limites máximos de tamanho do objetivo de serviço configurado. Este movimento ocorre de forma online, à semelhança de uma operação de escala de base de dados, e tem um [impacto](single-database-scale.md#impact)semelhante , incluindo uma falha curta (segundos) no final da operação. Esta falha termina as ligações abertas e reverte as transações, com impacto potencial nas aplicações que utilizam a base de dados nessa altura.

Como os dados são fisicamente copiados para uma máquina diferente, mover bases de dados maiores pode requerer uma quantidade substancial de tempo. Durante esse tempo, se o consumo de espaço local por uma grande base de dados de utilizadores ou piscina elástica, ou pela `tempdb` base de dados crescer muito rapidamente, o risco de ficar sem espaço aumenta. O sistema inicia o movimento da base de dados de forma equilibrada para evitar erros fora do espaço e evitar falhas desnecessárias.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre os limites gerais do Azure, consulte [os limites de subscrição e serviços, quotas e restrições da Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)
- Para obter informações sobre DTUs e eDTUs, consulte [DTUs e eDTUs](purchasing-models.md#dtu-based-purchasing-model).
- Para obter informações sobre os limites de tamanho temporário, consulte [o TempDB na Base de Dados Azure SQL](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).