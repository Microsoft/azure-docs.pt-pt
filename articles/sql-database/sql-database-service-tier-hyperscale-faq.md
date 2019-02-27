---
title: Hiperescala de base de dados SQL do Azure FAQ | Documentos da Microsoft
description: Pedir respostas aos clientes de perguntas comuns sobre uma base de dados SQL do Azure na camada de serviços de Hiperescala - comumente chamada de uma base de dados de grande escala.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/06/2019
ms.openlocfilehash: d94bc570f47d71a7e2b626041acd4bb6e84d0dc1
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876012"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>FAQ sobre bases de dados de Hiperescala de SQL do Azure

Este artigo fornece respostas às perguntas mais frequentes para clientes que considerem uma base de dados na camada de serviços de Hiperescala de base de dados do SQL Azure, comumente chamada de uma base de dados de grande escala (atualmente em pré-visualização pública). Este artigo descreve os cenários que oferece suporte a Hiperescala e os serviços para várias funcionalidades são compatíveis com Hiperescala de base de dados SQL em geral.

- Nestas perguntas frequentes destina-se a leitores que tem uma compreensão breve da camada de serviços de Hiperescala e quiser têm suas perguntas específicas e questões respondidas.
- Nestas perguntas frequentes não foi criada para ser um guidebook ou responder a perguntas sobre como utilizar uma base de dados de grande dimensão de base de dados SQL. Para isso, recomendamos que consulte o [Hiperescala de base de dados do SQL Azure](sql-database-service-tier-hyperscale.md) documentação.

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-a-hyperscale-database"></a>O que é uma base de dados de grande escala

Uma base de dados de grande escala é uma base de dados SQL do Azure na camada de serviços de Hiperescala que é sustentada pela tecnologia de armazenamento de escalamento horizontal de Hiperescala. Uma base de dados de Hiperescala suporta até 100 TB de dados e fornece um débito elevado e desempenho, bem como dimensionamento rápido para adaptar os requisitos de carga de trabalho. O dimensionamento é transparente para a aplicação – conectividade, o trabalho de processamento de consultas e assim por diante, como qualquer outro banco de dados SQL.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Quais tipos de recursos e os modelos de compras de Hiperescala de suporte

A camada de serviços de Hiperescala só está disponível para bases de dados individuais, utilizando o modelo de compra baseado em vCore na base de dados do Azure SQL.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>A camada de serviços de Hiperescala difere dos escalões de serviço para fins gerais e crítico para a empresa

O serviço de acesso baseado em vCore escalões principalmente são diferenciados com base na disponibilidade, de tipo de armazenamento e de IOPs.

- A camada de serviços de fins gerais é adequada para a maioria das cargas de trabalho empresariais, oferece um conjunto com balanceamento de opções de computação e armazenamento em que a horas de latência ou de ativação pós-falha de e/s não são a prioridade.
- A camada de serviços de Hiperescala está otimizada para cargas de trabalho de base de dados muito grandes.
- O escalão de serviço crítico para a empresa é adequado para cargas de trabalho empresariais em que a latência de e/s é uma prioridade.

| | Tipo de recurso | Fins Gerais |  Hiperescala | Crítico para a Empresa |
|:---|:---:|:---:|:---:|:---:|:---:|
| **Melhor para** |Todos|  A maioria das cargas de trabalho da empresa. Ofertas de orçamento orientadas equilibradas opções de computação e armazenamento. | Aplicações de dados com os requisitos de capacidade de dados grandes e a capacidade para armazenamento de dimensionamento automático e o dimensionamento de computação com fluidez. | Aplicativos de OLTP com elevada taxa de transações e menor latência e/s. Oferece maior resiliência a falhas usando vários, isolada réplicas.|
|  **Tipo de recurso** ||Base de dados / elastic pool / instância gerida | Base de dados individual | Base de dados / elastic pool / instância gerida |
| **Tamanho de computação**|Base de dados / elástico conjunto * | 1 a 80 vCores | 1 a 80 vCores * | 1 a 80 vCores |
| |Instância gerida | 8, 16, 24, 32, 40, 64, 80 vCores | N/A | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Tipo de armazenamento** | Todos |Armazenamento remoto Premium (por instância) | Armazenamento anular conjugado com a cache SSD local (por instância) | Extremamente rápido (por instância) o armazenamento SSD local |
| **Tamanho de armazenamento** | Base de dados / elastic pool | 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| | Instância gerida  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **Débito de e/s** | Base de dados única * * | 500 IOPS por vCore com IOPS máximos de 7000 | Ainda desconhecido | 5000 IOPS com 200 000 IOPS máximos|
| | Instância gerida | Depende do tamanho do ficheiro | N/A | Instância gerida: Depende do tamanho do ficheiro|
|**Disponibilidade**|Todos|1 réplica, sem cache de escala de leitura, não local | Várias réplicas, até 15 cache local de escala de leitura, parcial | total de 3 réplicas, 1 HA uma escala de leitura, com redundância de zona, local cache |
|**Cópias de segurança**|Todos|RA-GRS, 7-35 dias (7 dias por predefinição)| RA-GRS, 7-35 dias (7 dias por predefinição), o restauro de ponto in0time de tempo constante (PITR) | RA-GRS, 7-35 dias (7 dias por predefinição) |

\* Conjuntos elásticos não suportados na camada de serviços de Hiperescala

### <a name="who-should-use-the-hyperscale-service-tier"></a>Quem deve usar a camada de serviços de Hiperescala

A camada de serviços de Hiperescala foi criada primariamente para os clientes que têm bases de dados do grande no local do SQL Server e quiser modernizar seus aplicativos, movendo para a cloud ou para os clientes que já estão usando o SQL Database do Azure e pretendem expandir significativamente o potencial de crescimento da base de dados. Hiperescala destina-se também para clientes que procuram de alto desempenho e alta escalabilidade. Com a Hiperescala, obtém:

- Suporte para até 100 TB de tamanho de base de dados
- Cópias de segurança de base de dados rápido, independentemente do tamanho de base de dados (as cópias de segurança baseiam-se nos instantâneos de ficheiros)
- Restaura o banco de dados rápido, independentemente do tamanho de base de dados (restauros são de instantâneos de ficheiros)
- Débito mais elevado de log resulta em tempos de consolidação de transação de rápido independentemente do tamanho da base de dados
- Leitura Escalamento horizontal para um ou mais só de leitura nós para descarregar a sua carga de trabalho de leitura e para servidores de reserva frequente.
- Rápido aumento vertical de computação, no tempo constante, para ser mais eficientes para acomodar a carga de trabalho pesada e, em seguida, reduzir verticalmente, no tempo constante. Isso é semelhante ao aumentar e reduzir verticalmente entre um P6 para um P11, por exemplo, mas muito mais rápido, como isso é não um tamanho de operação de dados.

### <a name="what-regions-currently-support-hyperscale"></a>Que regiões atualmente suportam grande escala

Hiperescala está atualmente disponível para bases de dados individuais nas seguintes regiões:  US1 oeste, E.u.a. oeste2, US1 leste, E.U.A. Central, Europa Ocidental, Europa do Norte, Sudeste asiático, leste do Japão, Coreia Central, Austrália Sudeste e leste da Austrália.

### <a name="can-i-create-multiple-hyperscale-databases-per-sql-database-server"></a>Pode criar várias bases de dados de grande dimensão por servidor de base de dados SQL

Sim. Para obter mais informações e limites no número de bases de dados de grande dimensão por servidor de base de dados SQL, consulte [limites de recursos de base de dados SQL para bases de dados únicos e agrupados num servidor de base de dados SQL](sql-database-resource-limits-database-server.md).

### <a name="what-are-the-performance-characteristic-of-a-hyperscale-database"></a>O que são características de desempenho de uma base de dados de grande escala

A arquitetura de Hiperescala de base de dados SQL fornece elevado desempenho e o débito, dar suporte a tamanhos de base de dados grande. As características de perfil de desempenho precisos e não está disponível durante a pré-visualização pública.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>O que é a escalabilidade de um banco de dados de grande escala

Hiperescala de base de dados SQL fornece uma rápida escalabilidade com base na procura sua carga de trabalho.

- **Dimensionamento para cima/para baixo**

  Com a Hiperescala, pode aumentar verticalmente o tamanho de computação principal em termos de recursos, como CPU, memória e, em seguida, reduzir verticalmente, no tempo constante. Como o armazenamento é partilhado, a aumentar e reduzir verticalmente não é um tamanho de operação de dados.  
- **Dimensionamento de entrada/saída**

  Com a Hiperescala, também obtém a capacidade de aprovisionar um ou mais nós de computação adicional que pode utilizar para atender as solicitações de leitura. Isso significa que pode utilizar estes nós de computação adicionais como nós só de leitura para a descarga de sua carga de trabalho de leitura do primária para a computação. Para além só de leitura, que estes nós também servem como acesso frequente-em espera 's em caso de uma ativação pós-falha dos principais.

  Aprovisionamento de cada um desses computação adicional nós podem ser feitas de tempo constante e é uma operação online. Pode ligar a esses nós de computação adicionais de só de leitura, definindo a `ApplicationIntent` argumento na sua cadeia de ligação para `read_only`. Todas as ligações marcado com `read-only` são automaticamente encaminhadas para um de nós de computação adicionais de só de leitura.

## <a name="deep-dive-questions"></a>Perguntas de aprofundamento

### <a name="can-i-mix-hyperscale-and-single-databases-a-my-sql-database-server"></a>Posso combinar Hiperescala e bases de dados individuais um meu servidor de base de dados SQL

Sim, pode.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Hiperescala exige o meu modelo de programação de aplicativo para alterar

Não, o seu modelo de programação de aplicativo permanece como está. Utilize a cadeia de ligação como é habitual e os outros modos de regulares para interagir com a base de dados SQL do Azure.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Os níveis de isolamento de transação que vão ser predefinido na base de dados de grande dimensão de base de dados SQL

No nó principal, o nível de isolamento da transação é RCSI (leitura isolamento de instantâneo dedicada). Em nós de leitura de escala secundária, o nível de isolamento é instantâneo.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Posso usar minha licença de IaaS SQL Server ou no local de Hiperescala de base de dados SQL

Sim, [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) está disponível para Hiperescala. Todos os SQL Server Standard core pode mapear para 1 vCores de Hiperescala. Cada núcleo do SQL Server Enterprise pode mapear para 4 vCores de Hiperescala. Não precisa de uma licença do SQL para as réplicas secundárias. O preço do benefício híbrido do Azure será aplicado automaticamente a uma escala de leitura de réplicas (secundárias).

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Que tipo de cargas de trabalho de grande dimensão de base de dados SQL foi concebido para

Todas as cargas de trabalho do SQL Server oferece suporte a Hiperescala de base de dados SQL, mas principalmente está otimizado para OLTP. Pode dar híbrida e Analytical (datamart) cargas de trabalho.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Como escolher entre o Azure SQL Data Warehouse e de Hiperescala de base de dados SQL

Se estiver atualmente a execução de consultas de análises interativas com o SQL Server como um armazém de dados, Hiperescala de base de dados SQL é uma ótima opção porque pode alojar os armazéns de dados relativamente pequeno (por exemplo, alguns TB até 10's de TB) a um custo menor e pode migrar os dados  Armazém carga de trabalho de grande dimensão de base de dados SQL sem alterações de código do T-SQL.

Se estiver a executar análises de dados em grande escala com consultas complexas e com o armazém de dados paralela (PDW), Teradata ou outros em massa processador paralela (MPP)) armazéns de dados, o SQL Data Warehouse podem ser a melhor opção.
  
## <a name="sql-database-hyperscale-compute-questions"></a>Perguntas de computação de grande dimensão de base de dados SQL

### <a name="can-i-pause-my-compute-at-any-time"></a>Posso Pausar meu computação em qualquer altura

Não.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Pode aprovisionar uma computação com extra RAM para minha carga de trabalho de memória intensiva

Não. Para obter mais RAM, terá de atualizar para um tamanho de computação mais elevado. Hardware de Gen4 fornece mais RAM, em comparação comparado o hardware de Gen5. Para obter mais informações, consulte [tamanhos de armazenamento e computação de Hiperescala](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-preview).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Pode aprovisionar vários nós de computação de tamanhos diferentes

Não.

### <a name="how-many-read-scale-replicas-are-supported"></a>Como muitas réplicas de escala de leitura são suportadas

Em pré-visualização pública, as bases de dados de grande dimensão são criadas com uma réplica de uma escala de leitura (duas réplicas no total) por predefinição. Se pretender adicionar ou remover as réplicas de escala de leitura, submeta um pedido de suporte com o portal do Azure.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Para elevada disponibilidade,, tem de aprovisionar adicionais nós de computação

Bases de dados de grande escala, a elevada disponibilidade é fornecida ao nível do armazenamento. Só precisa de uma réplica para proporcionar elevada disponibilidade. Quando a réplica de computação estiver em baixo, uma nova réplica é criada automaticamente sem perda de dados.

No entanto, se existir apenas uma réplica, poderá demorar algum tempo para criar a cache local na nova réplica após a ativação pós-falha. Durante a fase de reconstrução de cache, a base de dados obtém dados diretamente a partir de servidores de página, resultando em degradação do desempenho IOPS e de consulta.

Para aplicações de missão crítica que exijam a elevada disponibilidade, deve aprovisionar um mínimo de 2 nós de computação, incluindo o nó de computação principal (predefinição). Dessa forma, há uma espera frequente disponível no caso de uma ativação pós-falha.

## <a name="data-size-and-storage-questions"></a>Perguntas de tamanho e o armazenamento de dados

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>O que é o tamanho de db máximo suportado com Hiperescala de base de dados SQL

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>O que é o tamanho do registo de transações com Hiperescala

O registo de transações com Hiperescala é praticamente infinito. Não é necessário se preocupar sobre a ficar sem espaço de registo de, num sistema que tem uma taxa de transferência do registo elevada. No entanto, a taxa de geração de registo poderá ser bloqueada para cargas de trabalho agressivas contínuas. O pico e a média de registo geração taxa ainda não for conhecida (ainda em pré-visualização).

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Minha db temp dimensionar à medida que aumenta a minha base de dados

Sua `tempdb` base de dados está localizado no armazenamento SSD local e está configurado com base no tamanho de computação que for aprovisionado. Sua `tempdb` é otimizado e dispostos a fornecer benefícios de desempenho máximo. O `tempdb` tamanho não é configurável e é gerenciado por si pelo subsistema de armazenamento.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Faz meu tamanho de base de dados automaticamente de aumentar, e é necessário que gerir o tamanho dos ficheiros de dados

O tamanho da base de dados cresce, automaticamente, conforme inserir/ingerir mais dados.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>O que é o tamanho da base de dados mais pequeno que Hiperescala de base de dados SQL suporta ou começa com

5 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>Em incrementos de que meu tamanho de base de dados crescer

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>É o armazenamento em grande escala do SQL da base de dados local ou remoto

Em grande escala, os ficheiros de dados são armazenados no armazenamento standard do Azure. Dados altamente é colocado em cache no armazenamento SSD local, em máquinas perto de nós de computação. Além disso, nós de computação têm um cache no local dentro da memória (conjunto de memória intermédia e assim por diante) e SSD para reduzir a freqüência de obtenção de dados de nós remotos.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Pode gerir ou definir ficheiros ou grupos de ficheiros com Hiperescala

Não
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Pode aprovisionar um limite rígido o crescimento de dados para meu banco de dados

Não

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Como são arquivos de dados apresentados com Hiperescala de base de dados SQL

Os ficheiros de dados são controlados pelos servidores de página. À medida que aumenta o tamanho dos dados, são adicionados os arquivos de dados e nós do servidor de página associada.

### <a name="is-database-shrink-supported"></a>Redução de base de dados é suportada

Não

### <a name="is-database-compression-supported"></a>Compressão de base de dados é suportada

Sim

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Se eu tiver uma grande tabela, meus dados de tabela obter espalhados em vários arquivos de dados

Sim. As páginas de dados associadas a uma determinada tabela podem acabar em vários arquivos de dados, que fazem parte do mesmo grupo de arquivos. O SQL Server usa um [estratégia de preenchimento proporcional](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) para distribuir os dados sobre os arquivos de dados.

## <a name="data-migration-questions"></a>Perguntas de migração de dados

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Posso mover minhas bases de dados SQL do Azure existentes para a camada de serviços de Hiperescala

Sim. Pode mover as bases de dados SQL do Azure existentes para Hiperescala. Em pré-visualização pública, esta é uma migração unidirecional. Não é possível mover bases de dados de Hiperescala para outra camada de serviço. Recomendamos que faça uma cópia dos seus bancos de dados de produção e migrar para o de Hiperescala para uma prova de conceitos (provas de conceito).
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Pode mover meus bancos de dados de Hiperescala para outras edições

Não. Em pré-visualização pública, não é possível mover uma base de dados de Hiperescala para outra camada de serviço.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Perder qualquer funcionalidade ou funcionalidades após a migração para a camada de serviços de Hiperescala

Sim. Alguns dos recursos de base de dados do Azure SQL não são suportadas numa Hiperescala durante a pré-visualização pública, incluindo mas não limitado a TDE e longo prazo retenção cópia de segurança. Depois de migrar as bases de dados de grande escala, essas funcionalidades deixam de funcionar.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Posso mover o meu banco de dados do SQL Server no local ou o meu banco de dados de máquina virtual do SQL Server para Hiperescala

Sim. Pode utilizar todas as tecnologias existentes de migração para migrar a Hiperescala, incluindo BACPAC, replicação transacional, a lógicos de carregamento de dados. Consulte também os [serviço de migração de base de dados do Azure](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>O que é o meu período de indisponibilidade durante a migração no local ou o ambiente de máquina virtual para Hiperescala e como pode minimizar

Tempo de inatividade é o mesmo que o período de indisponibilidade quando migrar as bases de dados para uma base de dados na base de dados do Azure SQL. Pode usar [replicação transacional](replication-to-sql-database.md#data-migration-scenario
) para minimizar a migração de um período de indisponibilidade para bases de dados até alguns TB de tamanho. Para a base de dados muito grande (mais de 10 TB), pode considerar a migração de dados com o ADF, Spark ou outras tecnologias de movimento de dados.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Quanto tempo Custaria demoram a obter X quantidade de dados de grande dimensão de base de dados SQL

Ainda não conhecido (ainda em pré-visualização)

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Posso ler dados a partir do armazenamento de BLOBs e rápida de carga (como o Polybase e o SQL Data Warehouse)

Pode ler os dados do armazenamento do Azure e carregar o carregamento de dados para uma base de dados de grande escala (da mesma forma que pode fazer com uma base de dados regular). O Polybase não é atualmente suportado na base de dados do Azure SQL. Pode fazer usando o Polybase [do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) ou em execução uma tarefa do Spark no [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) com o [conector do Spark para o SQL](sql-database-spark-connector.md). O conector do Spark SQL suporta a inserção em massa.

Recuperação simples ou modelo de registo em massa não é suportado em Hiperescala. Modelo de recuperação completa é necessário para fornecer elevada disponibilidade. No entanto, a Hiperescala fornece uma melhor em comparação comparada uma base de dados devido a nova arquitetura de registo de velocidade de ingestão de dados.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>O grande dimensão de base de dados SQL permite vários nós para ingerir grandes quantidades de dados de aprovisionamento

Não. Hiperescala de base de dados SQL é uma arquitetura SMP e não é um multiprocessamento assimétrico ou de uma arquitetura de vários mestre. Só pode criar várias réplicas para ampliar cargas de trabalho só de leitura.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>O que é mais antiga do SQL Server, versão será a migração de suporte de Hiperescala de base de dados SQL do

SQL Server 2005. Para obter mais informações, consulte [migrar para uma base de dados ou uma base de dados agrupada](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Para problemas de compatibilidade, consulte [solucionando problemas de compatibilidade de migração de base de dados](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>Hiperescala de base de dados SQL suporta a migração a partir de outras origens de dados, como a Aurora, MySQL, Oracle, DB2 e outras plataformas de base de dados

Sim. Provenientes de diferentes origens de dados diferente do SQL Server requer migração lógica. Pode utilizar o [Azure Database Migration Service](../dms/dms-overview.md) para uma migração de lógica.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Perguntas de recuperação de desastre e continuidade do negócio

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>O que são fornecidas SLAS para uma base de dados de Hiperescala

Em geral, não é oferecido qualquer SLA durante a pré-visualização pública. No entanto, a Hiperescala fornece o mesmo nível de elevada disponibilidade com ofertas atuais da BD SQL. Ver [SLA](https://azure.microsoft.com/support/legal/sla/).

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>As cópias de segurança da base de dados geridas para mim pelo serviço de base de dados do Azure SQL

Sim

### <a name="how-often-are-the-database-backups-taken"></a>A frequência com que são executadas as cópias de segurança da base de dados

Não há nenhum completo tradicional, diferenciais e backups de log para bases de dados de grande dimensão de base de dados SQL. Em vez disso, existem instantâneos regulares dos ficheiros de dados e log gerado simplesmente é mantido como está para o período de retenção configurado ou disponível para.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>Suporte de Hiperescala de base de dados SQL ponto anterior no tempo restaurar

Sim

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>O que é o objetivo de ponto de recuperação (RPO) / objetivo de tempo de recuperação (RTO) com cópia de segurança/restauro numa Hiperescala de base de dados SQL

O RPO é mínimo 0. O objetivo RTO é inferior a 10 minutos, independentemente do tamanho da base de dados. No entanto, durante a pré-visualização pública, poderá ter mais tempo de restauro.

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Cópias de segurança de bases de dados grandes afetar o desempenho de computação no meu principal

Não. As cópias de segurança são geridas pelo subsistema de armazenamento e tirar partido dos instantâneos de ficheiros. Eles não afetam a carga de trabalho de utilizador principal.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Pode efetuar o restauro geográfico com uma base de dados de grande dimensão de base de dados SQL

Não, não durante a pré-visualização pública.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Pode configurar a Georreplicação com base de dados de grande dimensão de base de dados SQL

Não, não durante a pré-visualização pública.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Meus nós de computação secundário proceder georreplicado com Hiperescala de base de dados SQL

Não, não durante a pré-visualização pública.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Pode efetuar cópia de segurança uma base de dados de grande dimensão de base de dados SQL e restaurá-lo ao meu servidor no local ou o SQL Server numa VM

Não. O formato de armazenamento para bases de dados de grande escala é diferente do tradicional do SQL Server, e não controlar as cópias de segurança ou ter acesso aos mesmos. Para tirar seus dados fora de uma base de dados de grande dimensão de base de dados SQL, utilize o serviço de exportação ou utilizar o scripting plus BCP.

## <a name="cross-feature-questions"></a>Em várias perguntas de funcionalidade

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Perder qualquer funcionalidade ou funcionalidades após a migração para a camada de serviços de Hiperescala

Sim. Alguns dos recursos de base de dados do Azure SQL não são suportadas numa Hiperescala durante a pré-visualização pública, incluindo mas não limitado a TDE e longo prazo retenção cópia de segurança. Depois de migrar as bases de dados de grande escala, essas funcionalidades deixam de funcionar.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Trabalho de Polybase irá com Hiperescala de base de dados SQL

Não. O Polybase não é suportado na base de dados do Azure SQL.

### <a name="does-the-compute-have-support-for-r-and-python"></a>A computação tem suporte para R e python

Não. R e Python não é suportadas na base de dados do Azure SQL.

### <a name="are-the-compute-nodes-containerized"></a>Os nós de computação em contentores

Não. A base de dados reside numa VM de computação e não um contentor.

## <a name="performance-questions"></a>Perguntas sobre desempenho

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Que débito pode enviar na maior computação de grande dimensão de base de dados SQL

Ainda não conhecido (ainda em pré-visualização)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>O número de IOPS obter na maior computação de grande dimensão de base de dados SQL

Ainda não conhecido (ainda em pré-visualização)

### <a name="does-my-throughput-get-affected-by-backups"></a>Minha taxa de transferência obter afetada pelas cópias de segurança

Não. Computação é dissociada da camada de armazenamento para evitar o impacto de computação.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Minha taxa de transferência obter afetada como posso aprovisionar nós de computação adicionais

Uma vez que o armazenamento é partilhado e não existe nenhuma replicação física direta entre nós de computação primário e secundário, a acontecer tecnicamente, o débito no nó principal será afetado pela adição de nós de escala de leitura. No entanto, pode limitar a carga de trabalho agressiva contínua para permitir início de sessão aplicam-se em nós secundários e servidores de página para acompanhar além de evitar o mau desempenho de leitura em nós secundários.

## <a name="scalability-questions"></a>Perguntas sobre escalabilidade

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Quanto tempo levaria para aumentar e diminuir um nó de computação

Vários minutos

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>É a minha base de dados offline enquanto o dimensionamento para cima ou para baixo a operação estiver em curso

Não. O dimensionamento e reduzir verticalmente será online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Devo esperar drop de ligação quando as operações de dimensionamento estão em curso

Aumentando ou reduzindo resulta em ligações existentes a ser removidas quando acontece de ativação pós-falha para o nó de computação com o tamanho de destino. Adicionar réplicas de leitura não resulta numa quedas de ligação.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>É o aumento vertical e para baixo de nós de computação automática ou o utilizador final acionado a operação

Utilizador final. Não é automática.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>Faz meu `tempb` também aumentar conforme a computação é aumentada verticalmente

Sim. Temp db irá aumentar verticalmente automaticamente à medida que aumenta a computação.  

### <a name="can-i-provision-multiple-primary-computes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Pode aprovisionar várias computações primárias como um sistema de vários mestre onde vários cabeças de computação principal podem promover a um nível mais elevado de simultaneidade

Não. Apenas o nó de computação principal aceita pedidos de leitura/escrita. Nós de computação secundário só aceitam pedidos só de leitura.

## <a name="read-scale-questions"></a>Perguntas de escala de leitura

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Quantos nós de computação secundário pode aprovisionar

Em pré-visualização pública, podemos criar 2 réplicas para bases de dados de grande dimensão por predefinição. Se quiser ajustar o número de réplicas, submeta um pedido de suporte com o portal do Azure.

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Como posso ligar a estes nós de computação secundário

Pode ligar a esses nós de computação adicionais de só de leitura, definindo a `ApplicationIntent` argumento na sua cadeia de ligação para `read_only`. Todas as ligações marcado com `read-only` são automaticamente encaminhadas para um de nós de computação adicionais de só de leitura.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Pode criar um ponto de extremidade dedicado para a réplica de uma escala de leitura

Não. Em pré-visualização pública, só se pode ligar à réplica de uma escala de leitura ao especificar `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>O sistema faz o balanceamento de carga inteligente da carga de trabalho de leitura

Não. Em pré-visualização, a carga de trabalho só de leitura é novamente direcionada para uma réplica de uma escala de leitura aleatória.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Posso Dimensionar para cima ou para baixo de nós de computação secundário, independentemente da computação primário

Não, não durante a pré-visualização pública.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Posso obter tamanho de db temp diferentes para meu computação primária e meus nós de computação secundário adicionais

Não. Sua `tempdb` está configurado com base no tamanho de computação aprovisionamento, durante a pré-visualização pública, os nós de computação secundário são o mesmo tamanho que a computação primário.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Posso adicionar nós de computação de índices e modos de exibição do meu secundário

Não. Bases de dados de Hiperescala tem armazenamento compartilhado, que significa que todos os computação nós ver as mesmas tabelas, índices e vistas. Se pretender que os índices adicionais otimizados para leituras do secundário – tem de adicioná-los na primária em primeiro lugar.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Quanto atraso lá vai ser entre o nó de computação primário e secundário

Desde o momento numa que transação é consolidada na primária, consoante a taxa de geração de log, ele pode ser instantânea ou em milissegundos baixos.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a camada de serviços de Hiperescala, consulte [camada de serviços de Hiperescala (pré-visualização)](sql-database-service-tier-hyperscale.md).
