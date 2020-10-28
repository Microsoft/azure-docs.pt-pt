---
title: FaQ de hiperescala da base de dados Azure SQL
description: Respostas a perguntas comuns que os clientes fazem sobre uma base de dados na Base de Dados SQL no nível de serviço de Hiperescala - vulgarmente chamada de base de dados hyperscale.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 400dd66827e82c1ede496526c49977e6f5383487
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780194"
---
# <a name="azure-sql-database-hyperscale-faq"></a>FaQ de hiperescala da base de dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece respostas a perguntas frequentes para clientes que consideram uma base de dados no nível de serviço de hiperescala de base de dados Azure SQL, referido como apenas Hyperscale no restante desta FAQ. Este artigo descreve os cenários que a Hyperscale suporta e as funcionalidades compatíveis com a Hyperscale.

- Este FAQ destina-se a leitores que tenham uma breve compreensão do nível de serviço de Hiperescala e que estejam à procura de respostas específicas às suas questões e preocupações específicas.
- Este FAQ não é para ser um guia ou responder a perguntas sobre como usar uma base de dados hyperscale. Para uma introdução à Hiperescala, recomendamos que consulte a documentação de [hiperescala da base de dados Azure SQL.](service-tier-hyperscale.md)

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-a-hyperscale-database"></a>O que é uma base de dados de hiperescala

Uma base de dados de hiperescala é uma base de dados na Base de Dados SQL no nível de serviço de hiperescala que é apoiada pela tecnologia de armazenamento de escala de hiperescala. Uma base de dados de hiperescala suporta até 100 TB de dados e fornece alta produção e desempenho, bem como uma escala rápida para se adaptar aos requisitos de carga de trabalho. O dimensionamento é transparente para a aplicação – conectividade, processamento de consultas, etc. funcionam como qualquer outra base de dados na Base de Dados Azure SQL.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Que tipos de recursos e modelos de compras suportam a Hiperescala

O nível de serviço Hyperscale só está disponível para bases de dados individuais utilizando o modelo de compra baseado em vCore na Base de Dados Azure SQL.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Como é que o nível de serviço de Hiperescala difere dos níveis de serviço de Finalidade Geral e De Negócios Crítico

Os níveis de serviço baseados em vCore são diferenciados com base na disponibilidade da base de dados e no tipo de armazenamento, desempenho e tamanho máximo, conforme descrito na tabela seguinte.

| | Tipo de recurso | Fins Gerais |  Hyperscale | Crítico para a Empresa |
|:---:|:---:|:---:|:---:|:---:|
| **Melhor para** |Todos|Oferece opções de cálculo e armazenamento equilibrados orientados para o orçamento.|A maioria das cargas de trabalho. Autoscaling tamanho de armazenamento até 100 TB, escala rápida vertical e horizontal do cálculo, restauração rápida da base de dados.|Aplicações OLTP com alta taxa de transação e baixa latência IO. Oferece maior resiliência a falhas e falhas rápidas usando múltiplas réplicas sincronizadas atualizadas.|
|  **Tipo de recurso** ||SQL Database / SQL Managed Instance | Base de dados individual | SQL Database / SQL Managed Instance |
| **Tamanho do cálculo**|Base de Dados SQL* | 1 a 80 vCores | 1 a 80 vCores* | 1 a 80 vCores |
| **Tamanho do cálculo**|Instância Gerida do SQL | 8, 16, 24, 32, 40, 64, 80 vCores | N/D | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Tipo de armazenamento** | Todos |Armazenamento remoto premium (por exemplo) | Armazenamento desacopdo com cache SSD local (por exemplo) | Armazenamento SSD local super rápido (por exemplo) |
| **Tamanho do armazenamento** | Base de Dados SQL *| 5 GB - 4 TB | Até 100 TB | 5 GB - 4 TB |
| **Tamanho do armazenamento** | Instância Gerida do SQL  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **IOPS** | Base de dados individual | 500 IOPS por vCore com 7000 IOPS máximos | Hyperscale é uma arquitetura multi-camadas com caching em vários níveis. O IOPS eficaz dependerá da carga de trabalho. | 5000 IOPS com 200.000 IOPS máximos|
| **IOPS** | Instância Gerida do SQL | Depende do tamanho do ficheiro | N/D | 1375 IOPS/vCore |
|**Disponibilidade**|Todos|1 réplica, sem escala de leitura, sem cache local | Múltiplas réplicas, até 4 Escala de Leitura, cache local parcial | 3 réplicas, 1 Read Scale-out, zona-redundante HA, armazenamento local completo |
|**Cópias de segurança**|Todos|RA-GRS, retenção de 7 a 35 dias (7 dias por defeito)| RA-GRS, retenção de 7 dias, recuperação constante do tempo no tempo (PITR) | RA-GRS, retenção de 7 a 35 dias (7 dias por defeito) |

\* Piscinas elásticas não são suportadas no nível de serviço hyperscale

### <a name="who-should-use-the-hyperscale-service-tier"></a>Quem deve usar o nível de serviço de Hiperescala

O nível de serviço de Hiperescala destina-se a clientes que tenham grandes bases de dados de SQL Server e queiram modernizar as suas aplicações movendo-se para a nuvem, ou para clientes que já estão a utilizar a Base de Dados SQL do Azure e querem expandir significativamente o potencial de crescimento da base de dados. A hiperescala também se destina a clientes que procuram tanto o alto desempenho como a alta escalabilidade. Com a Hyperscale, obtém-se:

- Tamanho da base de dados até 100 TB
- Backups de bases de dados rápidos independentemente do tamanho da base de dados (as cópias de segurança são baseadas em instantâneos de armazenamento)
- A base de dados rápida restaura independentemente do tamanho da base de dados (as restaurações são de instantâneos de armazenamento)
- Produção de registo mais alta independentemente do tamanho da base de dados e do número de vCores
- Leia Scale-out utilizando uma ou mais réplicas apenas de leitura, usadas para ler descarregamento e como standbys quentes.
- Escala rápida do cálculo, em tempo constante, para ser mais poderoso para acomodar a carga de trabalho pesada e, em seguida, escalar para baixo, em tempo constante. Isto é semelhante a escalar para cima e para baixo entre um P6 e um P11, por exemplo, mas muito mais rápido, uma vez que este não é um tamanho de operação de dados.

### <a name="what-regions-currently-support-hyperscale"></a>Que regiões suportam atualmente a Hiperescala

O nível de serviço de hiperescala está atualmente disponível nas regiões listadas no âmbito da [visão geral de hiperescala da base de dados Azure SQL](service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-server"></a>Posso criar várias bases de dados de hiperescala por servidor

Sim. Para obter mais informações e limites sobre o número de bases de dados de Hiperescala por servidor, consulte [os limites de recursos da Base de Dados SQL para bases de dados individuais e agidadas num servidor](resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Quais são as características de desempenho de uma base de dados de Hiperescala

A arquitetura Hyperscale proporciona um alto desempenho e produção, ao mesmo tempo que suporta grandes tamanhos de base de dados.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Qual é a escalabilidade de uma base de dados de hiperescala

A hiperescala proporciona uma escalabilidade rápida com base na sua procura de carga de trabalho.

- **Escalonamento para cima/para baixo**

  Com a Hyperscale, pode aumentar o tamanho da computação primária em termos de recursos como CPU e memória, e depois reduzir, em tempo constante. Como o armazenamento é partilhado, a escalada e a escalada não é um tamanho de operação de dados.  
- **Escalonamento dentro/fora**

  Com a Hyperscale, você também obtém a capacidade de providenciar uma ou mais réplicas de computação adicionais que você pode usar para servir seus pedidos de leitura. Isto significa que pode usar estas réplicas adicionais de computação como réplicas apenas de leitura para descarregar a carga de trabalho de leitura do cálculo primário. Além de apenas ler, estas réplicas também servem como hot-standbys em caso de uma falha da primária.

  O fornecimento de cada uma destas réplicas de computação adicionais pode ser feito em tempo constante e é uma operação online. Pode ligar-se a estas réplicas adicionais de cálculo apenas de leitura, definindo o `ApplicationIntent` argumento na sua cadeia de ligação para `ReadOnly` . Quaisquer ligações com a intenção da `ReadOnly` aplicação são automaticamente encaminhada para uma das réplicas adicionais de cálculo apenas de leitura.

## <a name="deep-dive-questions"></a>Perguntas de mergulho profundo

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-server"></a>Posso misturar hyperscale e bases de dados individuais num único servidor

Sim, pode.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>A Hyperscale requer que o meu modelo de programação de aplicações mude

Não, o seu modelo de programação de aplicações permanece como está. Utilize a sua cadeia de ligação como de costume e as outras formas regulares de interagir com a sua base de dados Hyperscale.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Qual o nível de isolamento de transações é o padrão numa base de dados de Hiperescala

Na réplica primária, o nível de isolamento de transações padrão é RCSI (Ler Isolamento Instantâneo Comprometido). Nas réplicas secundárias de Escala de Leitura, o nível de isolamento padrão é Snapshot.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Posso levar a minha licença de servidor iaas SQL para Hyperscale

Sim, [o Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) está disponível para a Hyperscale. Cada núcleo standard do servidor SQL pode mapear para 1 hiperescala vCores. Cada núcleo da SQL Server Enterprise pode mapear para 4 vCores de hiperescala. Não precisa de licença SQL para réplicas secundárias. O preço do Benefício Híbrido Azure será automaticamente aplicado às réplicas de Read Scale-out (secundária).

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Que tipo de cargas de trabalho é hyperscale projetado para

A hiperescala suporta todas as cargas de trabalho do SQL Server, mas é otimizada principalmente para o OLTP. Também pode trazer cargas de trabalho híbridas (HTAP) e Analytical (data mart).

### <a name="how-can-i-choose-between-azure-synapse-analytics-and-azure-sql-database-hyperscale"></a>Como posso escolher entre Azure Synapse Analytics e Azure SQL Database Hyperscale

Se está atualmente a executar consultas de análise interativa usando o SQL Server como um armazém de dados, o Hyperscale é uma ótima opção porque pode hospedar pequenos e médios armazéns de dados (como alguns TB até 100 TB) a um custo mais baixo, e pode migrar as cargas de dados do seu armazém de dados SQL Server para Hyperscale com alterações mínimas de código T-SQL.

Se estiver a executar análise de dados em larga escala com consultas complexas e taxas de ingestão sustentadas superiores a 100 MB/s, ou utilizar o Parallel Data Warehouse (PDW), Teradata ou outros armazéns de dados massively Parallel Processing (MPP), a Azure Synapse Analytics (antiga SQL Data Warehouse) pode ser a melhor escolha.
  
## <a name="hyperscale-compute-questions"></a>Questões de computação em hiperescala

### <a name="can-i-pause-my-compute-at-any-time"></a>Posso fazer uma pausa no meu cálculo a qualquer momento

Não neste momento, no entanto, pode escalar o seu cálculo e o número de réplicas para reduzir o custo durante os tempos não-pico.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Posso providenciar uma réplica computacional com RAM extra para a minha carga de trabalho intensiva de memória

Não. Para obter mais RAM, você precisa atualizar para um tamanho de cálculo mais alto. Para obter mais informações, consulte os tamanhos de armazenamento e cálculo de [Hiperescala.](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Posso providenciar réplicas de cálculo múltiplas de diferentes tamanhos

Não.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Quantas réplicas de escala de leitura são suportadas

As bases de dados de Hiperescala são criadas com uma réplica de Escala de Leitura (duas réplicas incluindo primária) por padrão. Pode escalar o número de réplicas apenas de leitura entre 0 e 4 utilizando [o portal Azure](https://portal.azure.com) ou [a API REST](/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Para uma elevada disponibilidade, preciso de providenciar réplicas de computação adicionais

Nas bases de dados de hiperescala, a resiliência dos dados é fornecida ao nível de armazenamento. Só precisa de uma réplica para dar resiliência. Quando a réplica do cálculo está em baixo, uma nova réplica é criada automaticamente sem perda de dados.

No entanto, se houver apenas uma réplica, pode levar algum tempo para construir a cache local na nova réplica após o fracasso. Durante a fase de reconstrução da cache, a base de dados recolhe dados diretamente dos servidores de página, resultando numa maior latência de armazenamento e desempenho de consulta degradado.

Para aplicações críticas à missão que exijam alta disponibilidade com o mínimo impacto de failover, deves providenciar pelo menos 2 réplicas de computação, incluindo a réplica do cálculo primário. Esta é a configuração predefinida. Assim, há uma réplica de prontidão disponível que serve como alvo de falha.

## <a name="data-size-and-storage-questions"></a>Questões sobre o tamanho dos dados e as questões de armazenamento

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Qual é o tamanho máximo da base de dados suportado com Hyperscale

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Qual é o tamanho do registo de transações com Hyperscale

O registo de transações com Hyperscale é praticamente infinito. Não precisa de se preocupar em ficar sem espaço de log num sistema que tem uma produção de log alta. No entanto, a taxa de geração de log pode ser acelerada para a escrita contínua de cargas de trabalho agressivamente. A taxa máxima de geração de log sustentada é de 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Será que a minha `tempdb` escala à medida que a minha base de dados cresce

A sua `tempdb` base de dados está localizada no armazenamento local de SSD e é dimensionada proporcionalmente ao tamanho do cálculo que fornece. O seu `tempdb` está otimizado para proporcionar o máximo de benefícios de desempenho. `tempdb` tamanho não é configurável e é gerido para si.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>O tamanho da minha base de dados cresce automaticamente, ou tenho de gerir o tamanho dos ficheiros de dados

O tamanho da sua base de dados aumenta automaticamente à medida que insere/ingere mais dados.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Qual é o menor tamanho da base de dados que a Hyperscale suporta ou começa com

40 GB. Uma base de dados de hiperescala é criada com um tamanho inicial de 10 GB. Em seguida, começa a crescer 10 GB a cada 10 minutos, até atingir o tamanho de 40 GB. Cada um destes chucks de 10 GB é alocado num servidor de página diferente, de forma a fornecer mais IOPS e um paralelismo de I/O mais elevado. Devido a esta otimização, mesmo que escolha o tamanho inicial da base de dados inferior a 40 GB, a base de dados crescerá para pelo menos 40 GB automaticamente.

### <a name="in-what-increments-does-my-database-size-grow"></a>Em que incrementos o tamanho da minha base de dados cresce

Cada ficheiro de dados cresce 10 GB. Vários ficheiros de dados podem crescer ao mesmo tempo.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>É o armazenamento em Hyperscale local ou remoto

Em Hyperscale, os ficheiros de dados são armazenados no armazenamento padrão Azure. Os dados estão totalmente em cache no armazenamento local de SSD, em servidores de página que estão perto das réplicas do cálculo. Além disso, as réplicas computacional têm caches de dados em SSD local e na memória, para reduzir a frequência de recolher dados de servidores de páginas remotas.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Posso gerir ou definir ficheiros ou grupos de ficheiros com Hyperscale

Não. Os ficheiros de dados são adicionados automaticamente. As razões comuns para a criação de grupos de ficheiros adicionais não se aplicam na arquitetura de armazenamento Hyperscale.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Posso providenciar um limite rígido para o crescimento dos dados para a minha base de dados

Não.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Como são apresentados ficheiros de dados com Hyperscale

Os ficheiros de dados são controlados por servidores de página, com um servidor de página por ficheiro de dados. À medida que o tamanho dos dados aumenta, são adicionados ficheiros de dados e servidores de página associados.

### <a name="is-database-shrink-supported"></a>É a base de dados suportada

Não.

### <a name="is-data-compression-supported"></a>É suportado com compressão de dados

Sim, incluindo compressão de linha, página e loja de colunas.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Se eu tiver uma mesa enorme, os dados da minha tabela são espalhados por vários ficheiros de dados

Sim. As páginas de dados associadas a uma determinada tabela podem acabar em vários ficheiros de dados, que fazem parte do mesmo grupo de ficheiros. O SQL Server utiliza [uma estratégia de preenchimento proporcional](/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) para distribuir dados por ficheiros de dados.

## <a name="data-migration-questions"></a>Questões de migração de dados

### <a name="can-i-move-my-existing-databases-in-azure-sql-database-to-the-hyperscale-service-tier"></a>Posso mover as minhas bases de dados existentes na Base de Dados Azure SQL para o nível de serviço de hiperescala

Sim. Pode mover as bases de dados existentes na Base de Dados Azure SQL para Hyperscale. Esta é uma migração unidirecciona. Não se pode mover bases de dados de Hyperscale para outro nível de serviço. Para provas de conceito (POCs), recomendamos que faça uma cópia da sua base de dados e emigre a cópia para Hyperscale. 

O tempo necessário para mover uma base de dados existente para Hyperscale consiste na hora de copiar dados, e o tempo para reproduzir as alterações es feitas na base de dados de origem enquanto copia dados. O tempo de cópia de dados é proporcional ao tamanho dos dados. O tempo para reproduzir alterações será mais curto se o movimento for feito durante um período de baixa atividade de escrita.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Posso mover as minhas bases de dados de Hiperescala para outros níveis de serviço

Não. Neste momento, não se pode mover uma base de dados hyperscale para outro nível de serviço.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Se perco alguma funcionalidade ou capacidade após a migração para o nível de serviço hyperscale

Sim. Algumas funcionalidades da Base de Dados Azure SQL ainda não são suportadas em Hyperscale, incluindo, mas não se limitando a retenção de backup a longo prazo. Depois de migrar as suas bases de dados para Hyperscale, essas funcionalidades deixam de funcionar.  Esperamos que estas limitações sejam temporárias.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Posso mover a minha base de dados do SQL Server no local ou a minha base de dados do SQL Server numa máquina virtual em nuvem para Hyperscale

Sim. Pode utilizar todas as tecnologias de migração existentes para migrar para a Hiperescala, incluindo a replicação transacional, e quaisquer outras tecnologias de movimento de dados (Bulk Copy, Azure Data Factory, Azure Databricks, SSIS). Consulte também o [Serviço de Migração da Base de Dados Azure,](../../dms/dms-overview.md)que suporta muitos cenários de migração.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Qual é o meu tempo de inatividade durante a migração de um ambiente de máquinas virtuais ou de uma máquina virtual para Hyperscale, e como posso minimizá-lo

O tempo de inatividade para a migração para Hyperscale é o mesmo que o tempo de inatividade quando migra as suas bases de dados para outros níveis de serviço de base de dados Azure SQL. Pode utilizar [a replicação transacional](replication-to-sql-database.md#data-migration-scenario
) para minimizar a migração de tempo de inatividade para bases de dados até poucos tamanhos de Tuberculose. Para bases de dados muito grandes (10+ TB), pode considerar migrar dados usando tecnologias de movimento de dados ADF, Spark ou outras.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Quanto tempo levaria para trazer x quantidade de dados para Hyperscale

A hiperescala é capaz de consumir 100 MB/s de dados novos/alterados, mas o tempo necessário para mover dados para bases de dados na Base de Dados Azure SQL também é afetado pelo rendimento disponível da rede, pela velocidade de leitura da fonte e pelo objetivo do nível de serviço da base de dados-alvo.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-azure-synapse-analytics"></a>Posso ler dados do armazenamento de bolhas e fazer carga rápida (como a Polybase in Azure Synapse Analytics)

Pode ter uma aplicação do cliente a ler dados a partir do Azure Storage e carregar a carga de dados numa base de dados de Hiperescala (tal como pode com qualquer outra base de dados na Base de Dados Azure SQL). A Polybase não é suportada atualmente na Base de Dados Azure SQL. Como alternativa para fornecer carga rápida, pode utilizar [a Azure Data Factory,](../../data-factory/index.yml)ou utilizar uma função spark em [Azure Databricks](/azure/azure-databricks/) com o [conector Spark para SQL](spark-connector.md). O conector Spark para SQL suporta a inserção a granel.

Também é possível ler em massa dados da loja Azure Blob utilizando Azure Blob store usando AZURE Insert ou OPENROWSET: [Exemplos de acesso a granel a dados no armazenamento de blob Azure.](/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)

O modelo simples de recuperação ou de registo a granel não é suportado em Hyperscale. É necessário um modelo de recuperação total para proporcionar elevada disponibilidade e recuperação pontual. No entanto, a arquitetura de registo de hiperescala fornece uma melhor taxa de ingestão de dados em comparação com outros níveis de serviço de base de dados Azure SQL.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>A Hyperscale permite o fornecimento de múltiplos nosdes para ingestão paralela de grandes quantidades de dados

Não. A hiperescala é uma arquitetura multi-processamento simétrica (SMP) e não é um processamento massivamente paralelo (MPP) ou uma arquitetura multi-master. Só é possível criar múltiplas réplicas para escalar cargas de trabalho apenas de leitura.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Qual é a versão mais antiga do SQL Server suportada para migração para Hyperscale

SQL Server 2005. Para obter mais informações, consulte [Migrar para uma única base de dados ou uma base de dados em conjunto.](migrate-to-database-from-sql-server.md#migrate-to-a-single-database-or-a-pooled-database) Para questões de compatibilidade, consulte [a resolução de questões de compatibilidade da migração da base de dados.](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues)

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>A Hyperscale suporta a migração de outras fontes de dados, tais como Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 e outras plataformas de base de dados

Sim. [O Azure Database Migration Service](../../dms/dms-overview.md) suporta muitos cenários de migração.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Questões de continuidade do negócio e recuperação de desastres

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>O que são SLAs são fornecidos para uma base de dados de hiperescala

Consulte [a SLA para a base de dados Azure SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Réplicas adicionais de computação secundária aumentam a disponibilidade, até 99,99% para uma base de dados com duas ou mais réplicas de computação secundária.

### <a name="are-the-database-backups-managed-for-me-by-azure-sql-database"></a>São as cópias de dados geridas por Azure SQL Database

Sim.

### <a name="how-often-are-the-database-backups-taken"></a>Quantas vezes são recolhidas as cópias de segurança da base de dados

Não existem cópias de segurança tradicionais completas, diferenciais e de registo para bases de dados de Hiperescala. Em vez disso, existem instantâneos de armazenamento regular de ficheiros de dados. O log que é gerado é simplesmente mantido como é para o período de retenção configurado, permitindo restaurar qualquer ponto no tempo dentro do período de retenção.

### <a name="does-hyperscale-support-point-in-time-restore"></a>O ponto de suporte da Hyperscale restaura no tempo

Sim.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Qual é o Objetivo do Ponto de Recuperação (RPO)/Objetivo do Tempo de Recuperação (RTO) para a restauração da base de dados em hiperescala

A RPO é 0 min. A maioria das operações de restauro completa em 60 minutos, independentemente do tamanho da base de dados. O tempo de restauração pode ser maior para bases de dados maiores, e se a base de dados tivesse experimentado uma atividade de escrita significativa antes e até ao ponto de restauro no tempo.

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>A cópia de segurança da base de dados afeta o desempenho do cálculo nas minhas réplicas primárias ou secundárias

Não. As cópias de segurança são geridas pelo subsistema de armazenamento e aproveitam as imagens de armazenamento. Não afetam as cargas de trabalho dos utilizadores.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Posso realizar geo-restauro com uma base de dados de Hiperescala

Sim. Geo-restauro é totalmente suportado. Ao contrário da restauração pontual, a geo-restauração requer uma operação de tamanho de dados. Os ficheiros de dados são copiados em paralelo, pelo que a duração desta operação depende principalmente do tamanho do maior ficheiro na base de dados, em vez do tamanho total da base de dados. O tempo de geo-restauração será significativamente mais curto se a base de dados for restaurada na região de Azure que é [emparelhada](../../best-practices-availability-paired-regions.md) com a região da base de dados de origem.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Posso configurar a geo-replicação com base de dados de hiperescala

Neste momento, não.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Posso pegar numa base de dados hyperscale e restaurá-la para o meu servidor no local, ou no SQL Server num VM

Não. O formato de armazenamento para bases de dados hyperscale é diferente de qualquer versão lançada do SQL Server, e você não controla cópias de segurança ou tem acesso a elas. Para retirar os seus dados de uma base de dados de hiperescala, pode extrair dados utilizando quaisquer tecnologias de movimento de dados, ou seja, Azure Databricks, SSIS, etc.

## <a name="cross-feature-questions"></a>Perguntas cruzadas

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Se perco alguma funcionalidade ou capacidade após a migração para o nível de serviço hyperscale

Sim. Algumas funcionalidades da Base de Dados Azure SQL não são suportadas em Hiperescala, incluindo, mas não se limitando a retenção de backup a longo prazo. Depois de migrar as suas bases de dados para Hyperscale, essas funcionalidades deixam de funcionar.

### <a name="will-polybase-work-with-hyperscale"></a>A Polybase funcionará com a Hyperscale

Não. A base de polibase não é suportada na Base de Dados Azure SQL.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>A Hyperscale tem apoio para R e Python

Neste momento, não.

### <a name="are-compute-nodes-containerized"></a>São nódoas de computação contentorizadas

Não. Processos de hiperescala funcionam nos nós [de tecido](https://azure.microsoft.com/services/service-fabric/) de serviço (VMs), não em recipientes.

## <a name="performance-questions"></a>Questões de desempenho

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Quanto produção posso fazer numa base de dados de Hiperescala

A tampa de produção de registo de transações está definida para 100 MB/s para qualquer tamanho de computação hyperscale. A capacidade de atingir esta taxa depende de múltiplos fatores, incluindo, mas não se limitando ao tipo de carga de trabalho, configuração do cliente, e ter capacidade de computação suficiente na réplica do cálculo primário para produzir log a esta taxa.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Quantos IOPS tenho no maior cálculo

A latência do IOPS e da IO variará em função dos padrões de carga de trabalho. Se os dados que estão a ser acedidos estiverem em cache na réplica do cálculo, verá um desempenho de IO semelhante ao do SSD local.

### <a name="does-my-throughput-get-affected-by-backups"></a>Será que a minha produção é afetada por backups

Não. A computação é dissociada da camada de armazenamento. Isto elimina o impacto de desempenho da cópia de segurança.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Será que a minha produção é afetada à medida que eu proviso réplicas adicionais de computação

Como o armazenamento é partilhado e não há replicação física direta entre réplicas de computação primária e secundária, a produção na réplica primária não será diretamente afetada pela adição de réplicas secundárias. No entanto, podemos acelerar continuamente escrevendo carga de trabalho agressivamente na primária para permitir que o registo se aplique em réplicas secundárias e servidores de página para recuperar, para evitar um fraco desempenho de leitura em réplicas secundárias.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Como posso diagnosticar e resolver problemas de desempenho numa base de dados de Hiperescala

Para a maioria dos problemas de desempenho, particularmente os que não estão enraizados no desempenho do armazenamento, aplicam-se os passos comuns de diagnóstico e resolução de problemas do SQL. Para diagnósticos de armazenamento específicos de hiperescala, consulte [diagnósticos de resolução de problemas de desempenho de hiperescala SQL](hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Perguntas sobre escalabilidade

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Quanto tempo levaria para escalar para cima e para baixo uma réplica computacional

A escala do cálculo para cima ou para baixo normalmente leva até 2 minutos, independentemente do tamanho dos dados.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>A minha base de dados está offline enquanto a operação de escala para cima/para baixo está em andamento

Não. A escalada para cima e para baixo estará online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Devo esperar que a ligação caia quando as operações de escala estiverem em andamento

A escalada ou para baixo resulta na queda das ligações existentes quando ocorre uma falha no final da operação de escalonamento. A adição de réplicas secundárias não resulta em quedas de ligação.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>É a escala de réplicas de cálculo operações automáticas ou de utilizador final

Utilizador final. Não é automático.  

### <a name="does-the-size-of-my-tempdb-database-and-rbpex-cache-also-grow-as-the-compute-is-scaled-up"></a>O tamanho da minha base de `tempdb` dados e cache RBPEX também crescem à medida que o cálculo é aumentado

Sim. A `tempdb` base de dados e o tamanho da [cache RBPEX](service-tier-hyperscale.md#distributed-functions-architecture) nos nós computacional aumentarão automaticamente à medida que o número de núcleos é aumentado.

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Posso providenciar múltiplas réplicas primárias de computação, como um sistema multi-mestre, onde várias cabeças de computação primária podem conduzir a um nível mais elevado de concordância

Não. Apenas a réplica do cálculo primário aceita pedidos de leitura/escrita. As réplicas de computação secundária só aceitam pedidos de leitura.

## <a name="read-scale-out-questions"></a>Leia questões de escala

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Quantas réplicas de computação secundária posso providenciar

Criamos uma réplica secundária para bases de dados de hiperescala por defeito. Se quiser ajustar o número de réplicas, pode fazê-lo utilizando [o portal Azure](https://portal.azure.com) ou [a API REST](/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Como posso ligar-me a estas réplicas de computação secundária

Pode ligar-se a estas réplicas adicionais de cálculo apenas de leitura, definindo o `ApplicationIntent` argumento na sua cadeia de ligação para `ReadOnly` . Quaisquer ligações marcadas são `ReadOnly` automaticamente encaminhadas para uma das réplicas adicionais de cálculo apenas de leitura. Para mais detalhes, consulte [réplicas apenas de leitura para descarregar cargas de trabalho de consulta apenas de leitura](read-scale-out.md).

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Como valido se tenho uma ligação com sucesso à réplica de computação secundária utilizando SSMS ou outras ferramentas de cliente?

Pode executar a seguinte consulta T-SQL: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')` .
O resultado é `READ_ONLY` se estiver ligado a uma réplica secundária apenas de leitura, e se estiver ligado à réplica `READ_WRITE` primária. Note que o contexto da base de dados deve ser definido como o nome da base de dados Hyperscale e não para a `master` base de dados.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Posso criar um ponto final dedicado para uma réplica de escala de leitura

Não. Só é possível ligar-se a réplicas de escala de leitura especificando `ApplicationIntent=ReadOnly` .

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>O sistema faz o equilíbrio inteligente da carga de trabalho

Não. Uma nova ligação com a intenção apenas de leitura é redirecionada para uma réplica arbitrária de escala de leitura.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Posso escalar para cima/para baixo as réplicas de computação secundária independentemente da réplica primária

Não. A réplica de computação secundária também é usada como alvos de falha de alta disponibilidade, por isso precisam de ter a mesma configuração que a principal para fornecer o desempenho esperado após o failover.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Recebo `tempdb` tamanhos diferentes para o meu cálculo primário e as minhas réplicas adicionais de computação secundária

Não. A sua `tempdb` base de dados está configurada com base no fornecimento do tamanho do cálculo, as suas réplicas de computação secundária têm o mesmo tamanho que o cálculo primário.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Posso adicionar índices e pontos de vista sobre as minhas réplicas de computação secundária

Não. Bases de dados de hiperescala têm armazenamento compartilhado, o que significa que todas as réplicas computacional vêem as mesmas tabelas, índices e vistas. Se quiser índices adicionais otimizados para leituras secundárias, deve adicioná-los na primária.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Quanto atraso vai haver entre as réplicas de computação primária e secundária

A latência dos dados desde o momento em que uma transação é comprometida no principal até ao momento em que é legível num secundário depende da taxa de geração de registos corrente, do tamanho da transação, da carga na réplica e de outros fatores. A latência típica de dados para pequenas transações encontra-se em dezenas de milissegundos, no entanto não existe limite superior à latência de dados. Os dados de uma determinada réplica secundária são sempre consistentes transacionais. No entanto, num dado momento, a latência dos dados pode ser diferente para diferentes réplicas secundárias. As cargas de trabalho que precisam de ler dados comprometidos imediatamente devem ser executadas na réplica primária.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o nível de serviço Hyperscale, consulte o [nível de serviço Hyperscale](service-tier-hyperscale.md).