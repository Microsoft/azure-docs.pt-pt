---
title: Perguntas frequentes de hiperescala do banco de dados SQL do Azure | Microsoft Docs
description: Respostas a perguntas comuns que os clientes perguntam sobre um banco de dados SQL do Azure na camada de serviço de hiperescala – normalmente chamado de banco de dados de hiperescala.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/06/2019
ms.openlocfilehash: 951d5bb10fbeeac090a1edb510b7214855477eac
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515349"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Perguntas frequentes sobre os bancos de dados de hiperescala do SQL do Azure

Este artigo fornece respostas para as perguntas frequentes para os clientes que consideram um banco de dados na camada de serviço de hiperescala do banco de dados SQL do Azure, normalmente chamada de banco de dados de hiperescala. Este artigo descreve os cenários para os quais o hiperscale dá suporte e os serviços de recurso cruzado são compatíveis com a hiperescala do banco de dados SQL em geral.

- Essas perguntas frequentes são destinadas a leitores que têm uma compreensão breve da camada de serviço de hiperescala e estão procurando dúvidas e preocupações específicas.
- Essas perguntas frequentes não devem ser uma Guidebook ou responder a perguntas sobre como usar um banco de dados de hiperescala do banco de dados SQL. Para isso, recomendamos que você consulte a documentação de hiperescala do [banco de dados SQL do Azure](sql-database-service-tier-hyperscale.md) .

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-a-hyperscale-database"></a>O que é um banco de dados de hiperescala

Um banco de dados de hiperescala é um banco de dados SQL do Azure na camada de serviço de hiperescala que é apoiada pela tecnologia de armazenamento de escala horizontal de hiperescala. Um banco de dados de hiperescala dá suporte a até 100 TB de data e fornece alta taxa de transferência e desempenho, bem como o dimensionamento rápido para se adaptar aos requisitos de carga de trabalho. O dimensionamento é transparente para o aplicativo – conectividade, processamento de consulta e assim por diante, funciona como qualquer outro banco de dados SQL.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Quais tipos de recursos e modelos de compra dão suporte a hiperescala

A camada de serviço de hiperescala está disponível somente para bancos de dados individuais usando o modelo de compra baseado em vCore no banco de dados SQL do Azure.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Como a camada de serviço de hiperescala difere das camadas de serviço Uso Geral e Comercialmente Crítico

As camadas de serviço baseadas em vCore são basicamente diferenciadas com base na disponibilidade, no tipo de armazenamento e no IOPs.

- A camada de serviço Uso Geral é apropriada para a maioria das cargas de trabalho de negócios, oferecendo um conjunto equilibrado de opções de computação e armazenamento em que os tempos de failover ou de latência de e/s não são a prioridade.
- A camada de serviço de hiperescala é otimizada para cargas de trabalho de banco de dados muito grandes.
- A camada de serviço Comercialmente Crítico é apropriada para cargas de trabalho de negócios em que a latência de e/s é uma prioridade.

| | Tipo de recurso | Fins Gerais |  Hiperescala | Crítico para Empresas |
|:---:|:---:|:---:|:---:|:---:|
| **Melhor para** |Todos|  A maioria das cargas de trabalho de negócios. Oferece opções de armazenamento e computação balanceadas com enfoque no orçamento. | Aplicativos de dados com requisitos de capacidade de dados maiores e a capacidade de dimensionar automaticamente o armazenamento e dimensionar a computação de forma fluida. | Aplicativos OLTP com alta taxa de transação e e/s de latência mais baixa. Oferece maior resiliência a falhas usando várias réplicas isoladas.|
|  **Tipo de recurso** ||Banco de dados individual/pool elástico/instância gerenciada | Base de dados individual | Banco de dados individual/pool elástico/instância gerenciada |
| **Tamanho da computação**|Banco de dados individual/pool elástico * | 1 a 80 vCores | 1 a 80 vCores * | 1 a 80 vCores |
| |Instância gerida | 8, 16, 24, 32, 40, 64, 80 vCores | N/A | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Tipo de armazenamento** | Todos |Armazenamento remoto Premium (por instância) | Armazenamento desacoplado com cache de SSD local (por instância) | Armazenamento SSD local extremamente rápido (por instância) |
| **Tamanho do armazenamento** | Banco de dados individual/pool elástico | 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| | Instância gerida  | 32 GB A 8 TB | N/A | 32 GB A 4 TB |
| **Produtividade de e/s** | Banco de dados individual * * | 500 IOPS por vCore com 7000 IOPS máximo | O hiperscale é uma arquitetura de várias camadas com cache em vários níveis. O IOPs efetivo dependerá da carga de trabalho. | 5000 IOPS com IOPS máximo de 200.000|
| | Instância gerida | Depende do tamanho do arquivo | N/A | Instância Gerenciada: Depende do tamanho do arquivo|
|**Disponibilidade**|Todos|1 réplica, sem escala de leitura, sem cache local | Várias réplicas, até 15 escala de leitura e cache local parcial | 3 réplicas, 1 escala de leitura, HA com redundância de zona, cache local completo |
|**Únicos**|Todos|RA-GRS, 7-35 dias (7 dias por padrão)| RA-GRS, 7 dias, tempo constante de recuperação point-in-time (PITR) | RA-GRS, 7-35 dias (7 dias por padrão) |

\*Pools elásticos sem suporte na camada de serviço de hiperescala

### <a name="who-should-use-the-hyperscale-service-tier"></a>Quem deve usar a camada de serviço de hiperescala

A camada de serviço de hiperescala destina-se principalmente a clientes que têm grandes bancos de dados de SQL Server locais e querem modernizar seus aplicativos ao migrar para a nuvem ou para clientes que já estão usando o banco de dados SQL do Azure e que desejam expandir significativamente o potencial para o crescimento do banco de dados. O hiperescala também destina-se a clientes que buscam alto desempenho e alta escalabilidade. Com o hiperescala, você obtém:

- Suporte para até 100 TB de tamanho de banco de dados
- Backups rápidos de banco de dados, independentemente do tamanho do banco de dados (backups são baseados em instantâneos de arquivo)
- Restaurações rápidas de banco de dados, independentemente do tamanho do banco de dados (as restaurações são de instantâneos de arquivo)
- A taxa de transferência de log superior resulta em tempos de confirmação de transação rápidos, independentemente do tamanho do banco
- Leia escalar horizontalmente para um ou mais nós somente leitura para descarregar sua carga de trabalho de leitura e para Hot-standbys.
- Expansão rápida da computação, em constante tempo, para ser mais potente para acomodar a carga de trabalho pesada e reduzir verticalmente, em constante tempo. Isso é semelhante a escalar verticalmente entre um P6 para um P11, por exemplo, mas muito mais rápido, pois esse não é um tamanho de operação de dados.

### <a name="what-regions-currently-support-hyperscale"></a>Quais regiões dão suporte a hiperescala no momento

A camada de hiperescala do banco de dados SQL do Azure está disponível atualmente nas regiões listadas em [visão geral do hiperescala do banco de dados SQL do Azure](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Posso criar vários bancos de dados de hiperescala por servidor lógico

Sim. Para obter mais informações e limites sobre o número de bancos de dados de hiperescala por servidor lógico, consulte [limites de recursos do banco de dados SQL para bancos de dados individuais e em pool em um servidor lógico](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Quais são as características de desempenho de um banco de dados de hiperescala

A arquitetura de hiperescala do banco de dados SQL fornece alto desempenho e taxa de transferência ao dar suporte a grandes tamanhos de banco de dados 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Qual é a escalabilidade de um banco de dados de hiperescala

A hiperescala do banco de dados SQL fornece escalabilidade rápida com base na demanda da carga de trabalho.

- **Expansão/redução**

  Com o hiperescala, você pode escalar verticalmente o tamanho de computação primário em termos de recursos como CPU, memória e reduzir verticalmente, em constante tempo. Como o armazenamento é compartilhado, escalar verticalmente e reduzir horizontalmente não é um tamanho de operação de dados.  
- **Expansão/saída**

  Com o hiperescala, você também obtém a capacidade de provisionar um ou mais nós de computação adicionais que podem ser usados para atender às suas solicitações de leitura. Isso significa que você pode usar esses nós de computação adicionais como nós somente leitura para descarregar a carga de trabalho de leitura da computação primária. Além de somente leitura, esses nós também servem como hot-standby no caso de um failover do primário.

  O provisionamento de cada um desses nós de computação adicionais pode ser feito em tempo constante e é uma operação online. Você pode se conectar a esses nós de computação somente leitura, definindo o `ApplicationIntent` argumento na cadeia de conexão como `readonly`. Todas as conexões marcadas com `readonly` são automaticamente roteadas para um dos nós de computação somente leitura adicionais.

## <a name="deep-dive-questions"></a>Perguntas aprofundadas

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Posso misturar o hiperescala e os bancos de dados individuais em um único servidor lógico

Sim, pode.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>O hiperscale exige que meu modelo de programação de aplicativo seja alterado

Não, seu modelo de programação de aplicativo permanece como está. Você usa a cadeia de conexão como de costume e os outros modos regulares para interagir com o banco de dados SQL do Azure.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Quais níveis de isolamento da transação serão padrão no banco de dados de hiperescala do banco de dados SQL

No nó primário, o nível de isolamento da transação é RCSI (isolamento de instantâneo de leitura confirmado). Nos nós secundários de escala de leitura, o nível de isolamento é instantâneo.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Posso colocar minha licença de SQL Server local ou IaaS para a hiperescala do banco de dados SQL

Sim, [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) está disponível para hiperescala. Cada núcleo de SQL Server Standard pode ser mapeado para um vCores de hiperescala. Todo SQL Server Enterprise núcleo pode ser mapeado para quatro vCores de hiperescala. Você não precisa de uma licença SQL para réplicas secundárias. O preço de Benefício Híbrido do Azure será aplicado automaticamente às réplicas de escala de leitura (secundárias).

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>A qual tipo de carga de trabalho a hiperescala do banco de dados SQL foi projetada

O hiperescala do banco de dados SQL dá suporte a todas as cargas de trabalho de SQL Server, mas é essencialmente otimizada para OLTP. Você também pode colocar cargas de trabalho híbridas (HTAP) e analíticas (data mart).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Como escolher entre a SQL Data Warehouse do Azure e a hiperescala do banco de dados SQL

Se você estiver executando consultas de análise interativas usando SQL Server como um data warehouse, o banco de dados SQL é uma ótima opção, pois você pode hospedar data warehouses relativamente pequenos (como alguns TB de até 10s de TB) a um custo menor e pode migrar seus dados w arehouse carga de trabalho para o SQL Database de hiperescala sem alterações de código T-SQL.

Se você estiver executando a análise de dados em grande escala com consultas complexas e usando data warehouses de data warehouse paralelos (PDW), Teradata ou outros dados do processador (MPP) massivamente paralelos), SQL Data Warehouse poderá ser a melhor opção.
  
## <a name="sql-database-hyperscale-compute-questions"></a>Perguntas de computação de hiperescala do banco de dados SQL

### <a name="can-i-pause-my-compute-at-any-time"></a>Posso pausar minha computação a qualquer momento

Não no momento, no entanto, você pode dimensionar a computação e o número de réplicas para reduzir o custo fora do horário de pico.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Posso provisionar uma computação com RAM extra para minha carga de trabalho com uso intensivo de memória

Não. Para obter mais RAM, você precisa atualizar para um tamanho de computação mais alto. Para obter mais informações, consulte [armazenamento de hiperescala e tamanhos de computação](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Posso provisionar vários nós de computação de tamanhos diferentes

Não.

### <a name="how-many-read-scale-replicas-are-supported"></a>Quantas réplicas de escala de leitura têm suporte

Os bancos de dados de hiperescala são criados com uma réplica de escala de leitura (duas réplicas no total) por padrão. Você pode dimensionar o número de réplicas somente leitura entre 0 e 4 usando o [portal do Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Para alta disponibilidade, preciso provisionar nós de computação adicionais

Em bancos de dados de hiperescala, a resiliência é fornecida no nível de armazenamento. Você só precisa de uma réplica para fornecer resiliência. Quando a réplica de computação está inoperante, uma nova réplica é criada automaticamente sem perda de dados.

No entanto, se houver apenas uma réplica, poderá levar algum tempo para criar o cache local na nova réplica após o failover. Durante a fase de recompilação do cache, o banco de dados busca o dado diretamente dos servidores de página, resultando em desempenho de consulta e IOPS degradados.

Para aplicativos de missão crítica que exigem alta disponibilidade, você deve provisionar pelo menos dois nós de computação, incluindo o nó de computação primário (padrão). Dessa forma, há um hot-standby disponível no caso de um failover.

## <a name="data-size-and-storage-questions"></a>Perguntas de armazenamento e tamanho de dados

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Qual é o tamanho máximo de BD com suporte com a hiperescala do banco de dados SQL

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Qual é o tamanho do log de transações com hiperescala

O log de transações com hiperescala é praticamente infinito. Você não precisa se preocupar em ficar sem espaço de log em um sistema que tenha uma alta taxa de transferência de log. No entanto, a taxa de geração de log pode ser limitada para cargas de trabalho agressivas contínuas. A taxa de geração de log sustentada de pico é de aproximadamente 100 MB/s.

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>O meu banco de dados Temp é dimensionado conforme o My Database cresce

Seu `tempdb` banco de dados está localizado no armazenamento SSD local e é configurado com base no tamanho de computação que você provisiona. Seu `tempdb` é otimizado e disposto para fornecer benefícios máximos de desempenho. O `tempdb` tamanho não é configurável e é gerenciado para você pelo subsistema de armazenamento.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>O tamanho do meu banco de dados aumenta automaticamente ou preciso gerenciar o tamanho dos arquivos

O tamanho do banco de dados aumenta automaticamente à medida que você insere/ingeri mais informações.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Qual é o menor tamanho de banco de dados ao qual o hiperescala do banco de dados SQL dá suporte ou começa

10 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>Em que incrementos o tamanho do banco de dados aumenta

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>O armazenamento no banco de dados SQL é de hiperescala local ou remoto

Em hiperescala, os arquivos de dados são armazenados no armazenamento standard do Azure. Os dados são amplamente armazenados em cache no armazenamento SSD local, em computadores próximos aos nós de computação. Além disso, os nós de computação têm um cache no SSD local e na memória (pool de buffers, e assim por diante), para reduzir a frequência de busca de dados de nós remotos.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Posso gerenciar ou definir arquivos ou grupos de arquivo com hiperescala

Não
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Posso provisionar um limite rígido no crescimento de dados para meu banco

Não

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Como os arquivos de dados são dispostos com o SQL Database de hiperescala

Os arquivos de dados são controlados por servidores de páginas. Conforme aumenta o tamanho dos dados, os arquivos de dados e os nós de servidor de página associados são adicionados.

### <a name="is-database-shrink-supported"></a>É compatível com a redução de banco de dados

Não

### <a name="is-database-compression-supported"></a>Há suporte para compactação de banco de dados

Sim

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Se eu tiver uma tabela enorme, meus dados de tabela serão distribuídos entre vários arquivos de dados

Sim. As páginas de dados associadas a uma determinada tabela podem terminar em vários arquivos de dados, que fazem parte do mesmo grupo de arquivos. SQL Server usa uma [estratégia de preenchimento proporcional](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) para distribuir dados em arquivos de dados.

## <a name="data-migration-questions"></a>Perguntas sobre migração de dados

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Posso mover meus bancos de dados SQL do Azure existentes para a camada de serviço de hiperescala

Sim. Você pode mover seus bancos de dados SQL do Azure para hiperescala. Essa é uma migração unidirecional. Não é possível mover bancos de dados de hiperescala para outra camada de serviço. Recomendamos que você faça uma cópia dos seus bancos de dados de produção e migre para o hiperscale para provas de conceito (POCs).
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Posso mover meus bancos de dados de hiperescala para outras edições

Não. Neste momento, você não pode mover um banco de dados de hiperescala para outra camada de serviço.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Eu perco qualquer funcionalidade ou recurso após a migração para a camada de serviço de hiperescala

Sim. Alguns dos recursos do banco de dados SQL do Azure ainda não são compatíveis com o hiperscale, incluindo, mas não limitado, o backup de retenção de longo prazo. Depois de migrar seus bancos de dados para o hiperescala, esses recursos param de funcionar.  Esperamos que essas limitações sejam temporárias.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Posso mover meu banco de dados de SQL Server local ou meu banco de dados de máquina virtual SQL Server para o subdimensionamento

Sim. Você pode usar todas as tecnologias de migração existentes para migrar para o hiperescala, incluindo BACPAC, replicação transacional, carregamento de dados lógicos. Consulte também o [serviço de migração de banco de dados do Azure](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Qual é o meu tempo de inatividade durante a migração de um ambiente de máquina virtual ou local para o hiperescala e como posso minimizá-lo

O tempo de inatividade é o mesmo que o tempo de inatividade quando você migra seus bancos de dados para um único Database no banco de dados SQL do Azure. Você pode usar [a replicação](replication-to-sql-database.md#data-migration-scenario
) transacional para minimizar a migração de tempo de inatividade para bancos de dados de até poucos TB de tamanho. Para um banco de dados muito grande (mais de 10 TB), você pode considerar a migração do ADF usando AAD, Spark ou outras tecnologias de movimentação de dados.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Quanto tempo levaria para trazer a quantidade X de dados para o SQL Database de hiperescala

O hiperscale é capaz de consumir 100 MB/s de dados novos/alterados.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Posso ler dados do armazenamento de BLOBs e fazer o carregamento rápido (como o polybase e o SQL Data Warehouse)

Você pode ler dados do armazenamento do Azure e carregar o carregamento de dados em um banco de dado de hiperescala (assim como você pode fazer com um único banco de dados regular). Atualmente, o polybase não tem suporte no banco de dados SQL do Azure. Você pode fazer o polybase usando [Azure data Factory](https://docs.microsoft.com/azure/data-factory/) ou executando um trabalho do spark no [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) com o [conector do Spark para SQL](sql-database-spark-connector.md). O conector do Spark para SQL dá suporte à inserção em massa.

Não há suporte para recuperação simples ou modelo de log em massa em hiperescala. O modelo de recuperação completa é necessário para fornecer alta disponibilidade. No entanto, o hiperscale fornece uma melhor taxa de ingestão de dados em comparação a um único banco de dado SQL do Azure devido à nova arquitetura de log.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>O SQL Database de hiperescala permite o provisionamento de vários nós para ingerir grandes quantidades de dados

Não. O hiperescala do banco de dados SQL é uma arquitetura SMP e não é um multiprocessamento assimétrico ou uma arquitetura de vários mestres. Você só pode criar várias réplicas para escalar horizontalmente cargas de trabalho somente leitura.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Qual é a versão mais antiga do SQL Server o suporte a hiperescala do banco de dados SQL será migrado de

SQL Server 2005. Para obter mais informações, consulte migrar para um banco de dados [individual ou um banco de dados em pool](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Para problemas de compatibilidade, consulte [resolvendo problemas de compatibilidade de migração de banco de dados](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>O hiperescala do banco de dados SQL é compatível com a migração de outras fontes, como Aurora, MySQL, Oracle, DB2 e outras plataformas de banco de dado

Sim. Provenientes de diferentes fontes de dados que não SQL Server requer migração lógica. Você pode usar o [serviço de migração de banco de dados do Azure](../dms/dms-overview.md) para uma migração lógica.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Perguntas sobre continuidade dos negócios e recuperação de desastre

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Quais SLAs são fornecidos para um banco de dados de hiperescala

Com o primário padrão mais 1 secundário legível, o SLA é de 99,95% de disponibilidade.  Com mais réplicas, o SLA vai até 99,99%.  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Os backups de banco de dados são gerenciados para mim pelo serviço de banco de dados SQL do Azure

Sim

### <a name="how-often-are-the-database-backups-taken"></a>Com que frequência os backups de banco de dados são feitos

Não há backups completos, diferenciais e de log tradicionais para bancos de dados de hiperescala do banco de dados SQL. Em vez disso, há instantâneos regulares dos arquivos de dados e o log gerado é simplesmente mantido como está para o período de retenção configurado ou disponível para você.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>O ponto de suporte de hiperescala do banco de dados SQL foi restaurado

Sim

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Qual é o objetivo de ponto de recuperação (RPO)/Recovery tempo objetivo (RTO) com backup/restauração no banco de dados SQL de hiperescala

O RPO é de 0 min. A meta de RTO é menor que 10 minutos, independentemente do tamanho do banco de dados. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Os backups de grandes bancos de dados afetam o desempenho de computação no meu primário

Não. Os backups são gerenciados pelo subsistema de armazenamento e aproveitam os instantâneos de arquivo. Eles não afetam a carga de trabalho do usuário no primário.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Posso executar a restauração geográfica com um banco de dados de hiperescala do banco de dados SQL

Sim.  A restauração geográfica tem suporte total.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Posso configurar a replicação geográfica com o banco de dados de hiperescala do banco de dados SQL

Neste momento, não.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Meus nós de computação secundários são replicados geograficamente com a hiperescala do banco de dados SQL

Neste momento, não.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Posso pegar um backup de banco de dados do SQL de hiperescala e restaurá-lo para meu servidor local ou SQL Server na VM

Não. O formato de armazenamento para bancos de dados de hiperescala é diferente do SQL Server tradicional e você não controla os backups ou tem acesso a eles. Para retirar seus dados de um banco de dado do SQL, use o serviço de exportação ou use o script e o BCP.

## <a name="cross-feature-questions"></a>Perguntas entre recursos

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Eu perco qualquer funcionalidade ou recurso após a migração para a camada de serviço de hiperescala

Sim. Alguns dos recursos do banco de dados SQL do Azure não têm suporte em hiperescala, incluindo, mas não limitado, backup de retenção de longo prazo. Depois de migrar seus bancos de dados para o hiperescala, esses recursos param de funcionar.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>O polybase funcionará com a hiperescala do banco de dados SQL

Não. O polybase não tem suporte no banco de dados SQL do Azure.

### <a name="does-the-compute-have-support-for-r-and-python"></a>A computação tem suporte para R e Python

Não. R e Python não têm suporte no banco de dados SQL do Azure.

### <a name="are-the-compute-nodes-containerized"></a>Os nós de computação estão em contêineres

Não. Seu banco de dados reside em uma VM de computação e não em um contêiner.

## <a name="performance-questions"></a>Perguntas de desempenho

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Quanto taxa de transferência posso enviar por push na maior computação de hiperescala do banco de dados SQL

Vimos um 100 MB/s de dados de alteração consistentes (geração de dados do log de transações)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Quantos IOPS obtenho na maior computação de hiperescala do banco de dados SQL

O IOPS e a latência de e/s variam dependendo dos padrões de carga de trabalho.  Se os dados que precisam ser acessados forem locais para o cache da computação, serão os mesmos padrões de e/s que o SSD local.   

### <a name="does-my-throughput-get-affected-by-backups"></a>Minha taxa de transferência é afetada pelos backups

Não. A computação é dissociada da camada de armazenamento para evitar o impacto na computação.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Minha taxa de transferência é afetada ao provisionar nós de computação adicionais

Como o armazenamento é compartilhado e não há nenhuma replicação física direta acontecendo entre nós de computação primários e secundários, tecnicamente, a taxa de transferência no nó primário não será afetada pela adição de nós de escala de leitura. No entanto, podemos limitar a carga de trabalho agressiva contínua para permitir que o log seja aplicado em nós secundários e servidores de página para acompanhar e evitar um desempenho de leitura inadequado em nós secundários.

## <a name="scalability-questions"></a>Perguntas sobre escalabilidade

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Quanto tempo levaria para escalar e reduzir verticalmente um nó de computação

O dimensionamento da computação para cima ou para baixo deve levar de 5-10 minutos, independentemente do tamanho dos dados.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>O meu banco de dados está offline enquanto a operação de expansão/redução está em andamento

Não. A expansão e a redução ficarão online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Devo esperar a conexão suspensa quando as operações de dimensionamento estiverem em andamento

A expansão ou redução dos resultados em conexões existentes são descartadas quando ocorre um failover para o nó de computação com o tamanho do destino. Adicionar réplicas de leitura não resulta em quedas de conexão.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>A expansão e a redução de operações de nós de computação são disparadas automaticamente ou acionadas pelo usuário final

Usuário final. Não automático.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>O My `tempb` também aumenta à medida que a computação é dimensionada

Sim. O DB temporário será dimensionado automaticamente conforme a computação aumentar.  

### <a name="can-i-provision-multiple-primary-compute-nodes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Posso provisionar vários nós de computação primários, como um sistema de vários mestres, em que vários cabeçotes de computação primários podem impulsionar um nível mais alto de simultaneidade

Não. Somente o nó de computação primário aceita solicitações de leitura/gravação. Nós de computação secundários só aceitam solicitações somente leitura.

## <a name="read-scale-questions"></a>Ler perguntas de escala

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Quantos nós de computação secundários posso provisionar

Criamos 2 réplicas para bancos de dados de hiperescala por padrão. Se você quiser ajustar o número de réplicas, poderá fazer isso usando [portal do Azure](https://portal.azure.com).

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Como fazer conectar-se a esses nós de computação secundários

Você pode se conectar a esses nós de computação somente leitura, definindo o `ApplicationIntent` argumento na cadeia de conexão como `readonly`. Todas as conexões marcadas com `readonly` são automaticamente roteadas para um dos nós de computação somente leitura adicionais.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Posso criar um ponto de extremidade dedicado para a réplica de escala de leitura

Não. Você só pode se conectar à réplica de escala de leitura `ApplicationIntent=ReadOnly`especificando.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>O sistema realiza o balanceamento de carga inteligente da carga de trabalho de leitura

Não. A carga de trabalho somente leitura é redirecionada para uma réplica de escala de leitura aleatória.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Posso escalar/reduzir verticalmente os nós de computação secundários independentemente da computação primária

Não. Os nós de computação secundários também são usados para HA, portanto, precisam ser a mesma configuração que o primário, no caso de um failover.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Obtenho um dimensionamento de banco de BD temporário diferente para minha computação primária e meus nós de computação secundários adicionais

Não. Seu `tempdb` é configurado com base no provisionamento de tamanho de computação, seus nós de computação secundários têm o mesmo tamanho que a computação primária.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Posso adicionar índices e exibições em meus nós de computação secundários

Não. Os bancos de dados de hiperescala têm armazenamento compartilhado, o que significa que todos os nós de computação veem as mesmas tabelas, índices e exibições. Se você quiser índices adicionais otimizados para leituras no secundário – você deve adicioná-los primeiro no primário.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Quanto atraso haverá entre o nó de computação primário e o secundário

A partir do momento em que uma transação é confirmada no primário, dependendo da taxa de geração de log, ela pode ser instantânea ou em milissegundos baixos.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a camada de serviço de hiperescala, consulte [camada de serviço](sql-database-service-tier-hyperscale.md)de hiperescala.
