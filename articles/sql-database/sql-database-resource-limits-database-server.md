---
title: Limites de recursos da Base de Dados Azure SQL Microsoft Docs
description: Este artigo fornece uma visão geral dos limites de recursos da Base de Dados Azure SQL para bases de dados únicas e piscinas elásticas. Também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou ultrapassados.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: fa41649e002bd4845b95e787c1d0589ed1987588
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255928"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Limites de recursos da Base de Dados SQL e governação de recursos

Este artigo fornece uma visão geral dos limites de recursos da Base de Dados SQL para um servidor de base de dados SQL que gere bases de dados únicas e piscinas elásticas. Fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou ultrapassados, e descreve os mecanismos de governação dos recursos utilizados para impor estes limites.

> [!NOTE]
> Para os limites de instância gerida, consulte os limites de recursos da [Base de Dados SQL para instâncias geridas](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites máximos de recursos

| Recurso | Limite |
| :--- | :--- |
| Bases de dados por servidor | 5000 |
| Número padrão de servidores por subscrição em qualquer região | 20 |
| Número máximo de servidores por subscrição em qualquer região | 200 |  
| Quota DTU / eDTU por servidor | 54,000 |  
| quota vCore por servidor/instância | 540 |
| Piscinas max por servidor | Limitado por número de DTUs ou vCores. Por exemplo, se cada piscina for de 1000 DTUs, então um servidor pode suportar 54 piscinas.|
|||

> [!IMPORTANT]
> À medida que o número de bases de dados se aproxima do limite por servidor de base de dados SQL, podem ocorrer os seguintes:
>
> - Aumentando a latência na execução de consultas contra a base de dados principal.  Isto inclui pontos de vista sobre estatísticas de utilização de recursos, tais como sys.resource_stats.
> - Aumento da latência nas operações de gestão e na renderização de pontos de vista portais que envolvem enumerar bases de dados no servidor.

> [!NOTE]
> Para obter mais quota DTU/eDTU, quota vCore ou mais servidores do que o valor padrão, submeta um novo pedido de suporte no portal Azure. Para mais informações, consulte o pedido de aumento da quota para a Base de [Dados SQL azure](quota-increase-request.md).

### <a name="storage-size"></a>Tamanho do armazenamento

Para obter bases de dados únicas tamanhos de armazenamento de recursos, consulte [os limites de recursos baseados em DTU](sql-database-dtu-resource-limits-single-databases.md) ou [os limites de recursos baseados em vCore](sql-database-vcore-resource-limits-single-databases.md) para os limites de tamanho de armazenamento por nível de preços.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>O que acontece quando os limites de recursos da base de dados são atingidos

### <a name="compute-dtus-and-edtus--vcores"></a>Cálculo (DTUs e eDTUs / vCores)

Quando a utilização da base de dados computada (medida por DTUs e eDTUs, ou vCores) se torna elevada, a latência da consulta aumenta e as consultas podem mesmo esgotar-se. Nestas condições, as consultas podem ser submetidas à fila pelo serviço e são fornecidos recursos para a execução à medida que os recursos se tornam gratuitos.
Ao encontrar uma alta utilização de cálculo, as opções de mitigação incluem:

- Aumentar o tamanho da computação da base de dados ou do pool elástico para fornecer à base de dados mais recursos computacionais. Consulte os recursos de base de [dados individuais scale](sql-database-single-database-scale.md) e [scale elásticos recursos de piscina.](sql-database-elastic-pool-scale.md)
- Otimizar consultas para reduzir a utilização de recursos de cada consulta. Para mais informações, consulte [Afinação/Insinuação](sql-database-performance-guidance.md#query-tuning-and-hinting)de Consulta.

### <a name="storage"></a>Armazenamento

Quando o espaço de base de dados utilizado atinge o limite máximo de tamanho, a base de dados insere e atualiza ções que aumentam a falha do tamanho dos dados e os clientes recebem uma [mensagem de erro](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). As declarações SELECT e DELETE continuam a ter sucesso.

Ao encontrar uma utilização de espaço elevado, as opções de mitigação incluem:

- Aumentar o tamanho máximo da base de dados ou piscina elástica, ou adicionar mais armazenamento. Consulte os recursos de base de [dados individuais scale](sql-database-single-database-scale.md) e [scale elásticos recursos de piscina.](sql-database-elastic-pool-scale.md)
- Se a base de dados estiver num pool elástico, então, em alternativa, a base de dados pode ser movida para fora da piscina para que o seu espaço de armazenamento não seja partilhado com outras bases de dados.
- Encolher uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhadores (pedidos)

O número máximo de sessões e trabalhadores é determinado pelo nível de serviço e dimensão do cálculo (DTUs/eDTUs ou vCores. Os novos pedidos são rejeitados quando a sessão ou os limites dos trabalhadores são atingidos, e os clientes recebem uma mensagem de erro. Embora o número de ligações disponíveis possa ser controlado pela aplicação, o número de trabalhadores simultâneos é muitas vezes mais difícil de estimar e controlar. Isto é especialmente verdade durante os períodos de pico de carga quando os limites de recursos da base de dados são atingidos e os trabalhadores acumulam-se devido a consultas de funcionamento mais longas, grandes cadeias de bloqueio ou paralelismo de consulta excessiva.

Ao encontrar uma sessão alta ou utilização do trabalhador, as opções de mitigação incluem:

- Aumentar o nível de serviço ou calcular o tamanho da base de dados ou da piscina elástica. Consulte os recursos de base de [dados individuais scale](sql-database-single-database-scale.md) e [scale elásticos recursos de piscina.](sql-database-elastic-pool-scale.md)
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa do aumento da utilização dos trabalhadores for devido à contenção dos recursos computacionais. Para mais informações, consulte [Afinação/Insinuação](sql-database-performance-guidance.md#query-tuning-and-hinting)de Consulta.

## <a name="resource-governance"></a>Governação de recursos

Para impor limites de recursos, a Base de Dados Azure SQL utiliza uma implementação de governação de recursos baseada no SQL Server [Resource Governor,](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)modificado e estendido para executar um serviço de base de dados SQL Server em Azure. Em cada instância do SQL Server no serviço, existem múltiplos [conjuntos](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) de recursos e [grupos](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)de carga de trabalho, com limites de recursos definidos tanto a nível de pool como de grupo para fornecer uma [base de dados equilibrada como um serviço.](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/) A carga de trabalho dos utilizadores e as cargas de trabalho internas são classificadas em conjuntos de recursos separados e grupos de carga de trabalho. A carga de trabalho dos utilizadores nas réplicas secundárias primárias e legíveis, incluindo georéplicas, está classificada no conjunto de recursos `SloSharedPool1` e `UserPrimaryGroup.DBId[N]` grupo de carga de trabalho, onde `N` representa o valor de ID da base de dados. Além disso, existem múltiplos conjuntos de recursos e grupos de carga de trabalho para várias cargas de trabalho internas.

Além de usar o Governor de Recursos para governar recursos dentro do processo do Servidor SQL, a Base de Dados Azure SQL também utiliza [objetos](https://docs.microsoft.com/windows/win32/procthread/job-objects) de trabalho do Windows para a governação de recursos de nível de processo, e o Gestor de Recursos do Servidor de Ficheiros do Windows [(FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) para a gestão de quotas de armazenamento.

A governação de recursos da Base de Dados Azure SQL é de natureza hierárquica. De cima para baixo, os limites são aplicados ao nível do OS e ao nível do volume de armazenamento utilizando mecanismos de governação de recursos do sistema operativo e governador de recursos, em seguida, ao nível do conjunto de recursos usando o Governor de Recursos, e, em seguida, ao nível do grupo de carga de trabalho usando Governador de Recursos. Os limites de governação dos recursos em vigor para a base de dados atual ou o pool elástico são surgidos na visão [sys.dm_user_db_resource_governance.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 

### <a name="data-io-governance"></a>Governação da IO de dados

A governação da IO de dados é um processo na Base de Dados Azure SQL usado para limitar tanto a leitura como a escrita de IO físico contra ficheiros de dados de uma base de dados. Os limites do IOPS são definidos para cada nível de serviço para minimizar o efeito "vizinho barulhento", fornecer equidade na alocação de recursos no serviço multi-inquilino, e permanecer dentro das capacidades do hardware e armazenamento subjacentes.

Para bases de dados únicas, os limites do grupo de carga de trabalho são aplicados a todos os IO de armazenamento na base de dados, enquanto os limites do conjunto de recursos aplicam-se a todos os IO de armazenamento contra todas as bases de dados na mesma instância do Servidor SQL, incluindo a base de dados `tempdb`. Para piscinas elásticas, os limites do grupo de trabalho aplicam-se a cada base de dados da piscina, enquanto o limite do conjunto de recursos se aplica a todo o pool elástico, incluindo a base de dados `tempdb`, que é partilhada entre todas as bases de dados da piscina. Em geral, os limites do conjunto de recursos podem não ser alcançáveis pela carga de trabalho contra uma base de dados (única ou reunida), porque os limites do grupo de carga de trabalho são inferiores aos limites do conjunto de recursos e limitam o IOPS/produção mais cedo. No entanto, os limites de piscina podem ser atingidos pela carga de trabalho combinada contra várias bases de dados na mesma instância do Servidor SQL.

Por exemplo, se uma consulta gerar 1000 IOPS sem qualquer governação de recursos IO, mas o limite máximo de IOPS do grupo de carga é fixado para 900 IOPS, a consulta não será capaz de gerar mais de 900 IOPS. No entanto, se o limite máximo de IOPS do conjunto de recursos for fixado para 1500 IOPS, e o total de IO de todos os grupos de carga de trabalho associados ao conjunto de recursos exceder 1500 IOPS, então o IO da mesma consulta pode ser reduzido abaixo do limite de grupo de trabalho de 900 IOPS.

Os iOPS e os valores de min/max de entrada devolvidos pelo [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) vêem como limites/tampas, e não como garantias. Além disso, a governação dos recursos não garante qualquer latência específica de armazenamento. A melhor latência alcançável, iopS, e a entrada para uma determinada carga de trabalho dos utilizadores dependem não só dos limites de governação dos recursos da OI, mas também da mistura de tamanhos IO utilizados, e das capacidades do armazenamento subjacente. O SQL Server utiliza IOs que variam de tamanho entre 512 KB e 4 MB. Para efeitos de aplicação dos limites iops, cada IO é contabilizada independentemente da sua dimensão, com exceção das bases de dados com ficheiros de dados no Armazenamento Azure. Nesse caso, os IOs superiores a 256 KB são contabilizados como múltiplos 256 KB IOs, para alinhar com a contabilidade Azure Storage IO.

Para bases de dados Básicas, Standard e General Purpose, que utilizam ficheiros de dados no Armazenamento Do Azure, o valor `primary_group_max_io` pode não ser alcançável se uma base de dados não tiver ficheiros de dados suficientes para fornecer cumulativamente este número de IOPS, ou se os dados não forem distribuídos uniformemente através de ficheiros, ou se o nível de desempenho das bolhas subjacentes limitar o IOPS/ato abaixo do limite de governação dos recursos. Da mesma forma, com os pequenos IOs de log gerados por transações frequentes, o valor `primary_max_log_rate` pode não ser alcançável por uma carga de trabalho devido ao limite iopS na bolha de armazenamento Azure subjacente.

Os valores de utilização de recursos, como `avg_data_io_percent` e `avg_log_write_percent`, reportados nas [visões sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)e [sys.elastic_pool_resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) são calculados como percentagens dos limites máximos de governação dos recursos. Por conseguinte, quando os fatores que não o limite de governação dos recursos IOPS/adcto, é possível ver o IOPS/ato/achatamento e as tardios a aumentarem à medida que a carga de trabalho aumenta, embora a utilização reportada de recursos permaneça abaixo de 100%. 

Para ver ler e escrever IOPS, entrada e latência por ficheiro de base de dados, utilize a função [sys.dm_io_virtual_file_stats().](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) Esta função encontra toda a IO contra a base de dados, incluindo o fundo IO que não é contabilizado para `avg_data_io_percent`, mas utiliza IOPS e a sua entrada do armazenamento subjacente, e pode impactar a latência de armazenamento observada. A função também surge em termos de latência adicional que pode ser introduzida pela governação dos recursos da OI para leituras e escritos, nas colunas `io_stall_queued_read_ms` e `io_stall_queued_write_ms`, respectivamente.

### <a name="transaction-log-rate-governance"></a>Governação da taxa de registo de transações

A governação da taxa de registo de transações é um processo na Base de Dados Azure SQL usado para limitar altas taxas de ingestão para cargas de trabalho como inserção a granel, SELECT INTO e construções de índices. Estes limites são rastreados e aplicados ao nível do subsegundo à taxa de produção de registo, limitando a produção independentemente do número de IOs que possam ser emitidos contra ficheiros de dados.  As taxas de geração de registos de transações atualmente escalam linearmente até um ponto que é dependente de hardware, com a taxa de registo máxima permitida ser de 96 MB/s com o modelo de compra vCore. 

> [!NOTE]
> Os IOs físicos reais aos ficheiros de registo de transações não são regidos ou limitados.

As taxas de registo são definidas de modo a que possam ser alcançadas e sustentadas em vários cenários, enquanto o sistema global pode manter a sua funcionalidade com um impacto minimizado na carga do utilizador. A governação da taxa de registo garante que os backups de registo de transações permanecem dentro dos SLAs de recuperabilidade publicados.  Esta governação também impede um atraso excessivo nas réplicas secundárias.

À medida que os registos de registo são gerados, cada operação é avaliada e avaliada para saber se deve ser adiada para manter uma taxa de registo máxima desejada (MB/s por segundo). Os atrasos não são adicionados quando os registos de registo são lavados ao armazenamento, em vez de a governação da taxa de registo ser aplicada durante a própria geração de taxas de registo.

As taxas reais de produção de registo impostas no tempo de funcionamento também podem ser influenciadas por mecanismos de feedback, reduzindo temporariamente as taxas de registo admissíveis para que o sistema possa estabilizar. A gestão do espaço de ficheiros de log, evitando o esgotamento das condições de espaço de registo e os mecanismos de replicação do Grupo de Disponibilidade podem reduzir temporariamente os limites globais do sistema.

A modelação de tráfego do governador da taxa de registo é emergida através dos seguintes tipos de espera (expostos no [DMV sys.dm_db_wait_stats):](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)

| Tipo de espera | Notas |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitação da base de dados |
| POOL_LOG_RATE_GOVERNOR | Limitação da piscina |
| INSTANCE_LOG_RATE_GOVERNOR | Limitação do nível de instância |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controlo de feedback, replicação física do grupo de disponibilidade em Premium/Business Critical não se mantendo |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controlo de feedback, limitando as taxas para evitar uma condição de espaço de registo |
|||

Ao encontrar um limite de taxa de registo que esteja a dificultar a escalabilidade desejada, considere as seguintes opções:
- Aumentar até um nível de serviço mais elevado para obter a taxa máxima de 96 MB/s. 
- Se os dados que estão a ser carregados forem transitórios, como a encenação de dados num processo ETL, podem ser carregados em tempdb (que é minimamente registado). 
- Para cenários analíticos, carregue numa mesa coberta de colunas agrupada. Isto reduz a taxa de registo exigida devido à compressão. Esta técnica aumenta a utilização do CPU e só é aplicável a conjuntos de dados que beneficiam de índices de lojas de colunas agrupadas. 

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre os limites gerais do Azure, consulte [os limites de subscrição e serviço do Azure, quotas e restrições.](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Para obter informações sobre DTUs e eDTUs, consulte [DTUs e eDTUs](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Para obter informações sobre os limites de tamanho temporário, consulte [TempDB na Base de Dados Azure SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
