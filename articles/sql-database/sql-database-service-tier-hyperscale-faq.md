---
title: FaQ de hiperescala de base de dados Azure SQL
description: Respostas a perguntas comuns que os clientes fazem sobre uma base de dados Azure SQL no nível de serviço de Hiperescala - vulgarmente chamada de base de dados Hyperscale.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 9f518df02b1923513fd014be53646a9a1be8465e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359884"
---
# <a name="azure-sql-database-hyperscale-faq"></a>FaQ de hiperescala de base de dados Azure SQL

Este artigo fornece respostas a perguntas frequentes para os clientes que consideram uma base de dados no nível de serviço de hiperescala de base de dados Azure SQL, referido como apenas Hyperscale no restante desta FAQ. Este artigo descreve os cenários que a Hyperscale suporta e as funcionalidades compatíveis com a Hiperescala.

- Esta FAQ destina-se a leitores que tenham uma breve compreensão do nível de serviço de Hiperescala e que procurem ter as suas perguntas e preocupações específicas respondidas.
- Este FAQ não pretende ser um guia ou responder a perguntas sobre como usar uma base de dados de Hiperescala. Para uma introdução à Hyperscale, recomendamos que consulte a documentação de hiperescala de base de [dados Azure SQL.](sql-database-service-tier-hyperscale.md)

## <a name="general-questions"></a>Perguntas Gerais

### <a name="what-is-a-hyperscale-database"></a>O que é uma base de dados de hiperescala

Uma base de dados hyperscale é uma base de dados Azure SQL no nível de serviço de Hiperescala que é apoiada pela tecnologia de armazenamento de escala hiperescala. Uma base de dados de hiperescala suporta até 100 TB de dados e fornece alta capacidade de entrada e desempenho, bem como uma escala rápida para se adaptar aos requisitos de carga de trabalho. A escala é transparente para a aplicação – conectividade, processamento de consultas, etc. funcionam como qualquer outra base de dados Azure SQL.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Que tipos de recursos e modelos de compra suportam a Hiperescala

O nível de serviço Hyperscale só está disponível para bases de dados únicas utilizando o modelo de compra baseado em vCore na Base de Dados Azure SQL.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Como é que o nível de serviço de hiperescala difere dos níveis de serviço de Propósito Geral e Business Critical

Os níveis de serviço baseados em vCore são diferenciados com base na disponibilidade de base de dados e no tipo de armazenamento, desempenho e tamanho máximo, conforme descrito na tabela seguinte.

| | Tipo de recurso | Fins Gerais |  Hyperscale | Business Critical |
|:---:|:---:|:---:|:---:|:---:|
| **O melhor para** |Todos|Oferece opções de cálculo e armazenamento equilibradas orientadas para o orçamento.|A maioria das cargas de trabalho de negócios. Tamanho de armazenamento de autoscalcificação até 100 TB, escala rápida vertical e horizontal da computação, restauração rápida da base de dados.|Aplicações OLTP com elevada taxa de transação e baixa latência iO. Oferece a maior resiliência a falhas e falhas rápidas usando múltiplas réplicas sincronizadamente atualizadas.|
|  **Tipo de recurso** ||Base de dados única / piscina elástica / instância gerida | Base de dados individual | Base de dados única / piscina elástica / instância gerida |
| **Tamanho da computação**|Base de dados única / piscina elástica * | 1 a 80 vCores | 1 a 80 vCores* | 1 a 80 vCores |
| |Instância gerida | 8, 16, 24, 32, 40, 64, 80 vCores | N/D | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Tipo de armazenamento** | Todos |Armazenamento remoto premium (por exemplo) | Armazenamento dissociado com cache SSD local (por exemplo) | Armazenamento sSD local super rápido (por exemplo) |
| **Tamanho do armazenamento** | Base de dados única / piscina elástica *| 5 GB - 4 TB | Até 100 TB | 5 GB - 4 TB |
| | Instância gerida  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **IOPS** | Base de dados individual | 500 IOPS por vCore com 7000 IOPS máximos | Hyperscale é uma arquitetura multi-nível com cache a vários níveis. IOPS eficaz dependerá da carga de trabalho. | 5000 IOPS com 200.000 IOPS máximos|
| | Instância gerida | Depende do tamanho do ficheiro | N/D | 1375 IOPS/vCore |
|**Disponibilidade**|Todos|1 réplica, sem Leitura Scale-out, sem cache local | Múltiplas réplicas, até 4 Leitura Scale-out, cache local parcial | 3 réplicas, 1 Ler Scale-out, zona-redundante HA, armazenamento local completo |
|**Backups**|Todos|RA-GRS, retenção de 7-35 dias (7 dias por padrão)| RA-GRS, retenção de 7 dias, recuperação constante do tempo ponto no tempo (PITR) | RA-GRS, retenção de 7-35 dias (7 dias por padrão) |

\* piscinas elásticas não são suportadas no nível de serviço de hiperescala

### <a name="who-should-use-the-hyperscale-service-tier"></a>Quem deve usar o nível de serviço de hiperescala

O nível de serviço Hyperscale destina-se a clientes que possuam grandes bases de dados do SQL Server no local e querem modernizar as suas aplicações movendo-se para a nuvem, ou para clientes que já estão a usar a Base de Dados Azure SQL e querem expandir significativamente o potencial para o crescimento da base de dados. A hiperescala também se destina a clientes que procuram alto desempenho e alta escalabilidade. Com a Hiperescala, obtém-se:

- Tamanho da base de dados até 100 TB
- Backups de base de dados rápidos independentemente do tamanho da base de dados (cópias de segurança são baseadas em instantâneos de armazenamento)
- A base de dados rápida restaura independentemente do tamanho da base de dados (os restauros são de instantâneos de armazenamento)
- Maior entrada de log independentemente do tamanho da base de dados e do número de vCores
- Leia Scale-out utilizando uma ou mais réplicas apenas para leitura, utilizadas para ler o descarregamento e como suporte seleto.
- Escala rápida da computação, em tempo constante, para ser mais poderoso para acomodar a carga de trabalho pesada e depois escalar para baixo, em tempo constante. Isto é semelhante ao escalonamento entre um P6 e um P11, por exemplo, mas muito mais rápido, uma vez que este não é um tamanho de operação de dados.

### <a name="what-regions-currently-support-hyperscale"></a>Quais as regiões que atualmente suportam a Hiperescala

O nível de serviço Hyperscale está atualmente disponível nas regiões listadas no âmbito da visão geral de hiperescala de base de [dados Azure SQL](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Posso criar várias bases de dados de Hiperescala por servidor lógico

Sim. Para obter mais informações e limites sobre o número de bases de dados de Hiperescala por servidor lógico, consulte os limites de recursos de base de [dados SQL para bases de dados individuais e agrofadas num servidor lógico](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Quais são as características de desempenho de uma base de dados de hiperescala

A arquitetura Hyperscale fornece alto desempenho e potência, ao mesmo tempo que suporta grandes tamanhos de base de dados. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Qual é a escalabilidade de uma base de dados de hiperescala

A hiperescala proporciona uma escala rápida com base na sua procura de carga de trabalho.

- **Escalando para cima/para baixo**

  Com a Hyperscale, pode aumentar o tamanho da computação primária em termos de recursos como CPU e memória, e depois escalar para baixo, em tempo constante. Como o armazenamento é partilhado, escalar e escalonar não é um tamanho de operação de dados.  
- **Escalando dentro e fora**

  Com a Hyperscale, você também obtém a capacidade de fornecer uma ou mais réplicas de computação adicionais que você pode usar para servir seus pedidos de leitura. Isto significa que pode usar estas réplicas de cálculo adicionais como réplicas apenas de leitura para descarregar a sua carga de trabalho de leitura a partir do cálculo primário. Além de ler apenas, estas réplicas também servem como standbys quentes em caso de uma falha das primárias.

  O fornecimento de cada uma destas réplicas de cálculo adicionais pode ser feito em tempo constante e é uma operação online. Pode ligar-se a estas réplicas de cálculo adicionais apenas para leitura, definindo o argumento `ApplicationIntent` na sua cadeia de ligação a `ReadOnly`. Quaisquer ligações com a intenção de aplicação `ReadOnly` são automaticamente encaminhadas para uma das réplicas de cálculo adicionais.

## <a name="deep-dive-questions"></a>Questões de Mergulho Profundo

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Posso misturar hiperescala e bases de dados individuais num único servidor lógico

Sim, pode.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>A Hiperescala requer que o meu modelo de programação de aplicações mude

Não, o seu modelo de programação de aplicações permanece como está. Usa a corda de ligação como de costume e as outras formas regulares de interagir com a sua base de dados hyperscale.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Que nível de isolamento de transações é o padrão numa base de dados de Hiperescala

Na réplica primária, o nível de isolamento de transações padrão é RCSI (Leia O Isolamento Instantâneo Comprometido). Nas réplicas secundárias de Read Scale-out, o nível de isolamento padrão é Snapshot.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Posso levar a minha licença de servidor IaaS SQL para hyperscale

Sim, [o Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) está disponível para hyperscale. Cada núcleo Padrão do Servidor SQL pode mapear para 1 hiperescala vCores. Cada núcleo da SQL Server Enterprise pode mapear para 4 vCores de hiperescala. Não precisa de licença SQL para réplicas secundárias. O preço do Benefício Híbrido Azure será automaticamente aplicado às réplicas de Leitura Scale-out (secundária).

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Que tipo de cargas de trabalho é hiperescala projetado para

A Hiperscale suporta todas as cargas de trabalho do SQL Server, mas está otimizada principalmente para OLTP. Também pode trazer cargas de trabalho híbridas (HTAP) e Analíticas (data mart).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Como posso escolher entre o Azure SQL Data Warehouse e o Azure SQL Database Hyperscale

Se está atualmente a executar consultas de análise interativa usando o SQL Server como armazém de dados, a Hyperscale é uma ótima opção porque pode alojar pequenos e médios armazéns de dados (como alguns TB até 100 TB) a um custo mais baixo, e pode migrar a sua guerra de dados do SQL Server cargas de trabalho ehouse para Hyperscale com alterações mínimas de código T-SQL.

Se estiver a fazer análises de dados em larga escala com consultas complexas e taxas de ingestão sustentadas superiores a 100 MB/s, ou a utilizar o Parallel Data Warehouse (PDW), a Teradata ou outros armazéns de dados de processamento paralelo massivamente (MPP), o SQL Data Warehouse pode ser a melhor escolha.
  
## <a name="hyperscale-compute-questions"></a>Perguntas computadas de hiperescala

### <a name="can-i-pause-my-compute-at-any-time"></a>Posso parar o meu cálculo a qualquer momento

Não neste momento, no entanto pode escalar o seu cálculo e o número de réplicas para reduzir o custo em tempos não-pico.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Posso fornecer uma réplica computacional com RAM extra para a minha carga de trabalho intensiva de memória

Não. Para obter mais RAM, você precisa atualizar para um tamanho de computação mais alto. Para mais informações, consulte o armazenamento em hiperescala e os [tamanhos da computação.](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Posso fornecer múltiplas réplicas computadas de diferentes tamanhos

Não.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Quantas réplicas de leitura são suportadas

As bases de dados de Hiperescala são criadas com uma réplica de leitura scale-out (duas réplicas incluindo primária) por padrão. Pode escalar o número de réplicas apenas para leitura entre 0 e 4 usando [o portal Azure](https://portal.azure.com) ou [rest API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Para alta disponibilidade, preciso fornecer réplicas de cálculo adicionais

Nas bases de dados de Hiperescala, a resiliência dos dados é fornecida ao nível do armazenamento. Só precisas de uma réplica para fornecer resiliência. Quando a réplica da computação está em baixo, uma nova réplica é criada automaticamente sem perda de dados.

No entanto, se houver apenas uma réplica, pode levar algum tempo para construir a cache local na nova réplica após o fracasso. Durante a fase de reconstrução da cache, a base de dados rebusca dados diretamente dos servidores da página, resultando numa maior latência de armazenamento e desempenho de consulta degradado.

Para aplicações críticas de missão que requerem alta disponibilidade com impacto mínimo de failover, você deve fornecer pelo menos 2 réplicas computacionais, incluindo a réplica do cálculo primário. Esta é a configuração padrão. Assim há uma réplica de espera quente disponível que serve como um alvo de falha.

## <a name="data-size-and-storage-questions"></a>Questões sobre tamanho e armazenamento de dados

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Qual é o tamanho máximo da base de dados suportado com Hiperescala

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Qual é o tamanho do registo de transações com hiperescala

O registo de transações com hiperescala é praticamente infinito. Não precisa de se preocupar em ficar sem espaço de registo num sistema que tenha uma alta entrada de log. No entanto, a taxa de geração de registos pode ser acelerada para cargas de trabalho contínuas e agressivas. A taxa de produção de registo sustentado máximo é de 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Será que a minha escala de `tempdb` à medida que a minha base de dados cresce

A sua base de dados `tempdb` está localizada no armazenamento local de SSD e é dimensionada proporcionalmente ao tamanho da computação que você disponibiliza. O seu `tempdb` está otimizado para proporcionar o máximo de benefícios de desempenho. `tempdb` tamanho não é configurável e é gerido para si.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>O tamanho da minha base de dados cresce automaticamente, ou tenho de gerir o tamanho dos ficheiros de dados

O tamanho da sua base de dados aumenta automaticamente à medida que insere/ingere mais dados.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Qual é o menor tamanho de base de dados que a Hyperscale suporta ou começa com

40 GB. Uma base de dados de hiperescala é criada com um tamanho inicial de 10 GB. Depois, começa a crescer 10 GB a cada 10 minutos, até atingir o tamanho de 40 GB. Cada um destes 10 chucks de 10 GB é atribuído num servidor de página diferente, de forma a fornecer mais IOPS e um maior paralelismo de I/S. Devido a esta otimização, mesmo que escolha o tamanho inicial da base de dados inferior a 40 GB, a base de dados irá crescer para pelo menos 40 GB automaticamente.

### <a name="in-what-increments-does-my-database-size-grow"></a>Em que incrementos o tamanho da minha base de dados cresce

Cada ficheiro de dados cresce 10 GB. Vários ficheiros de dados podem crescer ao mesmo tempo.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>É o armazenamento em Hiperescala local ou remoto

Em Hyperscale, os ficheiros de dados são armazenados no armazenamento padrão do Azure. Os dados estão totalmente emcache no armazenamento local de SSD, em servidores de página que estão perto das réplicas computadas. Além disso, as réplicas computacionais têm caches de dados no SSD local e na memória, para reduzir a frequência de recolha de dados de servidores de páginas remotas.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Posso gerir ou definir ficheiros ou grupos de ficheiros com Hiperescala

Não. Os ficheiros de dados são adicionados automaticamente. As razões comuns para a criação de grupos de ficheiros adicionais não se aplicam na arquitetura de armazenamento de hiperescala.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Posso fornecer um limite máximo no crescimento de dados para a minha base de dados

Não.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Como são os ficheiros de dados definidos com a Hyperscale

Os ficheiros de dados são controlados por servidores de página, com um servidor de página por ficheiro de dados. À medida que o tamanho dos dados aumenta, os ficheiros de dados e os servidores de página associados são adicionados.

### <a name="is-database-shrink-supported"></a>É o psiquiatra da base de dados suportado

Não.

### <a name="is-data-compression-supported"></a>É suportada compressão de dados

Sim, incluindo a compressão de fila, página e loja de colunas.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Se eu tiver uma mesa enorme, os meus dados de tabela são espalhados por vários ficheiros de dados

Sim. As páginas de dados associadas a uma determinada tabela podem acabar em vários ficheiros de dados, que fazem parte do mesmo grupo de ficheiros. O SQL Server utiliza uma estratégia proporcional de [preenchimento](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) para distribuir dados por ficheiros de dados.

## <a name="data-migration-questions"></a>Questões de migração de dados

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Posso mover as minhas bases de dados Azure SQL existentes para o nível de serviço de hiperescala

Sim. Pode mover as bases de dados Azure SQL existentes para Hyperscale. Esta é uma migração de sentido único. Não se pode mover bases de dados de Hiperscale para outro nível de serviço. Para provas de conceito (POCs), recomendamos que faça uma cópia da sua base de dados e emigra a cópia para hyperscale.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Posso mover as minhas bases de dados de Hiperescala para outros níveis de serviço

Não. Neste momento, não se pode mover uma base de dados de Hiperescala para outro nível de serviço.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Perco qualquer funcionalidade ou capacidade após a migração para o nível de serviço de Hiperescala

Sim. Algumas das funcionalidades da Base de Dados Azure SQL ainda não são suportadas em Hiperescala, incluindo, mas não se limitando à retenção de backup a longo prazo. Depois de migraras bases de dados para Hyperscale, essas funcionalidades deixam de funcionar.  Esperamos que estas limitações sejam temporárias.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Posso mover a minha base de dados do SQL Server no local, ou a minha base de dados do SQL Server numa máquina virtual em nuvem para Hyperscale

Sim. Você pode usar todas as tecnologias de migração existentes para migrar para Hyperscale, incluindo replicação transacional, e quaisquer outras tecnologias de movimento de dados (Copy Bulk, Azure Data Factory, Azure Databricks, SSIS). Consulte também o Serviço de Migração de Bases de [Dados Azure,](../dms/dms-overview.md)que apoia muitos cenários de migração.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Qual é o meu tempo de inatividade durante a migração de um ambiente de máquinas no local ou virtual para Hyperscale, e como posso minimizá-lo

O tempo de inatividade para a migração para Hyperscale é o mesmo que o tempo de inatividade quando migra as suas bases de dados para outros níveis de serviço de base de dados Azure SQL. Pode utilizar [a replicação transacional](replication-to-sql-database.md#data-migration-scenario
) para minimizar a migração para bases de dados até poucos TB em tamanho. Para bases de dados muito grandes (10+ TB), pode considerar migrar dados utilizando tecnologias de aDF, Spark ou outras tecnologias de movimento de dados.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Quanto tempo levaria para trazer X quantidade de dados para hyperscale

A Hiperscale é capaz de consumir 100 MB/s de dados novos/alterados, mas o tempo necessário para mover dados para bases de dados Azure SQL também é afetado pela produção disponível da rede, pela velocidade de leitura de fontes e pelo objetivo de nível de serviço de base de dados alvo.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>Posso ler dados do armazenamento de blob e fazer carga rápida (como polybase no SQL Data Warehouse)

Pode ter uma aplicação de cliente a ler dados do Azure Storage e carregar a carga de dados numa base de dados de Hiperescala (tal como pode com qualquer outra base de dados Azure SQL). A Polybase não é atualmente suportada na Base de Dados Azure SQL. Como alternativa para fornecer carga rápida, pode utilizar a [Azure Data Factory,](https://docs.microsoft.com/azure/data-factory/)ou utilizar uma spark em [Tecidos De dados Azure](https://docs.microsoft.com/azure/azure-databricks/) com o [conector Spark para SQL](sql-database-spark-connector.md). O conector Spark para SQL suporta a inserção a granel.

Também é possível ler em massa dados da loja Azure Blob utilizando inserção a granel ou OPENROWSET: [Exemplos de acesso a granel aos dados no armazenamento de Blob Azure](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

O modelo de recuperação simples ou de exploração a granel não é suportado em Hiperescala. O modelo de recuperação completa é necessário para fornecer alta disponibilidade e recuperação ponto-a-tempo. No entanto, a arquitetura de registo de hiperescala fornece uma melhor taxa de ingerir dados em comparação com outros níveis de serviço de base de dados Azure SQL.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>A Hiperescala permite o fornecimento de múltiplos nódosos para ingerir paralelamente grandes quantidades de dados

Não. Hyperscale é uma arquitetura simétrica multi-processamento (SMP) e não é um processamento massivamente paralelo (MPP) ou uma arquitetura multi-master. Só é possível criar várias réplicas para aumentar as cargas de trabalho apenas para leitura.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Qual é a versão mais antiga do SQL Server suportada para a migração para hyperscale

SQL Server 2005. Para mais informações, consulte [Migrate para uma única base de dados ou uma base de dados agrupina](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Para questões de compatibilidade, consulte problemas de compatibilidade com a migração da base de [dados Resolvendo.](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>A Hyperscale suporta a migração de outras fontes de dados como amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 e outras plataformas de base de dados

Sim. [O Azure Database Migration Service](../dms/dms-overview.md) suporta muitos cenários de migração.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Questões de continuidade de negócios e recuperação de desastres

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>O que são fornecidos SLAs para uma base de dados de hiperescala

Consulte o SLA para a Base de [Dados Azure SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Réplicas adicionais de cálculo secundário aumentam a disponibilidade, até 99,99% para uma base de dados com duas ou mais réplicas de cálculo secundário.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>São as cópias de dados geridas para mim pelo serviço de base de dados Azure SQL

Sim.

### <a name="how-often-are-the-database-backups-taken"></a>Quantas vezes são as cópias de dados tomadas

Não existem cópias de segurança tradicionais completas, diferenciais e de registo para bases de dados de hiperescala. Em vez disso, existem instantâneos regulares de armazenamento de ficheiros de dados. O registo gerado é simplesmente mantido como está para o período de retenção configurado, permitindo a restauração a qualquer momento dentro do período de retenção.

### <a name="does-hyperscale-support-point-in-time-restore"></a>O ponto de suporte da Hiperescala no tempo restaura

Sim.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Qual é o Objetivo do Ponto de Recuperação (RPO)/Objetivo de Tempo de Recuperação (RTO) para a restauração da base de dados em Hiperescala

O RPO está a 0 min. O objetivo do RTO é inferior a 10 minutos, independentemente do tamanho da base de dados. 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>A cópia de segurança da base de dados afeta o desempenho da computação nas minhas réplicas primárias ou secundárias

Não. As cópias de segurança são geridas pelo subsistema de armazenamento e alavancam os instantâneos de armazenamento. Não afetam as cargas de trabalho dos utilizadores.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Posso realizar geo-restauro com uma base de dados de Hiperescala

Sim. A geo-restauração é totalmente suportada. Ao contrário da restauração pontual, a geo-restauração requer uma operação de tamanho de dados. Os ficheiros de dados são copiados em paralelo, pelo que a duração desta operação depende principalmente do tamanho do maior ficheiro da base de dados, em vez do tamanho total da base de dados. O tempo de georestauro será significativamente mais curto se a base de dados for restaurada na região de Azure que é [emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) com a região da base de dados de origem.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Posso configurar geo-replicação com base de dados hyperscale

Neste momento, não.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Posso pegar numa cópia de segurança da base de dados hyperscale e restaurá-la no meu servidor no local, ou no SQL Server num VM

Não. O formato de armazenamento para bases de dados hyperscale é diferente de qualquer versão lançada do SQL Server, e não controla cópias de segurança ou tem acesso a elas. Para retirar os seus dados de uma base de dados de Hiperescala, pode extrair dados utilizando quaisquer tecnologias de movimento de dados, ou seja, Azure Data Factory, Azure Databricks, SSIS, etc.

## <a name="cross-feature-questions"></a>Perguntas cruzadas

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Perco qualquer funcionalidade ou capacidade após a migração para o nível de serviço de Hiperescala

Sim. Algumas das funcionalidades da Base de Dados Azure SQL não são suportadas em Hiperescala, incluindo, mas não se limitando à retenção de backup a longo prazo. Depois de migraras bases de dados para Hyperscale, essas funcionalidades deixam de funcionar.

### <a name="will-polybase-work-with-hyperscale"></a>Will Polybase trabalhar com Hyperscale

Não. A Polybase não é suportada na Base de Dados Azure SQL.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>A Hiperescala tem suporte para R e Python

Neste momento, não.

### <a name="are-compute-nodes-containerized"></a>São os nódos os computados contentorizados

Não. Os processos de hiperescala funcionam em nódos os tecidos [de serviço](https://azure.microsoft.com/services/service-fabric/) (VMs), não em recipientes.

## <a name="performance-questions"></a>Questões de Desempenho

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Quanto de escrita posso empurrar numa base de dados de Hiperescala

A tampa de entrada de registo de transação está definida para 100 MB/s para qualquer tamanho de computação de hiperescala. A capacidade de atingir esta taxa depende de múltiplos fatores, incluindo, mas não limitado sem limitação ao tipo de carga de trabalho, configuração do cliente, e ter capacidade computacional suficiente na réplica da computação primária para produzir log a este ritmo.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Quantos IOPS recebo no maior cálculo

A latência IOPS e IO variará dependendo dos padrões de carga de trabalho. Se os dados a serem acedidos estiverem em cache na réplica da computação, verá um desempenho semelhante ao da IO como com o SSD local.

### <a name="does-my-throughput-get-affected-by-backups"></a>Será que a minha entrada é afetada por backups

Não. A computação é dissociada da camada de armazenamento. Isto elimina o impacto de desempenho do backup.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Será que a minha produção é afetada como eu provisão réplicas de computação adicionais

Como o armazenamento é partilhado e não há replicação física direta entre réplicas de computação primária e secundária, a produção na réplica primária não será diretamente afetada pela adição de réplicas secundárias. No entanto, podemos acelerar a continuação da carga de trabalho de escrita agressiva mente nas primárias para permitir que o log se aplique em réplicas secundárias e servidores de página para recuperar, para evitar um mau desempenho de leitura em réplicas secundárias.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Como diagnostico e saquei problemas de desempenho numa base de dados de Hiperescala

Para a maioria dos problemas de desempenho, particularmente os que não estão enraizados no desempenho do armazenamento, aplicam-se os passos comuns de diagnóstico do Servidor SQL e de resolução de problemas. Para diagnósticos de armazenamento específicos da Hiperescala, consulte diagnósticos de resolução de problemas de desempenho da [SQL Hyperscale](sql-database-hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Questões de escalabilidade

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Quanto tempo levaria para escalar para cima e para baixo uma réplica computacional

A computação de escalonamento para cima ou para baixo deve demorar 5-10 minutos, independentemente do tamanho dos dados.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>A minha base de dados está offline enquanto a operação de escala para cima/para baixo está em andamento

Não. A escala para cima e para baixo estará online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Devo esperar que a ligação caia quando as operações de escalação estão em andamento

A escala para cima ou para baixo resulta na queda das ligações existentes quando uma falha ocorre no final da operação de escala. A adição de réplicas secundárias não resulta em quedas de ligação.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>A escala para cima e para baixo das réplicas computacionais é automática ou ativada pelo utilizador final

Utilizador final. Não é automático.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>O tamanho da minha base de dados `tempdb` também cresce à medida que a computação é dimensionada

Sim. A base de dados `tempdb` irá aumentar automaticamente à medida que o cálculo cresce.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Posso fornecer múltiplas réplicas primárias de computação, como um sistema multi-master, onde várias cabeças de computação primária podem conduzir um nível mais alto de conmoeda

Não. Apenas a réplica do cálculo primário aceita pedidos de leitura/escrita. As réplicas de cálculo secundário só aceitam pedidos de leitura.

## <a name="read-scale-out-questions"></a>Ler Perguntas scale-out

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Quantas réplicas de cálculo secundário posso fornecer

Criamos uma réplica secundária para bases de dados de hiperescala por padrão. Se quiser ajustar o número de réplicas, pode fazê-lo utilizando [o portal Azure](https://portal.azure.com) ou a [REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Como me ligo a estas réplicas de computação secundária

Pode ligar-se a estas réplicas de cálculo adicionais apenas para leitura, definindo o argumento `ApplicationIntent` na sua cadeia de ligação a `ReadOnly`. Quaisquer ligações marcadas com `ReadOnly` são automaticamente encaminhadas para uma das réplicas de cálculo adicionais.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Como posso validar se tenho conseguido ligar-me a uma réplica de computação secundária utilizando SSMS ou outras ferramentas de cliente?

Pode executar a seguinte consulta T-SQL: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`.
O resultado é `READ_ONLY` se estiver ligado a uma réplica secundária apenas para leitura, e `READ_WRITE` se estiver ligado à réplica primária. Note que o contexto da base de dados deve ser definido para o nome da base de dados Hyperscale, e não para a base de dados `master`.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Posso criar um ponto final dedicado para uma réplica de leitura scale-out

Não. Só é possível ligar-se a réplicas de "Scale-out", especificando `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Será que o sistema faz equilíbrio de carga inteligente da carga de trabalho de leitura

Não. Uma nova ligação com a intenção de leitura é redirecionada para uma réplica arbitrária de leitura scale-out.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Posso escalar para cima/para baixo as réplicas de computação secundária independentemente da réplica primária

Não. A réplica de computação secundária também é usada como alvos de falha de alta disponibilidade, por isso precisam de ter a mesma configuração que a primária para fornecer desempenho esperado após a falha.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Tenho diferentes `tempdb` tamanho para a minha computação primária e as minhas réplicas adicionais de cálculo secundário

Não. A sua base de dados `tempdb` está configurada com base no fornecimento de tamanho de cálculo, as suas réplicas de cálculo secundário têm o mesmo tamanho que a computação primária.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Posso adicionar índices e pontos de vista sobre as minhas réplicas de cálculo secundário

Não. As bases de dados de hiperescala têm armazenamento partilhado, o que significa que todas as réplicas computacionais vêem as mesmas tabelas, índices e vistas. Se quiser índices adicionais otimizados para leituras secundárias, deve adicioná-los nas primárias.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Quanto atraso vai haver entre as réplicas primárias e secundárias do cálculo

A latência de dados a partir do momento em que uma transação é cometida no primário até ao momento em que é visível numa taxa secundária depende da taxa de produção de registo atual. A latência típica de dados está em milissegundos baixos.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o nível de serviço hyperscale, consulte o [nível de serviço Hyperscale](sql-database-service-tier-hyperscale.md).
