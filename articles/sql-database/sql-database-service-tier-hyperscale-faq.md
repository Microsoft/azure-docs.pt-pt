---
title: FAQ de hiperescala do banco de dados SQL do Azure
description: Respostas a perguntas comuns que os clientes perguntam sobre um banco de dados SQL do Azure na camada de serviço de hiperescala – normalmente chamado de banco de dados de hiperescala.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 10/12/2019
ms.openlocfilehash: 6a25d5197746e04ffa25ee397e6d8451e24ae176
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614995"
---
# <a name="azure-sql-database-hyperscale-faq"></a>FAQ de hiperescala do banco de dados SQL do Azure

Este artigo fornece respostas para as perguntas frequentes para os clientes que consideram um banco de dados na camada de serviço de hiperescala do banco de dados SQL do Azure, conhecida como apenas hiperescala no restante das perguntas frequentes. Este artigo descreve os cenários que o hiperscale dá suporte e os recursos que são compatíveis com o hiperscale.

- Essas perguntas frequentes são destinadas a leitores que têm uma compreensão breve da camada de serviço de hiperescala e estão procurando dúvidas e preocupações específicas.
- Essas perguntas frequentes não devem ser uma Guidebook ou responder a perguntas sobre como usar um banco de dados de hiperescala. Para obter uma introdução ao hiperescala, recomendamos que você consulte a documentação do [hiperescala do banco de dados SQL do Azure](sql-database-service-tier-hyperscale.md) .

## <a name="general-questions"></a>Perguntas Gerais

### <a name="what-is-a-hyperscale-database"></a>O que é um banco de dados de hiperescala

Um banco de dados de hiperescala é um banco de dados SQL do Azure na camada de serviço de hiperescala que é apoiada pela tecnologia de armazenamento de escala horizontal de hiperescala. Um banco de dados de hiperescala dá suporte a até 100 TB de data e fornece alta taxa de transferência e desempenho, bem como o dimensionamento rápido para se adaptar aos requisitos de carga de trabalho. O dimensionamento é transparente para o aplicativo – conectividade, processamento de consulta, etc., funciona como qualquer outro banco de dados SQL do Azure.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Quais tipos de recursos e modelos de compra dão suporte a hiperescala

A camada de serviço de hiperescala está disponível somente para bancos de dados individuais usando o modelo de compra baseado em vCore no banco de dados SQL do Azure.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Como a camada de serviço de hiperescala difere das camadas de serviço Uso Geral e Comercialmente Crítico

As camadas de serviço baseadas em vCore são diferenciadas com base na disponibilidade do banco de dados e no tipo de armazenamento, desempenho e tamanho máximo, conforme descrito na tabela a seguir.

| | Tipo de recurso | Fins Gerais |  Hiperescala | Crítico para a Empresa |
|:---:|:---:|:---:|:---:|:---:|
| **Melhor para** |Tudo|Oferece opções de armazenamento e computação balanceadas com enfoque no orçamento.|A maioria das cargas de trabalho de negócios. Dimensionamento automático de tamanho de armazenamento de até 100 TB, dimensionamento rápido vertical e horizontal de computação, restauração rápida de banco de dados.|Aplicativos OLTP com alta taxa de transação e baixa latência de e/s. Oferece maior resiliência a falhas e failovers rápidos usando várias réplicas atualizadas de forma síncrona.|
|  **Tipo de recurso** ||Banco de dados individual/pool elástico/instância gerenciada | Base de dados única | Banco de dados individual/pool elástico/instância gerenciada |
| **Tamanho da computação**|Banco de dados individual/pool elástico * | 1 a 80 vCores | 1 a 80 vCores * | 1 a 80 vCores |
| |Instância gerida | 8, 16, 24, 32, 40, 64, 80 vCores | N/A | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Tipo de armazenamento** | Tudo |Armazenamento remoto Premium (por instância) | Armazenamento desacoplado com cache de SSD local (por instância) | Armazenamento SSD local extremamente rápido (por instância) |
| **Tamanho do armazenamento** | Banco de dados individual/pool elástico *| 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| | Instância gerida  | 32 GB A 8 TB | N/A | 32 GB A 4 TB |
| **IOPS** | Base de dados única | 500 IOPS por vCore com 7000 IOPS máximo | O hiperscale é uma arquitetura de várias camadas com cache em vários níveis. O IOPS efetivo dependerá da carga de trabalho. | 5000 IOPS com IOPS máximo de 200.000|
| | Instância gerida | Depende do tamanho do arquivo | N/A | 1375 IOPS/vCore |
|**Disponibilidade**|Tudo|1 réplica, sem escala de leitura, sem cache local | Várias réplicas, até 4 expansão de leitura, cache local parcial | 3 réplicas, 1 expansão de leitura, HA com redundância de zona, armazenamento local completo |
|**Únicos**|Tudo|RA-GRS, 7-35 dias de retenção (7 dias por padrão)| RA-GRS, 7 dias de retenção, PITR (tempo constante de recuperação pontual) | RA-GRS, 7-35 dias de retenção (7 dias por padrão) |

\* pools elásticos não têm suporte na camada de serviço de hiperescala

### <a name="who-should-use-the-hyperscale-service-tier"></a>Quem deve usar a camada de serviço de hiperescala

A camada de serviço de hiperescala destina-se a clientes que têm grandes bancos de dados de SQL Server locais e que desejam modernizar seus aplicativos ao migrar para a nuvem ou para clientes que já estão usando o banco de dados SQL do Azure e que desejam expandir significativamente o potencial para o crescimento do banco de dados. O hiperescala também destina-se a clientes que buscam alto desempenho e alta escalabilidade. Com o hiperescala, você obtém:

- Tamanho do banco de dados de até 100 TB
- Backups rápidos de banco de dados, independentemente do tamanho do banco de dados (backups são baseados em instantâneos de armazenamento)
- Restaurações rápidas de banco de dados, independentemente do tamanho do banco de dados (as restaurações são de instantâneos de armazenamento)
- Maior taxa de transferência de log, independentemente do tamanho do banco de dados e do número de vCores
- Escala horizontal de leitura usando uma ou mais réplicas somente leitura, usadas para descarregamento de leitura e em esperas ativas.
- Expansão rápida da computação, em constante tempo, para ser mais potente para acomodar a carga de trabalho pesada e reduzir verticalmente, em constante tempo. Isso é semelhante a escalar verticalmente entre uma P6 e uma P11, por exemplo, mas muito mais rápido, pois esse não é um tamanho de operação de dados.

### <a name="what-regions-currently-support-hyperscale"></a>Quais regiões dão suporte a hiperescala no momento

A camada de serviço de hiperescala está disponível atualmente nas regiões listadas em [visão geral do hiperescala do banco de dados SQL do Azure](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Posso criar vários bancos de dados de hiperescala por servidor lógico

Sim. Para obter mais informações e limites sobre o número de bancos de dados de hiperescala por servidor lógico, consulte [limites de recursos do banco de dados SQL para bancos de dados individuais e em pool em um servidor lógico](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Quais são as características de desempenho de um banco de dados de hiperescala

A arquitetura de hiperescala fornece alto desempenho e taxa de transferência ao dar suporte a tamanhos de bancos de dados grandes. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Qual é a escalabilidade de um banco de dados de hiperescala

O hiperscale fornece escalabilidade rápida com base em sua demanda de carga de trabalho.

- **Expansão/redução**

  Com o hiperescala, você pode escalar verticalmente o tamanho da computação primária em termos de recursos, como CPU e memória, e, em seguida, reduzir verticalmente, em tempo constante. Como o armazenamento é compartilhado, escalar verticalmente e reduzir horizontalmente não é um tamanho de operação de dados.  
- **Expansão/saída**

  Com o hiperescala, você também obtém a capacidade de provisionar uma ou mais réplicas de computação adicionais que você pode usar para atender às suas solicitações de leitura. Isso significa que você pode usar essas réplicas de computação adicionais como réplicas somente leitura para descarregar a carga de trabalho de leitura da computação primária. Além de somente leitura, essas réplicas também servem como Hot-standbys no caso de um failover do primário.

  O provisionamento de cada uma dessas réplicas de computação adicionais pode ser feito em tempo constante e é uma operação online. Você pode se conectar a essas réplicas de computação somente leitura, definindo o argumento `ApplicationIntent` na cadeia de conexão como `ReadOnly`. Todas as conexões com a intenção de `ReadOnly` aplicativo são automaticamente roteadas para uma das réplicas de computação somente leitura adicionais.

## <a name="deep-dive-questions"></a>Perguntas aprofundadas

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Posso misturar o hiperescala e os bancos de dados individuais em um único servidor lógico

Sim, pode.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>O hiperscale exige que meu modelo de programação de aplicativo seja alterado

Não, seu modelo de programação de aplicativo permanece como está. Você usa a cadeia de conexão como de costume e as outras maneiras normais de interagir com seu banco de dados de hiperescala.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Qual nível de isolamento da transação é o padrão em um banco de dados de hiperescala

Na réplica primária, o nível de isolamento da transação padrão é RCSI (isolamento de instantâneo de leitura comprometido). Nas réplicas secundárias de expansão de leitura, o nível de isolamento padrão é instantâneo.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Posso colocar minha licença de SQL Server local ou IaaS em hiperescala

Sim, [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) está disponível para hiperescala. Cada núcleo de SQL Server Standard pode ser mapeado para um vCores de hiperescala. Todo SQL Server Enterprise núcleo pode ser mapeado para quatro vCores de hiperescala. Você não precisa de uma licença SQL para réplicas secundárias. O preço de Benefício Híbrido do Azure será aplicado automaticamente às réplicas de escala horizontal de leitura (secundária).

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Para qual tipo de carga de trabalho a hiperescala é projetada

O hiperscale dá suporte a todas as cargas de trabalho de SQL Server, mas é essencialmente otimizado para OLTP. Você também pode colocar cargas de trabalho híbridas (HTAP) e analíticas (data mart).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Como escolher entre a SQL Data Warehouse do Azure e a hiperescala do banco de dados SQL do Azure

Se você estiver executando consultas de análise interativas usando SQL Server como um data warehouse, o hiperscale é uma ótima opção, pois você pode hospedar data warehouses de pequeno e médio porte (como alguns TB de até 100 TB) a um custo mais baixo e pode migrar seus SQL Server War de dados Ehouse cargas de trabalho para hiperescala com alterações mínimas de código T-SQL.

Se você estiver executando a análise de dados em grande escala com consultas complexas e taxas de ingestão sustentadas superiores a 100 MB/s ou usando o PDW (Parallel data warehouse), o Teradata ou outros data warehouses de processamento paralelo (MPP), o SQL Data Warehouse poderá ser a melhor opção.
  
## <a name="hyperscale-compute-questions"></a>Perguntas de computação de hiperescala

### <a name="can-i-pause-my-compute-at-any-time"></a>Posso pausar minha computação a qualquer momento

Não no momento, no entanto, você pode dimensionar a computação e o número de réplicas para reduzir o custo fora do horário de pico.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Posso provisionar uma réplica de computação com RAM extra para minha carga de trabalho com uso intensivo de memória

Não. Para obter mais RAM, você precisa atualizar para um tamanho de computação mais alto. Para obter mais informações, consulte [armazenamento de hiperescala e tamanhos de computação](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Posso provisionar várias réplicas de computação de tamanhos diferentes

Não.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Quantas réplicas de expansão de leitura têm suporte

Os bancos de dados de hiperescala são criados com uma réplica de escala horizontal de leitura (duas réplicas, incluindo a primária) por padrão. Você pode dimensionar o número de réplicas somente leitura entre 0 e 4 usando o [portal do Azure](https://portal.azure.com) ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Para alta disponibilidade, preciso provisionar réplicas de computação adicionais

Em bancos de dados de hiperescalas, a resiliência é fornecida no nível de armazenamento. Você só precisa de uma réplica para fornecer resiliência. Quando a réplica de computação está inoperante, uma nova réplica é criada automaticamente sem perda de dados.

No entanto, se houver apenas uma réplica, poderá levar algum tempo para criar o cache local na nova réplica após o failover. Durante a fase de recompilação do cache, o banco de dados busca o dado diretamente dos servidores de página, resultando em maior latência de armazenamento e desempenho de consulta degradado.

Para aplicativos de missão crítica que exigem alta disponibilidade com impacto mínimo sobre o failover, você deve provisionar pelo menos duas réplicas de computação, incluindo a réplica de computação primária. Esta é a configuração predefinida. Dessa forma, há uma réplica hot-standby disponível que funciona como um destino de failover.

## <a name="data-size-and-storage-questions"></a>Perguntas de armazenamento e tamanho de dados

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Qual é o tamanho máximo de banco de dados com suporte com o hiperscale

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Qual é o tamanho do log de transações com hiperescala

O log de transações com hiperescala é praticamente infinito. Você não precisa se preocupar em ficar sem espaço de log em um sistema que tenha uma alta taxa de transferência de log. No entanto, a taxa de geração de log pode ser limitada para cargas de trabalho de gravação agressivamente contínuas. A taxa de geração de log sustentada de pico é de 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>O meu `tempdb` escala conforme o meu banco de dados cresce

Seu banco de dados `tempdb` está localizado no armazenamento SSD local e é dimensionado proporcionalmente ao tamanho de computação que você provisiona. Seu `tempdb` é otimizado para fornecer benefícios máximos de desempenho. o tamanho do `tempdb` não é configurável e é gerenciado para você.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>O tamanho do meu banco de dados aumenta automaticamente ou preciso gerenciar o tamanho dos arquivos

O tamanho do banco de dados aumenta automaticamente à medida que você insere/ingeri mais informações.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Qual é o menor tamanho de banco de dados que o hiperscale dá suporte ou começa com

40 GB. Um banco de dados de hiperescala é criado com um tamanho inicial de 10 GB. Em seguida, ele começa crescendo 10 GB a cada 10 minutos até atingir o tamanho de 40 GB. Cada um desses 10 GB Chuck é alocado em um servidor de página diferente para fornecer mais IOPS e um paralelismo de e/s maior. Por causa dessa otimização, mesmo se você escolher o tamanho inicial do banco de dados menor que 40 GB, o banco de dados aumentará para pelo menos 40 GB automaticamente.

### <a name="in-what-increments-does-my-database-size-grow"></a>Em que incrementos o tamanho do banco de dados aumenta

Cada arquivo de dados aumenta em 10 GB. Vários arquivos de dados podem crescer ao mesmo tempo.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>O armazenamento está em hiperescala local ou remoto

Em hiperescala, os arquivos de dados são armazenados no armazenamento standard do Azure. Os dados são totalmente armazenados em cache no armazenamento SSD local, em servidores de páginas próximos às réplicas de computação. Além disso, as réplicas de computação têm caches de dados no SSD local e na memória, para reduzir a frequência de busca de dados de servidores de páginas remotas.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Posso gerenciar ou definir arquivos ou grupos de arquivo com hiperescala

Não. Os arquivos de dados são adicionados automaticamente. Os motivos comuns para a criação de grupos de fileadicionais não se aplicam à arquitetura de armazenamento em hiperescala.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Posso provisionar um limite rígido no crescimento de dados para meu banco

Não.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Como os arquivos de dados são dispostos com hiperescala

Os arquivos de dados são controlados por servidores de página, com um servidor de página por arquivo de dados. Conforme aumenta o tamanho dos dados, os arquivos de dados e os servidores de página associados são adicionados.

### <a name="is-database-shrink-supported"></a>É compatível com a redução de banco de dados

Não.

### <a name="is-data-compression-supported"></a>A compactação de dados é suportada

Sim, incluindo a compactação de linha, página e columnstore.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Se eu tiver uma tabela enorme, meus dados de tabela serão distribuídos entre vários arquivos de dados

Sim. As páginas de dados associadas a uma determinada tabela podem terminar em vários arquivos de dados, que fazem parte do mesmo grupo de arquivos. SQL Server usa a [estratégia de preenchimento proporcional](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) para distribuir dados em arquivos de dados.

## <a name="data-migration-questions"></a>Perguntas sobre migração de dados

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Posso mover meus bancos de dados SQL do Azure existentes para a camada de serviço de hiperescala

Sim. Você pode mover seus bancos de dados SQL do Azure para hiperescala. Essa é uma migração unidirecional. Não é possível mover bancos de dados de hiperescala para outra camada de serviço. Para provas de conceito (POCs), recomendamos que você faça uma cópia do seu banco de dados e migre a cópia para o hiperescala.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Posso mover meus bancos de dados de hiperescala para outras camadas de serviço

Não. Neste momento, você não pode mover um banco de dados de hiperescala para outra camada de serviço.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Eu perco qualquer funcionalidade ou recurso após a migração para a camada de serviço de hiperescala

Sim. Alguns dos recursos do banco de dados SQL do Azure ainda não são compatíveis com o hiperscale, incluindo, entre outros, a retenção de backup de longo prazo. Depois de migrar seus bancos de dados para o hiperescala, esses recursos param de funcionar.  Esperamos que essas limitações sejam temporárias.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Posso mover meu banco de dados local SQL Server ou meu banco de dados SQL Server em uma máquina virtual de nuvem para o subdimensionamento

Sim. Você pode usar todas as tecnologias de migração existentes para migrar para o hiperescala, incluindo a replicação transacional e quaisquer outras tecnologias de movimentação de dados (cópia em massa, Azure Data Factory, Azure Databricks, SSIS). Consulte também o [serviço de migração de banco de dados do Azure](../dms/dms-overview.md), que dá suporte a vários cenários de migração.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Qual é o meu tempo de inatividade durante a migração de um ambiente de máquina virtual ou local para o hiperescala e como posso minimizá-lo

O tempo de inatividade para a migração para o hiperescala é o mesmo que o tempo de inatividade quando você migra seus bancos de dados para outras camadas de serviço do Azure SQL Database. Você pode usar a [replicação transacional](replication-to-sql-database.md#data-migration-scenario
) para minimizar a migração de tempo de inatividade para bancos de dados de até poucos TB de tamanho. Para bancos de dados muito grandes (mais de 10 TB), você pode considerar a migração do ADF usando AAD, Spark ou outras tecnologias de movimentação de dados.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Quanto tempo levaria para colocar a quantidade X de dados em hiperescala

O hiperscale é capaz de consumir 100 MB/s de dados novos/alterados, mas o tempo necessário para mover dados para bancos de dado SQL do Azure também é afetado pela taxa de transferência de rede disponível, pela velocidade de leitura de origem e pelo objetivo de nível de serviço do banco de dados de destino.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>Posso ler dados do armazenamento de BLOBs e fazer o carregamento rápido (como o polybase no SQL Data Warehouse)

Você pode fazer com que um aplicativo cliente leia dados do armazenamento do Azure e carregue a carga de dados em um banco de dados de hiperescala (assim como você pode com qualquer outro banco de dado SQL do Azure). Atualmente, o polybase não tem suporte no banco de dados SQL do Azure. Como alternativa para fornecer carga rápida, você pode usar [Azure data Factory](https://docs.microsoft.com/azure/data-factory/)ou usar um trabalho do spark no [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) com o [conector do Spark para SQL](sql-database-spark-connector.md). O conector do Spark para SQL dá suporte à inserção em massa.

Também é possível ler em massa dados do armazenamento de BLOBs do Azure usando BULK INSERT ou OPENROWSET: [exemplos de acesso em massa aos dados no armazenamento de BLOBs do Azure](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

Não há suporte para recuperação simples ou modelo de log em massa em hiperescala. O modelo de recuperação completa é necessário para fornecer alta disponibilidade e recuperação pontual. No entanto, a arquitetura de log de hiperescala fornece uma melhor taxa de ingestão de dados em comparação com outras camadas de serviço do Azure SQL Database.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>O hiperscale permite o provisionamento de vários nós para a ingestão paralela de grandes quantidades de dados

Não. O hiperscale é uma arquitetura de multi-processamento simétrico (SMP) e não é um processamento paralelo maciço (MPP) ou uma arquitetura de vários mestres. Você só pode criar várias réplicas para escalar horizontalmente cargas de trabalho somente leitura.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Qual é a versão mais antiga do SQL Server com suporte para migração para hiperescala

SQL Server 2005. Para obter mais informações, consulte [migrar para um banco de dados individual ou um banco de dados em pool](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Para problemas de compatibilidade, consulte [resolvendo problemas de compatibilidade de migração de banco de dados](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>O hiperscale oferece suporte à migração de outras fontes de dados, como Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 e outras plataformas de banco de dado

Sim. O [serviço de migração de banco de dados do Azure](../dms/dms-overview.md) dá suporte a vários cenários de

## <a name="business-continuity-and-disaster-recovery-questions"></a>Perguntas sobre continuidade dos negócios e recuperação de desastre

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Quais SLAs são fornecidos para um banco de dados de hiperescala

Consulte [SLA para o banco de dados SQL do Azure](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Réplicas de computação secundárias adicionais aumentam a disponibilidade, até 99,99% para um banco de dados com duas ou mais réplicas de computação secundárias.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Os backups de banco de dados são gerenciados para mim pelo serviço de banco de dados SQL do Azure

Sim.

### <a name="how-often-are-the-database-backups-taken"></a>Com que frequência os backups de banco de dados são feitos

Não há backups completos, diferenciais e de log tradicionais para bancos de dados de hiperescala. Em vez disso, há instantâneos de armazenamento regular de arquivos de dados. O log gerado é simplesmente mantido como está para o período de retenção configurado, permitindo a restauração para qualquer ponto no tempo dentro do período de retenção.

### <a name="does-hyperscale-support-point-in-time-restore"></a>A restauração pontual do suporte a hiperescala

Sim.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>O que é o RTO (objetivo de ponto de recuperação)/Recovery tempo (objetivo) para restauração de banco de dados em hiperescala

O RPO é de 0 min. A meta de RTO é menor que 10 minutos, independentemente do tamanho do banco de dados. 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>O backup de banco de dados afeta o desempenho de computação em minhas réplicas primárias ou secundárias

Não. Os backups são gerenciados pelo subsistema de armazenamento e aproveitam os instantâneos de armazenamento. Eles não afetam as cargas de trabalho do usuário.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Posso executar a restauração geográfica com um banco de dados de hiperescala

Sim.  A restauração geográfica tem suporte total. Diferentemente da restauração pontual, a restauração geográfica pode exigir uma operação de tamanho de dados de execução longa.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Posso configurar a replicação geográfica com o banco de dados de hiperescala

Neste momento, não.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Posso pegar um backup de banco de dados de hiperescala e restaurá-lo para meu servidor local ou em SQL Server em uma VM

Não. O formato de armazenamento para bancos de dados de hiperescala é diferente de qualquer versão lançada do SQL Server, e você não controla os backups ou tem acesso a eles. Para retirar seus dados de um banco de dado de hiperescala, você pode extrair dados usando qualquer tecnologia de movimentação de dados, ou seja, Azure Data Factory, Azure Databricks, SSIS etc.

## <a name="cross-feature-questions"></a>Perguntas entre recursos

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Eu perco qualquer funcionalidade ou recurso após a migração para a camada de serviço de hiperescala

Sim. Alguns dos recursos do banco de dados SQL do Azure não têm suporte em hiperescala, incluindo, mas não se limitando à retenção de backup de longo prazo. Depois de migrar seus bancos de dados para o hiperescala, esses recursos param de funcionar.

### <a name="will-polybase-work-with-hyperscale"></a>O polybase trabalhará com o hiperescala

Não. O polybase não tem suporte no banco de dados SQL do Azure.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>O Multiscale tem suporte para R e Python

Neste momento, não.

### <a name="are-compute-nodes-containerized"></a>Nós de computação são em contêineres

Não. Os processos de hiperescala são executados em um [Service Fabric](https://azure.microsoft.com/services/service-fabric/) nós (VMS), não em contêineres.

## <a name="performance-questions"></a>Perguntas de desempenho

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Quanto taxa de transferência de gravação posso enviar por push em um banco de dados de hiperescala

O limite de produtividade do log de transações é definido como 100 MB/s para qualquer tamanho de computação de hiperescala. A capacidade de atingir essa taxa depende de vários fatores, incluindo, entre outros, o tipo de carga de trabalho, a configuração do cliente e a existência de capacidade de computação suficiente na réplica de computação primária para produzir o log a essa taxa.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Quantos IOPS eu obtenho na maior computação

O IOPS e a latência de e/s variam dependendo dos padrões de carga de trabalho. Se os dados que estão sendo acessados forem armazenados em cache na réplica de computação, você verá o desempenho de e/s semelhante ao SSD local.

### <a name="does-my-throughput-get-affected-by-backups"></a>Minha taxa de transferência é afetada pelos backups

Não. A computação é dissociada da camada de armazenamento. Isso elimina o impacto no desempenho do backup.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Minha taxa de transferência é afetada ao provisionar réplicas de computação adicionais

Como o armazenamento é compartilhado e não há nenhuma replicação física direta acontecendo entre réplicas de computação primárias e secundárias, a taxa de transferência na réplica primária não será diretamente afetada pela adição de réplicas secundárias. No entanto, podemos limitar a carga de trabalho de forma agressiva no primário para permitir que o log seja aplicado em réplicas secundárias e servidores de página para acompanhar, para evitar um baixo desempenho de leitura em réplicas secundárias.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Como fazer diagnosticar e solucionar problemas de desempenho em um banco de dados de hiperescala

Para a maioria dos problemas de desempenho, particularmente aqueles que não têm raiz no desempenho do armazenamento, as etapas de diagnóstico e solução de problemas SQL Server comuns são aplicáveis. Para diagnóstico de armazenamento específico de hiperescala, consulte [diagnóstico de solução de problemas de desempenho de hiperescala do SQL](sql-database-hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Perguntas sobre escalabilidade

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Quanto tempo levaria para escalar verticalmente uma réplica de computação

O dimensionamento da computação para cima ou para baixo deve levar de 5-10 minutos, independentemente do tamanho dos dados.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>O meu banco de dados está offline enquanto a operação de expansão/redução está em andamento

Não. A expansão e a redução ficarão online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Devo esperar a conexão suspensa quando as operações de dimensionamento estiverem em andamento

A expansão ou redução dos resultados em conexões existentes são descartadas quando ocorre um failover no final da operação de dimensionamento. A adição de réplicas secundárias não resulta em quedas de conexão.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>É a expansão e redução vertical da operação de réplicas de computação automática ou disparada pelo usuário final

Usuário final. Não automático.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>O tamanho do meu banco de dados de `tempdb` também aumenta à medida que a computação é dimensionada

Sim. O banco de dados `tempdb` será dimensionado automaticamente conforme a computação aumentar.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Posso provisionar várias réplicas de computação primárias, como um sistema de vários mestres, em que vários cabeçotes de computação primários podem impulsionar um nível mais alto de simultaneidade

Não. Somente a réplica de computação primária aceita solicitações de leitura/gravação. Réplicas de computação secundárias só aceitam solicitações somente leitura.

## <a name="read-scale-out-questions"></a>Ler as perguntas de expansão

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Quantas réplicas de computação secundárias posso provisionar

Criamos uma réplica secundária para bancos de dados de hiperescala por padrão. Se você quiser ajustar o número de réplicas, poderá fazer isso usando [portal do Azure](https://portal.azure.com) ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Como fazer conectar-se a essas réplicas de computação secundárias

Você pode se conectar a essas réplicas de computação somente leitura, definindo o argumento `ApplicationIntent` na cadeia de conexão como `ReadOnly`. Todas as conexões marcadas com `ReadOnly` são automaticamente roteadas para uma das réplicas de computação somente leitura adicionais.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Como fazer validar se eu me conectasse com êxito à réplica de computação secundária usando o SSMS ou outras ferramentas de cliente?

Você pode executar a seguinte consulta T-SQL: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`.
O resultado será `READ_ONLY` se você estiver conectado a uma réplica secundária somente leitura e `READ_WRITE` se estiver conectado à réplica primária. Observe que o contexto do banco de dados deve ser definido como o nome do banco de dados de hiperescala, não para o banco de dados `master`.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Posso criar um ponto de extremidade dedicado para uma réplica de expansão de leitura

Não. Você só pode se conectar a réplicas de expansão de leitura especificando `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>O sistema realiza o balanceamento de carga inteligente da carga de trabalho de leitura

Não. Uma nova conexão com a intenção somente leitura é redirecionada para uma réplica de expansão de leitura arbitrária.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Posso escalar/reduzir verticalmente as réplicas de computação secundárias independentemente da réplica primária

Não. A réplica de computação secundária também é usada como destinos de failover de alta disponibilidade, portanto, eles precisam ter a mesma configuração que a primária para fornecer o desempenho esperado após o failover.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Obtenho diferentes `tempdb` dimensionamento para minha computação primária e para minhas réplicas de computação secundárias adicionais

Não. Seu banco de dados `tempdb` está configurado com base no provisionamento de tamanho de computação, suas réplicas de computação secundárias têm o mesmo tamanho que a computação primária.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Posso adicionar índices e exibições em minhas réplicas de computação secundárias

Não. Os bancos de dados de hiperescala têm armazenamento compartilhado, o que significa que todas as réplicas de computação veem as mesmas tabelas, índices e exibições. Se desejar que índices adicionais sejam otimizados para leituras no secundário, você deverá adicioná-los no primário.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Quanto atraso haverá entre as réplicas de computação primárias e secundárias

Latência de dados do momento em que uma transação é confirmada no primário até o momento em que ela é visível em um secundário depende da taxa de geração de log atual. A latência de dados típica é de poucos milissegundos.

## <a name="next-steps"></a>Próximos Passos

Para obter mais informações sobre a camada de serviço de hiperescala, consulte [camada de serviço de hiperescala](sql-database-service-tier-hyperscale.md).
