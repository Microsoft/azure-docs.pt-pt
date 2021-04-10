---
title: 'SQL Server para Azure SQL Database: Visão geral da migração'
description: Saiba mais sobre as ferramentas e opções disponíveis para migrar as bases de dados do SQL Server para a Base de Dados Azure SQL.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f515725ea0f306546039b92d953254a093b15b8b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065179"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Visão geral da migração: SQL Server para Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Saiba mais sobre as opções e considerações para migrar as bases de dados do SQL Server para a Base de Dados Azure SQL. 

Pode migrar bases de dados do SQL Server que executam no local ou em: 

- Sql Server em Azure Virtual Machines.  
- Amazon Web Services (AWS) Elastic Compute Cloud (EC2).
- Serviço de Base de Dados Relacional (RDS).
- Motor compute na Plataforma Google Cloud (GCP).  
- SqL cloud para SQL Server em GCP. 

Para outros guias de migração, consulte [a Migração da Base de Dados.](https://docs.microsoft.com/data-migration) 

## <a name="overview"></a>Descrição Geral

[A Azure SQL Database](../../database/sql-database-paas-overview.md) é uma opção-alvo recomendada para cargas de trabalho do SQL Server que requerem uma plataforma totalmente gerida como um serviço (PaaS). A SQL Database lida com a maioria das funções de gestão de bases de dados. Possui também alta disponibilidade incorporada, processamento inteligente de consultas, escalabilidade e capacidade de desempenho para atender a muitos tipos de aplicação. 

A SQL Database proporciona flexibilidade com vários modelos de [implementação](../../database/sql-database-paas-overview.md#deployment-models) e [níveis](../../database/service-tiers-vcore.md#service-tiers) de serviço que atendem a diferentes tipos de aplicações ou cargas de trabalho.

Um dos principais benefícios da migração para a Base de Dados SQL é que pode modernizar a sua aplicação utilizando as capacidades paaS. Em seguida, pode eliminar qualquer dependência de componentes técnicos que sejam examinados ao nível da instância, tais como trabalhos de Agente SQL.

Também pode economizar custos usando o [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) for SQL Server para migrar as suas licenças de servidor SQL no local para a Base de Dados Azure SQL. Esta opção está disponível se escolher o [modelo de compra baseado em vCore.](../../database/service-tiers-vcore.md)

## <a name="considerations"></a>Considerações 

Os principais fatores a ter em conta quando se avalia as opções de migração são: 

- Número de servidores e bases de dados
- Tamanho das bases de dados
- Tempo de inatividade de negócios aceitável durante o processo de migração 

As opções de migração enumeradas neste guia têm em conta estes fatores. Para a migração lógica de dados para a Base de Dados Azure SQL, o tempo para migrar pode depender tanto do número de objetos numa base de dados como do tamanho da base de dados. 

As ferramentas estão disponíveis para várias cargas de trabalho e preferências do utilizador. Algumas ferramentas podem ser usadas para realizar uma migração rápida de uma única base de dados através de uma ferramenta baseada em UI. Outras ferramentas podem automatizar a migração de várias bases de dados para lidar com migrações em escala. 

## <a name="choose-an-appropriate-target"></a>Escolha um alvo apropriado

Considere as diretrizes gerais para ajudá-lo a escolher o modelo de implementação e o nível de serviço adequados da Base de Dados Azure SQL. Pode escolher recursos de computação e armazenamento durante a implementação e, em seguida, [alterá-los depois, utilizando o portal Azure](../../database/scale-resources.md) sem incorrer em tempo de inatividade para a sua aplicação.

**Modelos de implantação**: Compreenda a carga de trabalho da sua aplicação e o padrão de utilização para decidir entre uma única base de dados ou uma piscina elástica. 

- Uma [única base de dados](../../database/single-database-overview.md) representa uma base de dados totalmente gerida que é adequada para a maioria das aplicações e microserviços modernos.
- Uma [piscina elástica](../../database/elastic-pool-overview.md) é uma coleção de bases de dados individuais com um conjunto partilhado de recursos, como CPU ou memória. É adequado para combinar bases de dados em uma piscina com padrões de uso previsíveis que podem efetivamente compartilhar o mesmo conjunto de recursos.

**Modelos de compra**: Escolha entre o vCore, unidade de transação de bases de dados (DTU) ou modelos de compra sem servidor. 

- O [modelo vCore](../../database/service-tiers-vcore.md) permite-lhe escolher o número de vCores para Azure SQL Database, por isso é a escolha mais fácil quando está a traduzir a partir do SQL Server no local. Esta é a única opção que suporta a poupança de custos de licença com o [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- O [modelo DTU](../../database/service-tiers-dtu.md) abstrata os recursos de computação, memória e E/S subjacentes para fornecer um DTU misturado. 
- O [modelo sem servidor](../../database/serverless-tier-overview.md) destina-se a cargas de trabalho que requerem uma escala automática a pedido com recursos computacional faturados por segundo de utilização. O nível de computação sem servidor interrompe automaticamente as bases de dados durante períodos inativos (onde apenas o armazenamento é faturado). Retoma automaticamente as bases de dados quando a atividade retorna. 

**Níveis de serviço**: Escolha entre três níveis de serviço concebidos para diferentes tipos de aplicações.

- [O nível de serviço geral/standard](../../database/service-tier-general-purpose.md) oferece uma opção equilibrada orientada para o orçamento com computação e armazenamento adequado para entregar aplicações nos escalões médio e inferior. A redundância é incorporada na camada de armazenamento para recuperar de falhas. É projetado para a maioria das cargas de trabalho da base de dados. 
- [Business Critical/premium service tier](../../database/service-tier-business-critical.md) é para aplicações de alto nível que requerem altas taxas de transação, I/O de baixa latência, e um alto nível de resiliência. Réplicas secundárias estão disponíveis para falha e para descarregar cargas de trabalho de leitura.
- [O nível de serviço de hiperescala](../../database/service-tier-hyperscale.md) destina-se a bases de dados que têm volumes de dados em crescimento e precisam de escalar automaticamente até 100 TB no tamanho da base de dados. É projetado para bases de dados muito grandes. 

> [!IMPORTANT]
> [A taxa de registo de transações é regida](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) na Base de Dados Azure SQL para limitar as elevadas taxas de ingestão. Como tal, durante a migração, poderá ter de escalar os recursos de base de dados-alvo (vCores ou DTUs) para aliviar a pressão sobre a CPU ou a produção. Escolha a base de dados-alvo adequadamente dimensionada, mas planeie aumentar os recursos para a migração, se necessário. 


### <a name="sql-server-vm-alternative"></a>Alternativa SQL Server VM

O seu negócio pode ter requisitos que tornem o [SQL Server em Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) um alvo mais adequado do que a Base de Dados Azure SQL. 

Se uma das seguintes condições se aplicar ao seu negócio, considere mudar-se para uma máquina virtual SQL Server (VM) em vez disso: 

- Necessita de acesso direto ao sistema operativo ou sistema de ficheiros, como por exemplo para instalar agentes de terceiros ou personalizados na mesma máquina virtual com o SQL Server. 
- Tem uma dependência estrita de funcionalidades que ainda não são suportadas, tais como FileStream/FileTable, PolyBase e transações de instâncias cruzadas. 
- É necessário manter-se numa versão específica do SQL Server (2012, por exemplo). 
- Os seus requisitos de cálculo são muito inferiores às ofertas de instância gerida (um vCore, por exemplo), e a consolidação da base de dados não é uma opção aceitável. 


## <a name="migration-tools"></a>Ferramentas de migração 

Recomendamos as seguintes ferramentas de migração: 

|Tecnologia | Descrição|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Este serviço Azure ajuda-o a descobrir e avaliar a sua propriedade de dados SQL em escala na VMware. Fornece recomendações de implantação do Azure SQL, dimensionamento de alvos e estimativas mensais. | 
|[Assistente de Migração de Dados](/sql/dma/dma-migrateonpremsqltosqldb)|Esta ferramenta de desktop da Microsoft fornece avaliações perfeitas do SQL Server e migrações de base de dados únicas para Azure SQL Database (ambos esquema e dados). </br></br>A ferramenta pode ser instalada num servidor no local ou na sua máquina local que tenha conectividade com as suas bases de dados de origem. O processo de migração é um movimento lógico de dados entre objetos nas bases de dados de origem e alvo.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)|Este serviço Azure pode migrar bases de dados do SQL Server para Azure SQL Database através do portal Azure ou automaticamente através do PowerShell. O Serviço de Migração de Dados requer que selecione uma rede virtual Azure preferida durante o fornecimento para garantir a conectividade às bases de dados do seu Servidor SQL de origem. Pode migrar bases de dados individuais ou em escala. |
| | |


O quadro que se segue lista os instrumentos alternativos de migração: 

|Tecnologia |Descrição  |
|---------|---------|
|[Replicação transacional](../../database/replication-to-sql-database.md)|Replicar dados das tabelas de base de dados do SQL Server de origem para a Base de Dados Azure SQL, fornecendo uma opção de migração tipo de assinante-editor, mantendo a consistência transacional. As alterações incrementais de dados são propagadas aos assinantes à medida que ocorrem nas editoras.|
|[Serviço de Exportação de Importações/BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) é um ficheiro Windows com uma extensão .bacpac que encapsula o esquema e os dados de uma base de dados. Pode utilizar o BACPAC para exportar dados de uma fonte do SQL Server e importar os dados para a Base de Dados Azure SQL. Um ficheiro BACPAC pode ser importado para uma nova base de dados SQL através do portal Azure. </br></br> Para escala e desempenho com grandes tamanhos de bases de dados ou um grande número de bases de dados, considere usar a ferramenta de linha de comando [SqlPackage](../../database/database-import.md#using-sqlpackage) para exportar e importar bases de dados.|
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|A [ferramenta do programa de cópias a granel (bcp)](/sql/tools/bcp-utility) copia dados de uma instância do SQL Server num ficheiro de dados. Utilize a ferramenta para exportar os dados da sua fonte e importe o ficheiro de dados para a base de dados SQL alvo. </br></br> Para operações de cópia a granel de alta velocidade para mover dados para Azure SQL Database, pode utilizar a [ferramenta Smart Bulk Copy](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) para maximizar a velocidade de transferência, tirando partido de tarefas de cópia paralelas.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|A [atividade copy](../../../data-factory/copy-activity-overview.md) in Azure Data Factory migra dados das bases de dados do SQL Server para a Base de Dados Azure SQL utilizando conectores incorporados e um tempo de [execução de integração](../../../data-factory/concepts-integration-runtime.md).</br> </br> A Data Factory suporta uma vasta gama de [conectores](../../../data-factory/connector-overview.md) para transferir dados de fontes do SQL Server para a Base de Dados Azure SQL.|
|[Sincronização de Dados SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL Data Sync é um serviço construído na Base de Dados Azure SQL que permite sincronizar dados selecionados bidirecionalmente através de várias bases de dados, tanto no local como na nuvem.</br>O Data Sync é útil nos casos em que os dados precisam de ser mantidos atualizados em várias bases de dados na Base de Dados Azure SQL ou no SQL Server.|
| | |

## <a name="compare-migration-options"></a>Comparar opções de migração

Compare as opções de migração para escolher o caminho adequado às necessidades do seu negócio. 

O quadro a seguir compara as opções de migração que recomendamos: 

|Opção de migração  |Quando utilizar  |Considerações  |
|---------|---------|---------|
|[Assistente de Migração de Dados](/sql/dma/dma-migrateonpremsqltosqldb) | - Migrar bases de dados únicas (esquema e dados).  </br> - Pode acomodar tempo de inatividade durante o processo de migração de dados. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 a 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM | - A atividade de migração realiza movimentos de dados entre objetos de base de dados (de origem a alvo), pelo que recomendamos que o execute durante os tempos fora do pico. </br> - O Assistente de Migração de Dados informa o estado da migração por objeto de base de dados, incluindo o número de linhas migradas.  </br> - Para grandes migrações (número de bases de dados ou tamanho da base de dados), utilize o Serviço de Migração da Base de Dados Azure.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)| - Migrar bases de dados únicas ou em escala. </br> - Pode acomodar tempo de inatividade durante o processo de migração. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 a 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM | - As migrações em escala podem ser automatizadas através do [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> - O tempo para completar a migração depende do tamanho da base de dados e do número de objetos na base de dados. </br> - Requer que a base de dados de origem seja definida apenas para leitura. |
| | | |

O quadro a seguir compara as opções alternativas de migração: 

|Método ou tecnologia |Quando utilizar    |Considerações  |
|---------|---------|---------|
|[Replicação transacional](../../database/replication-to-sql-database.md)| - Migrar publicando continuamente alterações das tabelas de bases de dados de origem para as tabelas de bases de dados SQL alvo. </br> - Faça migrações na base de dados completas ou parciais de tabelas selecionadas (subconjunto de uma base de dados).  </br> </br> Fontes apoiadas: </br> - [SQL Server (2016 a 2019) com algumas limitações](/sql/relational-databases/replication/replication-backward-compatibility) </br> - AWS EC2  </br> - GCP Compute SQL Server VM  | - A configuração é relativamente complexa em comparação com outras opções de migração.   </br> - Fornece uma opção de replicação contínua para migrar dados (sem tirar as bases de dados offline).  </br> - A replicação transacional tem limitações a ter em conta quando está a configurar o editor na origem sql Server. Consulte [as limitações na publicação de objetos](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) para saber mais. </br>- É possível monitorizar a [atividade de replicação.](/sql/relational-databases/replication/monitor/monitoring-replication)    |
|[Serviço de Exportação de Importações/BACPAC](../../database/database-import.md)| - Migrar bases de dados individuais de aplicações de linha de negócio. </br>- Adequado para bases de dados mais pequenas.  </br>  - Não necessita de um serviço ou ferramenta de migração separado. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 a 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM  |  - Requer tempo de inatividade porque os dados precisam de ser exportados na fonte e importados no destino.   </br> - Os formatos de ficheiros e os tipos de dados utilizados na exportação ou importação devem ser coerentes com os esquemas de tabelas para evitar erros de truncação ou de incompatibilidade do tipo dados.  </br> - O tempo necessário para exportar uma base de dados com um grande número de objetos pode ser significativamente maior.       |
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - Faça migrações de dados completas ou parciais. </br> - Pode acomodar o tempo de inatividade. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 a 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM   | - Requer tempo de paragem para exportar dados da fonte e importar para o objetivo. </br> - Os formatos de ficheiros e os tipos de dados utilizados na exportação ou importação devem ser coerentes com os esquemas de tabela. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| - Migrar e/ou transformar dados a partir de bases de dados do SQL Server de origem. </br> - A fusão de dados de múltiplas fontes de dados para a Base de Dados Azure SQL é normalmente para cargas de trabalho de inteligência empresarial (BI).  |  - Requer a criação de oleodutos de movimento de dados na Data Factory para mover dados de origem para destino.   </br> - [O custo](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) é uma consideração importante e baseia-se em fatores como os gatilhos do gasoduto, as operações e a duração do movimento de dados. |
|[Sincronização de Dados SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)| - Sincronizar dados entre bases de dados de origem e alvo.</br> - Adequado para executar sincronização contínua entre a Base de Dados Azure SQL e o SqL Server no local num fluxo bidirecional. | - A base de dados Azure SQL deve ser a base de dados do hub para sincronização com uma base de dados do SQL Server no local como base de dados de membros.</br> - Em comparação com a replicação transacional, o SQL Data Sync suporta uma sincronização de dados bidirecional entre as instalações e a Base de Dados Azure SQL. </br> - Pode ter um impacto de desempenho maior, dependendo da carga de trabalho.|
| | | |

## <a name="feature-interoperability"></a>Interoperabilidade do recurso 

Há mais considerações quando está a migrar cargas de trabalho que dependem de outras funcionalidades do SQL Server.

### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Migrar pacotes de Serviços de Integração de Servidores SQL (SSIS) para a Azure, recolocando os pacotes para o tempo de funcionamento Azure-SSIS na [Azure Data Factory](../../../data-factory/introduction.md). A Azure Data Factory apoia a [migração de pacotes SSIS,](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) fornecendo um tempo de execução construído para executar pacotes SSIS em Azure. Em alternativa, pode reescrever a lógica SSIS ETL (extrato, transformação, carga) na Fábrica de Dados Azure utilizando [fluxos de dados](../../../data-factory/concepts-data-flow-overview.md).


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
A Migração de Serviços de Relato de Servidores SQL (SSRS) reporta relatórios paginados no Power BI. Utilize a [Ferramenta de Migração RDL](https://github.com/microsoft/RdlMigration) para ajudar a preparar e migrar os seus relatórios. A Microsoft desenvolveu esta ferramenta para ajudar os clientes a migrar relatórios de Linguagem de Definição de Relatório (RDL) dos seus servidores SSRS para o Power BI. Está disponível no GitHub e inclui um guia ponto a ponto do cenário de migração. 

### <a name="high-availability"></a>Elevada disponibilidade
A configuração manual de funcionalidades de alta disponibilidade do SQL Server, como as instâncias de cluster de sempre em falha e os grupos de disponibilidade Always On, tornam-se obsoletos na base de dados SQL alvo. A arquitetura de alta disponibilidade já está incorporada nos níveis de serviço [de Finalidade Geral (modelo de disponibilidade padrão)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) e [Business Critical (modelo de disponibilidade premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) para base de dados Azure SQL. O nível de serviço Business Critical/premium também fornece escala de leitura que permite ligar-se a um dos nós secundários para fins de leitura. 

Além da arquitetura de alta disponibilidade que está incluída na Base de Dados Azure SQL, a funcionalidade [grupos de falha automática permite-lhe](../../database/auto-failover-group-overview.md) gerir a replicação e falha de bases de dados em um caso gerido para outra região. 

### <a name="sql-agent-jobs"></a>Empregos de agente SQL
Os postos de trabalho do SqL Agent não são suportados diretamente na Base de Dados Azure SQL e precisam de ser utilizados para [trabalhos de base de dados elásticos (pré-visualização)](../../database/job-automation-overview.md).

### <a name="logins-and-groups"></a>Logins e grupos
Mover os logins SQL da fonte do SQL Server para a Base de Dados Azure SQL utilizando o Serviço de Migração de Bases de Dados em modo offline. Utilize o painel **de logins selecionado** no Assistente de Migração para migrar os logins para a base de dados SQL alvo. 

Também pode migrar utilizadores e grupos windows através do Serviço de Migração de Bases de Dados, permitindo o alternância correspondente na página de Configuração do Serviço de **Migração** da Base de Dados. 

Em alternativa, pode utilizar o [utilitário PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) especialmente concebido pelos arquitetos da migração de dados da Microsoft. O utilitário utiliza o PowerShell para criar um script Transact-SQL (T-SQL) para recriar logins e selecionar utilizadores de bases de dados da fonte para o alvo. 

O utilitário PowerShell mapeia automaticamente as contas do Windows Server Ative Directory para as contas do Azure Ative Directory (Azure AD) e pode fazer uma procura UPN para cada login contra a origem do Diretório Ativo. O utilitário scripts personalizado servidor e funções de base de dados, juntamente com a adesão à função e permissões do utilizador. As bases de dados contidas ainda não estão suportadas e apenas um subconjunto de possíveis permissões do SQL Server são scriptados. 

### <a name="system-databases"></a>Bases de dados do sistema
Para a Base de Dados Azure SQL, as únicas bases de dados do sistema aplicáveis são [master](/sql/relational-databases/databases/master-database) e temporário. Para saber mais, consulte [Tempdb na Base de Dados Azure SQL](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="advanced-features"></a>Funcionalidades avançadas 

Certifique-se de aproveitar as funcionalidades avançadas baseadas na nuvem na Base de Dados SQL. Por exemplo, não precisa de se preocupar em gerir backups porque o serviço o faz por si. Pode restaurar a qualquer [ponto no tempo dentro do período de retenção](../../database/recovery-using-backups.md#point-in-time-restore). 

Para reforçar a segurança, considere a utilização da [autenticação AZURE AD,](../../database/authentication-aad-overview.md) [auditoria,](../../database/auditing-overview.md) [deteção de ameaças,](../../database/azure-defender-for-sql.md) [segurança ao nível da linha](/sql/relational-databases/security/row-level-security)e [mascaramento dinâmico de dados.](/sql/relational-databases/security/dynamic-data-masking)

Além de funcionalidades avançadas de gestão e segurança, a SQL Database fornece ferramentas que podem ajudá-lo a [monitorizar e afinar a sua carga de trabalho.](../../database/monitor-tune-overview.md) [O Azure SQL Analytics (Preview)](../../../azure-monitor/insights/azure-sql.md) é uma solução avançada para monitorizar o desempenho de todas as suas bases de dados na Base de Dados Azure SQL em escala e em várias subscrições numa única vista. O Azure SQL Analytics recolhe e visualiza as principais métricas de desempenho com inteligência incorporada para resolução de problemas de desempenho.

[Afinação](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   automática Monitoriza continuamente o desempenho do seu plano de execução SQL e corrige automaticamente problemas de desempenho identificados. 


## <a name="migration-assets"></a>Ativos de migração 

Para obter mais assistência, consulte os seguintes recursos que foram desenvolvidos para projetos de migração no mundo real.

|Recurso  |Description  |
|---------|---------|
|[Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e um nível de remediação de aplicação/base de dados para uma carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda a acelerar as grandes avaliações imobiliárias, fornecendo um processo de decisão automatizado e uniforme para plataformas-alvo.|
|[Utilidade DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Pode utilizar o DBLoader para carregar dados de ficheiros de texto delimitados para o SQL Server. Este utilitário de consola Windows utiliza a interface de carga a granel do cliente nativo do SQL Server. A interface funciona em todas as versões do SQL Server, juntamente com a Base de Dados Azure SQL.|
|[Criação de base de dados a granel com PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Pode utilizar um conjunto de três scripts PowerShell que criam um grupo de recursos (create_rg.ps1), o [servidor lógico em Azure](../../database/logical-servers.md) (create_sqlserver.ps1) e uma base de dados SQL (create_sqldb.ps1). Os scripts incluem capacidades de loop para que possa iterar e criar o maior número de servidores e bases de dados necessários.|
|[Implantação de esquemas a granel com MSSQL-Scripter e PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Este ativo cria um grupo de recursos, cria um ou [vários servidores lógicos em Azure](../../database/logical-servers.md) para hospedar a Base de Dados Azure SQL, exporta todos os esquemas de uma instância do SQL Server (ou de vários exemplos SQL Server 2005+ ) e importa os esquemas para a Base de Dados Azure SQL.|
|[Converter trabalhos de agente de servidor SQL em trabalhos de base de dados elásticas](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Este script migra os trabalhos do seu agente de servidor SQL para trabalhos de base de dados elásticos.|
|[Enviar e-mails da Base de Dados Azure SQL](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Esta solução é uma alternativa à capacidade do SendMail e está disponível para o SQL Server no local. Utiliza funções Azure e o serviço SendGrid para enviar e-mails da Base de Dados Azure SQL.|
|[Utilitário para mover no local logins do SQL Server para Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Um script PowerShell pode criar um script de comando T-SQL para recriar logins e selecionar utilizadores de bases de dados de bases de dados de bases de dados sql server para Azure SQL Database. A ferramenta permite o mapeamento automático das contas do Windows Server Ative Directory para contas Azure AD, juntamente com logins nativos do SQL Server.|
|[Automatização de recolha de dados perfmon usando Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Pode utilizar a ferramenta Logman para recolher dados perfmon (para ajudá-lo a compreender o desempenho da linha de base) e obter recomendações de destino de migração. Esta ferramenta utiliza logman.exe para criar o comando que irá criar, iniciar, parar e eliminar contadores de desempenho definidos numa instância remota do SQL Server.|
|[Migração de bases de dados para Azure SQL Database utilizando BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Este livro branco fornece orientações e passos para ajudar a acelerar as migrações do SQL Server para a Base de Dados Azure SQL utilizando ficheiros BACPAC.|

A equipa de Engenharia Data SQL desenvolveu estes recursos. A principal carta desta equipa é desbloquear e acelerar a modernização complexa dos projetos de migração da plataforma de dados para a plataforma de dados Azure da Microsoft.


## <a name="next-steps"></a>Passos seguintes

- Para começar a migrar as bases de dados do SQL Server para a Base de Dados Azure SQL, consulte o Guia de Migração do [SQL Server para Azure SQL Database](sql-server-to-sql-database-guide.md).

- Para uma matriz de serviços e ferramentas que podem ajudá-lo com cenários de base de dados e migração de dados, bem como tarefas de especialidade, consulte [Serviços e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Base de Dados SQL, consulte:
   - [Visão geral da Base de Dados Azure SQL](../../database/sql-database-paas-overview.md)
   - [Custo Total da Calculadora de Propriedade Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, consulte:
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Best practices for costing and sizing workloads migrated to Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) (Melhores práticas para avaliar custos e dimensionar as cargas de trabalho migradas para o Azure) 

- Para avaliar a camada de acesso à aplicação, consulte o [Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Para obter mais informações sobre como realizar testes A/B para a camada de acesso a dados, consulte o [Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).
