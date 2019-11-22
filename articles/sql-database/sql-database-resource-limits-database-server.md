---
title: Limites de recursos do banco de dados SQL do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos limites de recursos do banco de dados SQL do Azure para bancos de dados individuais e pools elásticos. Ele também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou ultrapassados.
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
ms.openlocfilehash: 40b277f0b1bfb3501bb246e555d46db5e1ee9f95
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279311"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Limites de recursos do banco de dados SQL e governança de recursos

Este artigo fornece uma visão geral dos limites de recursos do banco de dados SQL para um servidor de banco de dados SQL que gerencia bancos de dados individuais e pools elásticos. Ele fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou ultrapassados e descreve os mecanismos de governança de recursos usados para impor esses limites.

> [!NOTE]
> Para limites de Instância Gerenciada, consulte [limites de recursos do banco de dados SQL para instâncias gerenciadas](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites máximos de recursos

| Recurso | Limite |
| :--- | :--- |
| Bancos de dados por servidor | 5000 |
| Número padrão de servidores por assinatura em qualquer região | 20 |
| Número máximo de servidores por assinatura em qualquer região | 200 |  
| Cota de DTU/eDTU por servidor | 54,000 |  
| cota vCore por servidor/instância | 540 |
| Máximo de pools por servidor | Limitado pelo número de DTUs ou vCores. Por exemplo, se cada pool for 1000 DTUs, um servidor poderá dar suporte a pools de 54.|
|||

> [!NOTE]
> Para obter mais cota de DTU/eDTU, cota de vCore ou mais servidores do que o valor padrão, uma nova solicitação de suporte pode ser enviada no portal do Azure para a assinatura com o tipo de problema "cota". A cota de DTU/eDTU e o limite de banco de dados por servidor restringem o número de pools elásticos por servidor.

> [!IMPORTANT]
> À medida que o número de bancos de dados se aproxima do limite por servidor do SQL Database, pode ocorrer o seguinte:
>
> - Aumento da latência na execução de consultas no banco de dados mestre.  Isso inclui exibições de estatísticas de utilização de recursos, como sys. resource_stats.
> - Aumento da latência em operações de gerenciamento e pontos de vista do portal de renderização que envolvem a enumeração de bancos de dados no servidor.

### <a name="storage-size"></a>Tamanho do armazenamento

Para tamanhos de armazenamento de recursos de bancos de dados únicos, consulte [limites de recursos baseados em DTU](sql-database-dtu-resource-limits-single-databases.md) ou limites de [recursos baseados em vCore](sql-database-vcore-resource-limits-single-databases.md) para os limites de tamanho de armazenamento por tipo de preço.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>O que acontece quando os limites de recursos do banco de dados são atingidos

### <a name="compute-dtus-and-edtus--vcores"></a>Computação (DTUs e eDTUs/vCores)

Quando a utilização de computação de banco de dados (medida por DTUs e eDTUs ou vCores) se torna alta, a latência de consulta aumenta e as consultas podem até mesmo atingir o tempo limite. Sob essas condições, as consultas podem ser enfileiradas pelo serviço e são fornecidas recursos para execução à medida que os recursos são liberados.
Ao encontrar alta utilização de computação, as opções de mitigação incluem:

- Aumentar o tamanho de computação do banco de dados ou do pool elástico para fornecer ao banco de dados mais recursos de computação. Consulte [dimensionar recursos de banco de dados individual](sql-database-single-database-scale.md) e [dimensionar recursos de pool elástico](sql-database-elastic-pool-scale.md).
- Otimização de consultas para reduzir a utilização de recursos de cada consulta. Para obter mais informações, consulte [ajuste/dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Quando o espaço de banco de dados usado atinge o limite de tamanho máximo, as inserções e atualizações de banco de dados que aumentam a falha de tamanho e os clientes recebem uma [mensagem de erro](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). As instruções SELECT e DELETE continuam a ter sucesso.

Ao encontrar alta utilização de espaço, as opções de mitigação incluem:

- Aumentar o tamanho máximo do banco de dados ou do pool elástico ou adicionar mais armazenamento. Consulte [dimensionar recursos de banco de dados individual](sql-database-single-database-scale.md) e [dimensionar recursos de pool elástico](sql-database-elastic-pool-scale.md).
- Se o banco de dados estiver em um pool elástico, como alternativa, o banco de dados poderá ser movido para fora do pool para que seu espaço de armazenamento não seja compartilhado com outros bancos de dados.
- Reduza um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhadores (solicitações)

O número máximo de sessões e trabalhos é determinado pela camada de serviço e pelo tamanho da computação (DTUs/eDTUs ou vCores. Novas solicitações são rejeitadas quando os limites de sessão ou de trabalho são atingidos e os clientes recebem uma mensagem de erro. Embora o número de conexões disponíveis possa ser controlado pelo aplicativo, o número de trabalhos simultâneos geralmente é mais difícil de estimar e controlar. Isso é especialmente verdadeiro durante períodos de pico de carga quando os limites de recursos do banco de dados são atingidos e os trabalhadores se acumulam devido a consultas em execução mais longas, cadeias de bloqueio grandes ou paralelismo de consulta excessivo.

Ao encontrar alta utilização de sessão ou de trabalho, as opções de mitigação incluem:

- Aumentando a camada de serviço ou o tamanho de computação do banco de dados ou do pool elástico. Consulte [dimensionar recursos de banco de dados individual](sql-database-single-database-scale.md) e [dimensionar recursos de pool elástico](sql-database-elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa da maior utilização do trabalhador for devido à contenção de recursos de computação. Para obter mais informações, consulte [ajuste/dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="resource-governance"></a>Governação de recursos

Para impor limites de recursos, o banco de dados SQL do Azure usa uma implementação de governança de recursos baseada em SQL Server [resource governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), modificado e estendido para executar um serviço de banco de dados SQL Server no Azure. Em cada instância de SQL Server no serviço, há vários [pools de recursos](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) e [grupos de carga de trabalho](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group), com limites de recursos definidos no pool e nos níveis de grupo para fornecer um banco de [dados como serviço equilibrado](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). Carga de trabalho do usuário e cargas de trabalho internas são classificadas em pools de recursos e grupos de carga de trabalho separados. A carga de trabalho do usuário nas réplicas secundárias primárias e legíveis, incluindo réplicas geográficas, é classificada no pool de recursos `SloSharedPool1` e `UserPrimaryGroup.DBId[N]` grupo de carga de trabalho, em que `N` representa o valor da ID do banco de dados. Além disso, há vários pools de recursos e grupos de carga de trabalho para várias cargas de trabalho internas.

Além de usar Resource Governor para controlar os recursos dentro do processo de SQL Server, o banco de dados SQL do Azure também usa [objetos de trabalho](https://docs.microsoft.com/windows/win32/procthread/job-objects) do Windows para governança de recursos de nível de processo e o [FSRM (Gerenciador de recursos de servidor de arquivos)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) do Windows para gerenciamento de cota de armazenamento.

A governança de recursos do banco de dados SQL do Azure é hierárquica por natureza. De cima para baixo, os limites são impostos no nível do sistema operacional e no nível do volume de armazenamento usando mecanismos de governança de recursos do sistema operacional e Resource Governor, em seguida, no nível do pool de recursos usando Resource Governor e, em seguida, no nível do grupo de carga de trabalho usando Resource Governor. Os limites de governança de recursos em vigor para o banco de dados atual ou pool elástico são exibidos na exibição [Sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) . 

### <a name="data-io-governance"></a>Governança de e/s de dados

A governança de e/s de dados é um processo no banco de dados SQL do Azure usado para limitar a e/s física de leitura e gravação em arquivos de data de um banco. Os limites de IOPS são definidos para cada nível de serviço para minimizar o efeito de "vizinho ruidosa", para fornecer a integridade de alocação de recursos no serviço multilocatário e para permanecer dentro dos recursos do hardware e do armazenamento subjacentes.

Para bancos de dados individuais, os limites de grupo de carga de trabalho são aplicados a todas as e/s de armazenamento em relação ao banco de dados, enquanto os limites do pool de recursos se aplicam a todas as e/s de armazenamento em todos os bancos na mesma instância de SQL Server, incluindo `tempdb` o Para pools elásticos, os limites de grupo de carga de trabalho se aplicam a cada banco de dados no pool, enquanto o limite do pool de recursos se aplica a todo o pool elástico, incluindo o banco de dados `tempdb`, que é compartilhado entre todos os bancos de dados no pool. Em geral, os limites do pool de recursos podem não ser obtidos pela carga de trabalho em relação a um banco de dados (único ou em pool), pois os limites do grupo de carga de trabalho são menores do que os limites do pool de recursos e limitam o IOPS/taxa No entanto, os limites de pool podem ser alcançados pela carga de trabalho combinada em vários bancos de dados na mesma instância de SQL Server.

Por exemplo, se uma consulta gerar 1000 IOPS sem nenhuma governança de recursos de e/s, mas o limite máximo de IOPS do grupo de carga de trabalho for definido como 900 IOPS, a consulta não poderá gerar mais de 900 IOPS. No entanto, se o limite máximo de IOPS do pool de recursos for definido como 1500 IOPS e a e/s total de todos os grupos de carga de trabalho associados ao pool de recursos exceder 1500 IOPS, a e/s da mesma consulta poderá ser reduzida abaixo do limite do grupo de trabalhos de 900 IOPS.

Os valores mínimo/máximo de IOPS e taxa de transferência retornados pela exibição [Sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) atuam como limites/Caps, não como garantias. Além disso, a governança de recursos não garante nenhuma latência de armazenamento específica. A melhor latência, IOPS e taxa de transferência atingíveis para uma determinada carga de trabalho de usuário dependem não apenas dos limites de governança de recursos de e/s, mas também da combinação de tamanhos de e/s usados e dos recursos do armazenamento subjacente. O SQL Server usa o IOs que varia de tamanho entre 512 KB e 4 MB. Para fins de imposição de limites de IOPS, cada e/s é contabilizada independentemente de seu tamanho, com a exceção de bancos de dados com arquivos de data no armazenamento do Azure. Nesse caso, IOs com mais de 256 KB são contabilizados como vários 256 KB de IOs, para alinhar com a contabilidade de e/s do armazenamento do Azure.

Para bancos de dados Basic, Standard e Uso Geral, que usam arquivos de dado no armazenamento do Azure, o valor de `primary_group_max_io` pode não ser atingível se um banco de dados não tiver arquivos suficientes para fornecer esse número de IOPS, ou se os dados não forem distribuídos uniformemente entre arquivos ou se o nível de desempenho dos BLOBs subjacentes limitar IOPS/taxa de transferência abaixo do limite de governança de recursos Da mesma forma, com o IOs de log pequeno gerado pela confirmação de transação frequente, o valor `primary_max_log_rate` pode não ser atingível por uma carga de trabalho devido ao limite de IOPS no blob de armazenamento do Azure subjacente.

Valores de utilização de recursos, como `avg_data_io_percent` e `avg_log_write_percent`, relatados nas exibições [Sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [Sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)e [Sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) , são calculados como porcentagens de limites máximos de governança de recursos. Portanto, quando fatores diferentes da governança de recursos limitam o IOPS/taxa de transferência, é possível ver o aumento de taxa de transferência e as latências aumentadas à medida que a carga de trabalho aumenta, embora a utilização de recursos relatada permaneça abaixo de 100%. Para ver a leitura e gravação de IOPS, taxa de transferência e latência por arquivo de banco de dados, use a função [Sys. dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) . Essa função faz a superfície de todas as e/s no banco de dados, incluindo a e/s de plano de fundo que não é contabilizada em `avg_data_io_percent`, mas usa IOPS e taxa de transferência do armazenamento subjacente e pode afetar a latência de armazenamento observada.

### <a name="transaction-log-rate-governance"></a>Governança de taxa de log de transações

A governança de taxa do log de transações é um processo no banco de dados SQL do Azure usado para limitar altas taxas de ingestão para cargas de trabalho, como inserção em massa, seleção INTO e compilações de índice. Esses limites são rastreados e aplicados no nível de subsegundos à taxa de geração de registro de log, limitando a taxa de transferência, independentemente de quantos IOs podem ser emitidos em relação aos arquivos de dados.  As taxas de geração de log de transações atualmente são dimensionadas linearmente até um ponto que é dependente de hardware, com a taxa de log máxima permitida de 96 MB/s com o modelo de compra vCore. 

> [!NOTE]
> O IOs físico real para os arquivos de log de transações não são governados ou limitados.

As taxas de log são definidas de modo que elas possam ser alcançadas e mantidas em vários cenários, enquanto o sistema geral pode manter sua funcionalidade com impacto minimizado na carga do usuário. A governança de taxa de log garante que os backups de log de transações permaneçam dentro dos SLAs de recuperação publicados.  Essa governança também impede uma pendência excessiva em réplicas secundárias.

À medida que os registros de log são gerados, cada operação é avaliada e avaliada se deve ser atrasada para manter uma taxa máxima de log desejada (MB/s por segundo). Os atrasos não são adicionados quando os registros de log são liberados para armazenamento, em vez disso, a governança de taxa de log é aplicada durante a própria geração de taxa de log.

As taxas de geração de log reais impostas em tempo de execução também podem ser influenciadas por mecanismos de comentários, reduzindo temporariamente as taxas de log permitidas para que o sistema possa se estabilizar. Gerenciamento de espaço de arquivo de log, evitando a execução de condições de espaço de log e os mecanismos de replicação de grupo de disponibilidade podem diminuir temporariamente os limites gerais do sistema.

A modelagem de tráfego do administrador da taxa de log é apresentada por meio dos seguintes tipos de espera (expostos na DMV [Sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) ):

| Tipo de espera | Notas |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitação de banco de dados |
| POOL_LOG_RATE_GOVERNOR | Limitação de pool |
| INSTANCE_LOG_RATE_GOVERNOR | Limitação de nível de instância |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controle de comentários, replicação física do grupo de disponibilidade em Premium/Comercialmente Crítico não está acompanhando |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controle de comentários, limitando as taxas para evitar uma condição de espaço de log insuficiente |
|||

Ao encontrar um limite de taxa de log que está atrasando a escalabilidade desejada, considere as seguintes opções:
- Escale verticalmente para um nível de serviço superior para obter a taxa máxima de logs de 96 MB/s. 
- Se os dados que estão sendo carregados forem transitórios, como dados de preparo em um processo de ETL, eles poderão ser carregados em tempdb (que é minimamente registrado). 
- Para cenários analíticos, carregue em uma tabela coberta por columnstore clusterizado. Isso reduz a taxa de log necessária devido à compactação. Essa técnica aumenta a utilização da CPU e só é aplicável a conjuntos de dados que se beneficiam de índices columnstore clusterizados. 

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre limites gerais do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre DTUs e eDTUs, consulte [DTUs e eDTUs](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Para obter informações sobre os limites de tamanho do tempdb, consulte [tempdb no banco de dados SQL do Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
