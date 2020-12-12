---
title: 'SQL Server para SQL Database: Visão geral da migração'
description: Conheça as diferentes ferramentas e opções disponíveis para migrar as bases de dados do SQL Server para a Base de Dados Azure SQL.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 2d668775e8d11faa0b2913cebc41e5217b49c278
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357335"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>Visão geral da migração: SQL Server para SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Saiba mais sobre diferentes opções e considerações de migração do seu SQL Server para Azure SQL Database. 

Pode migrar o SQL Server a funcionar no local ou em: 

- SQL Server nas Máquinas Virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de Base de Dados Relacional da Amazon (AWS RDS) 
- Motor compute (Plataforma Google Cloud - GCP)  
- Cloud SQL para SQL Server (Google Cloud Platform – GCP) 

Para outros cenários, consulte o [Guia de Migração da Base de Dados.](https://datamigration.microsoft.com/) 

## <a name="overview"></a>Descrição geral

[A Azure SQL Database](../../database/sql-database-paas-overview.md) é uma opção-alvo recomendada para cargas de trabalho do SQL Server que requerem uma Plataforma totalmente gerida como um Serviço (PaaS). A SQL Database lida com a maioria das funções de gestão de bases de dados, juntamente com alta disponibilidade, processamento inteligente de consultas, escalabilidade e capacidades de desempenho incorporadas para atender a muitos tipos de aplicação diferentes. 

A SQL Database proporciona flexibilidade com vários modelos de [implementação](../../database/sql-database-paas-overview.md#deployment-models) e [níveis](../../database/service-tiers-vcore.md#service-tiers) de serviço que atendem a diferentes tipos de aplicações ou cargas de trabalho.

Um dos principais benefícios da migração para a Base de Dados SQL é que pode modernizar a sua aplicação aproveitando as capacidades do PaaS e eliminar qualquer dependência de componentes técnicos que são telescópios ao nível de instâncias como empregos de Agente SQL.

Também pode economizar no custo migrando as suas licenças de instalação do SQL Server para a Base de Dados Azure SQL utilizando o [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) for SQL Server caso escolha o [modelo de compra baseado em vCore.](../../database/service-tiers-vcore.md)

Este guia tem como objetivo clarificar opções e considerações de migração enquanto se prepara para migrar as bases de dados do SQL Server para a Base de Dados Azure SQL.  

## <a name="considerations"></a>Considerações 

Os factores-chave a ter em conta na avaliação das opções de migração dependem de: 

- Número de servidores e bases de dados
- Tamanho das bases de dados
- Tempo de inatividade de negócios aceitável durante o processo de migração 

As opções de migração enumeradas neste guia têm em conta estes fatores. Para a migração lógica de dados para a Base de Dados Azure SQL, o tempo de migração pode depender tanto do número de objetos numa base de dados como do tamanho da base de dados. 

Estão disponíveis diferentes ferramentas para diferentes cargas de trabalho e preferências do utilizador. Algumas ferramentas podem ser usadas para realizar uma migração rápida de uma única base de dados usando uma ferramenta baseada em UI, enquanto outras ferramentas podem migrar várias bases de dados que podem ser automatizadas para lidar com migrações em escala. 

## <a name="choose-appropriate-target"></a>Escolha o alvo apropriado

Considere as diretrizes gerais para ajudá-lo a escolher o modelo de implementação e o nível de serviço adequados da Base de Dados Azure SQL. Pode escolher recursos de computação e armazenamento durante a implementação e, em seguida, alterá-los depois usando o  [portal Azure](../../database/scale-resources.md)  sem incorrer em tempo de inatividade para a sua aplicação.


**Modelos de implantação**: Compreenda a carga de trabalho da sua aplicação e o padrão de utilização para decidir entre uma única base de dados ou piscina elástica. 

- Uma [única base de dados](../../database/single-database-overview.md) representa uma base de dados totalmente gerida, adequada para a maioria das aplicações e microserviços modernos.
- Uma [piscina elástica](../../database/elastic-pool-overview.md) é uma coleção de bases de dados individuais com um conjunto partilhado de recursos como CPU ou memória e adequado para combinar bases de dados em uma piscina com padrões de uso previsíveis que podem efetivamente compartilhar o mesmo conjunto de recursos.

**Modelos de compra**: Escolha entre o modelo de compra vCore, DTU ou sem servidor. 

- O [modelo vCore](../../database/service-tiers-vcore.md) permite-lhe escolher o número de vCores para a sua Base de Dados Azure SQL, tornando-a a escolha mais fácil ao traduzir a partir do SQL Server no local. Esta é a única opção que suporta a poupança no custo da licença com o [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- O [modelo DTU](../../database/service-tiers-dtu.md) abstrata os recursos subjacentes à computação, memória e IO para fornecer um DTU misturado. 
- O [modelo sem servidor](../../database/serverless-tier-overview.md) destina-se a cargas de trabalho que requerem uma escala automática a pedido com recursos computacional faturados por segundo de utilização. O nível de computação sem servidor interrompe automaticamente as bases de dados durante períodos inativos (onde apenas o armazenamento é faturado) e retoma automaticamente as bases de dados quando a atividade retorna. 

**Níveis de serviço**: Escolha entre três níveis de serviço concebidos para diferentes tipos de aplicações.

- [O nível de serviço Geral / Standard](../../database/service-tier-general-purpose.md) oferece uma opção equilibrada orientada para o orçamento com computação e armazenamento adequado para entregar aplicações de nível médio inferior, com redundância incorporada na camada de armazenamento para recuperar de falhas. Projetado para a maioria das cargas de trabalho da base de dados. 
- [O nível de serviço Business Critical / Premium](../../database/service-tier-business-critical.md) é para aplicações de alto nível que requerem altas taxas de transação, baixa iO de latência, e um alto nível de resiliência com réplicas secundárias disponíveis tanto para failover como para descarregar cargas de trabalho de leitura.
- [O nível de serviço de hiperescala](../../database/service-tier-hyperscale.md) destina-se a bases de dados que têm volumes de dados em crescimento e precisam de aumentar automaticamente até 100-TB do tamanho da base de dados. Projetado para bases de dados muito grandes. 

> [!IMPORTANT]
> [A taxa de registo de transações é regida](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) na Base de Dados Azure SQL para limitar as elevadas taxas de ingestão. Como tal, durante a migração, pode ser necessário escalar os recursos de base de dados-alvo (vCores/DTUs) para aliviar a pressão sobre a CPU ou a produção. Escolha a base de dados-alvo de tamanho adequado, mas planeie aumentar os recursos para a migração, se necessário. 


### <a name="sql-server-on-azure-vm-alternative"></a>SqL Server na alternativa Azure VM

O seu negócio pode ter requisitos que tornem o [SQL Server em Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) um alvo mais adequado do que a Base de Dados Azure SQL. 

Se o seguinte se aplicar ao seu negócio, considere mudar-se para um SQL Server em Azure VM: 

- Se necessitar de acesso direto ao sistema operativo ou sistema de ficheiros, como por exemplo instalar agentes de terceiros ou personalizados na mesma máquina virtual com o SQL Server. 
- Se tiver uma dependência estrita de funcionalidades que ainda não sejam suportadas, como FileStream/FileTable, PolyBase e transações de instâncias cruzadas. 
- Se precisar de ficar numa versão específica do SQL Server (2012, por exemplo). 
- Se os seus requisitos de cálculo forem muito inferiores às ofertas de instância geridas (um vCore, por exemplo), e a consolidação da base de dados não é uma opção aceitável. 


## <a name="migration-tools"></a>Ferramentas de migração 

As ferramentas recomendadas para a migração são o Assistente de Migração de Dados e o Serviço de Migração da Base de Dados Azure. Existem outras opções alternativas de migração disponíveis também. 

### <a name="recommended-tools"></a>Ferramentas recomendadas

O quadro que se segue lista as ferramentas de migração recomendadas: 

|Tecnologia | Descrição|
|---------|---------|
|[Assistente de Migração de Dados (DMA)](/sql/dma/dma-migrateonpremsqltosqldb)|O Data Migration Assistant é uma ferramenta de ambiente de trabalho que fornece avaliações perfeitas do SQL Server e migrações para a Base de Dados Azure SQL (ambos esquemas e dados). A ferramenta pode ser instalada num servidor no local ou na sua máquina local que tenha conectividade com as suas bases de dados de origem. O processo de migração é um movimento lógico de dados entre objetos na base de dados de origem e alvo. </br> - Migrar bases de dados únicas (esquema e dados)|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)|Um serviço Azure de primeira parte que pode migrar as suas bases de dados SQL Server para Azure SQL Database utilizando o portal Azure ou automatizado com o PowerShell. O Azure DMS requer que selecione uma Rede Virtual Azure (VNet) preferida durante o fornecimento para garantir que existe conectividade com as bases de dados do seu Servidor SQL de origem. </br> - Migrar bases de dados únicas ou em escala. |
| | |


### <a name="alternative-tools"></a>Ferramentas alternativas

O quadro que se segue lista os instrumentos alternativos de migração: 

|Tecnologia |Descrição  |
|---------|---------|
|[Replicação transacional](../../database/replication-to-sql-database.md)|Replicar dados da tabela de base de dados do SQL Server de origem para a Base de Dados SQL, fornecendo uma opção de migração tipo de assinante-editor, mantendo a consistência transacional. As alterações incrementais de dados são propagadas aos Assinantes à medida que ocorrem nos Editores.|
|[Serviço de Exportação de Importações / BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) é um ficheiro Windows com uma `.bacpac` extensão que encapsula o esquema e os dados de uma base de dados. O BACPAC pode ser utilizado tanto para exportar dados de um servidor SQL de origem como para importar os dados para a Base de Dados Azure SQL. O ficheiro BACPAC pode ser importado para uma nova Base de Dados Azure SQL utilizando o portal Azure. </br></br> Para escala e desempenho com grandes tamanhos de bases de dados ou um grande número de bases de dados, deve considerar a utilização da linha de comando [SqlPackage](../../database/database-import.md#using-sqlpackage) para exportar e importar bases de dados.|
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|O [programa de cópias em massa (bcp) copia](/sql/tools/bcp-utility) dados de uma instância do SQL Server num ficheiro de dados. Utilize o utilitário BCP para exportar os dados da sua fonte e importe o ficheiro de dados para a Base de Dados SQL alvo. </br></br> Para operações de cópia a granel de alta velocidade para mover dados para Azure SQL Database, a [ferramenta Smart Bulk Copy](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) pode ser utilizada para maximizar a velocidade de transferência, aproveitando tarefas paralelas de cópia.|
|[Fábrica de Dados Azure (ADF)](../../../data-factory/connector-azure-sql-database.md)|A [atividade copy](../../../data-factory/copy-activity-overview.md) in Azure Data Factory migra dados da base de dados(s) do SQL Server para a BASE de Dados SQL utilizando conectores incorporados e uma taxa de [execução de integração](../../../data-factory/concepts-integration-runtime.md).</br> </br> A ADF suporta uma vasta gama de [conectores](../../../data-factory/connector-overview.md) para mover dados de fontes do SQL Server para a Base de Dados SQL.|
|[Sincronização de Dados SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL Data Sync é um serviço construído na Base de Dados Azure SQL que permite sincronizar os dados que seleciona bidireccionalmente através de várias bases de dados, tanto no local como na nuvem.</br>O Data Sync é útil nos casos em que os dados precisam de ser mantidos atualizados em várias bases de dados na Base de Dados Azure SQL ou no SQL Server.|
| | |

## <a name="compare-migration-options"></a>Comparar opções de migração

Compare as opções de migração para escolher o caminho adequado às necessidades do seu negócio. 

### <a name="recommended-options"></a>Opções recomendadas

O quadro a seguir compara as opções de migração recomendadas: 

|Opção de migração  |Quando utilizar  |Considerações  |
|---------|---------|---------|
|[Assistente de Migração de Dados (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) | - Migrar bases de dados únicas (esquema e dados).  </br> - Pode acomodar tempo de inatividade durante o processo de migração de dados. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 - 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM | - A atividade de migração realiza movimentos de dados entre objetos de base de dados (de origem para alvo) e, portanto, recomendado para executar durante os tempos fora do pico. </br> - A DMA informa o estado da migração por objeto de base de dados, incluindo o número de linhas migradas.  </br> - Para grandes migrações (número de bases de dados/tamanho da base de dados), utilize o Serviço de Migração da Base de Dados Azure listado abaixo.|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)| - Migrar bases de dados únicas ou em escala. </br> - Pode acomodar tempo de inatividade durante o processo de migração. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 - 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM | - As migrações em escala podem ser automatizadas através do [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> - O tempo para completar a migração depende do tamanho da base de dados e do número de objetos na base de dados. </br> - Requer que a base de dados de origem seja definida como Read-Only. |
| | | |

### <a name="alternative-options"></a>Opções alternativas

O quadro a seguir compara as opções alternativas de migração: 

|Método / tecnologia |Quando utilizar    |Considerações  |
|---------|---------|---------|
|[Replicação transacional](../../database/replication-to-sql-database.md)| - Migrar publicando continuamente alterações das tabelas de bases de dados de origem para as tabelas de bases de dados SQL alvo. </br> - Migrações completas ou parciais de bases de dados de tabelas selecionadas (subconjunto de base de dados).  </br> </br> Fontes apoiadas: </br> - [SQL Server (2016 - 2019) com algumas limitações](/sql/relational-databases/replication/replication-backward-compatibility) </br> - AWS EC2  </br> - GCP Compute SQL Server VM  | - A configuração é relativamente complexa em comparação com outras opções de migração.   </br> - Fornece uma opção de replicação contínua para migrar dados (sem tirar as bases de dados offline).  </br> - A replicação transacional tem uma série de limitações a ter em conta na configuração do Editor no Servidor SQL de origem. Consulte [as limitações dos objetos de publicação](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) para saber mais. </br>- É possível monitorizar a [atividade de replicação.](/sql/relational-databases/replication/monitor/monitoring-replication)    |
|[Serviço de Exportação de Importações / BACPAC](../../database/database-import.md)| - Migrar bases de dados individuais de aplicações em linha de negócio. </br>- Adequado para bases de dados mais pequenas.  </br>  - Não necessita de um serviço ou ferramenta de migração separado. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 - 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM  |  - Requer tempo de inatividade, uma vez que os dados devem ser exportados na fonte e importados no destino.   </br> - Os formatos de ficheiros e os tipos de dados utilizados na exportação/importação devem ser consistentes com esquemas de tabela para evitar erros de incompatibilidade entre truncações/tipos de dados.  </br> - O tempo necessário para exportar uma base de dados com um grande número de objetos pode ser significativamente maior.       |
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - Migrar migrações de dados completos ou parciais. </br> - Pode acomodar o tempo de inatividade. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 - 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM   | - Requer tempo de inatividade para exportar dados provenientes da fonte e importar para o alvo. </br> - Os formatos de ficheiros e os tipos de dados utilizados na exportação/importação devem ser consistentes com os esquemas de tabela. |
|[Fábrica de Dados Azure (ADF)](../../../data-factory/connector-azure-sql-database.md)| - Migrar e/ou transformar dados a partir de bases de dados do SQL Server de origem. </br> - Fusão de dados de múltiplas fontes de dados para a Base de Dados Azure SQL normalmente para cargas de trabalho de Business Intelligence (BI).  |  - Requer a criação de oleodutos de movimento de dados na ADF para mover dados de origem para destino.   </br> - [O custo](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) é uma consideração importante e baseia-se nos gatilhos do gasoduto, nas operações, na duração do movimento dos dados, etc. |
|[Sincronização de Dados SQL](../../database/sql-data-sync-data-sql-server-sql-database.md)| - Sincronizar dados entre bases de dados de origem e alvo.</br> - Adequado para executar sincronização contínua entre a Base de Dados Azure SQL e o SqL Server no local num fluxo biducional. | - A base de dados Azure SQL deve ser a base de dados hub para sincronização com base de dados do Servidor SQL on-prem como base de dados de membros.</br> - Em comparação com a replicação transacional, o SQL Data Sync suporta uma sincronização de dados bidis entre as instalações e a Base de Dados Azure SQL. </br> - Pode ter um impacto de desempenho mais elevado dependendo da carga de trabalho.|
| | | |

## <a name="feature-interoperability"></a>Interoperabilidade do recurso 

Existem considerações adicionais ao migrar cargas de trabalho que dependem de outras funcionalidades do SQL Server.

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Migrar pacotes de Serviços de Integração de Servidores SQL (SSIS) para a Azure, recolocando os pacotes para o tempo de funcionamento do Azure-SSIS na [Azure Data Factory](../../../data-factory/introduction.md). A Azure Data Factory apoia a [migração de pacotes SSIS,](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) fornecendo um tempo de execução construído para executar pacotes SSIS em Azure. Em alternativa, também pode reescrever a lógica SSIS ETL de forma nativa em ADF utilizando [dataflows](../../../data-factory/concepts-data-flow-overview.md).


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
A Migração de Serviços de Relato de Servidores SQL (SSRS) reporta relatórios paginados no Power BI. Utilize a [Ferramenta de Migração RDL](https://github.com/microsoft/RdlMigration) para ajudar a preparar e migrar os seus relatórios. Essa ferramenta foi desenvolvida pela Microsoft para ajudar os clientes a migrar relatórios RDL dos seus servidores do SSRS para o Power BI. Está disponível no GitHub, e documenta uma passagem de ponta a ponta do cenário de migração. 

#### <a name="high-availability"></a>Elevada disponibilidade
A configuração manual de funcionalidades de alta disponibilidade do SQL Server, como as instâncias de cluster de sempre em falha e os grupos de disponibilidade always on, tornam-se obsoletos na base de dados Azure SQL alvo, uma vez que a arquitetura de alta disponibilidade já está incorporada tanto na Base de Dados SQL de [Finalidade Geral (modelo de disponibilidade padrão)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) como na Base de Dados SQL SQL [do Business Critical (modelo de disponibilidade premium).](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) O Business Critical / Premium Service Tier também fornece escala de leitura que permite ligar-se a um dos nós secundários para fins de leitura. 

Para além da arquitetura de alta disponibilidade que está incluída na Base de Dados SQL, existe também a funcionalidade [de grupos de falha automática](../../database/auto-failover-group-overview.md) que permite gerir a replicação e falha de bases de dados em um caso gerido para outra região. 

#### <a name="sql-agent-jobs"></a>Empregos de agente SQL
Os trabalhos do Agente SQL não são suportados diretamente na Base de Dados Azure SQL e terão de ser implantados em [Trabalhos de Base de Dados Elásticas (Pré-visualização)](../../database/job-automation-overview.md#elastic-database-jobs-preview).

#### <a name="logins-and-groups"></a>Logins e grupos
Mover os logins SQL do servidor SQL de origem para a base de dados Azure SQL utilizando o Serviço de Migração de Bases de Dados (DMS) em modo offline.  Utilize a lâmina **de login selecionada** no Assistente de **Migração** para migrar os logins para a base de dados SQL alvo. 

Os utilizadores e grupos windows também podem ser migrados usando DMS, permitindo o botão de toggle correspondente na página de Configuração DMS. 

Em alternativa, pode utilizar a [ferramenta de utilitário PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) especialmente concebida pelos Microsoft Data Migration Architects. O utilitário utiliza o PowerShell para criar um script Transact-SQL (T-SQL) para recriar logins e selecionar utilizadores de bases de dados da fonte para o alvo. A ferramenta mapeia automaticamente as contas AD do Windows para as contas AD do Azure e pode fazer uma procura UPN para cada login contra a fonte Ative Directory. A ferramenta scripts personalizado servidor e funções de base de dados, bem como membro de função, função de base de dados e permissões do utilizador. As bases de dados contidas ainda não estão suportadas e apenas um subconjunto de possíveis permissões do SQL Server são scriptados. 


#### <a name="system-databases"></a>Bases de dados do sistema
Para a Base de Dados Azure SQL, as únicas bases de dados do sistema aplicáveis são [master](/sql/relational-databases/databases/master-database) e temporário. Para saber mais, consulte [Tempdb na Base de Dados Azure SQL](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="leverage-advanced-features"></a>Alavancar recursos avançados 

Certifique-se de aproveitar as funcionalidades avançadas baseadas na nuvem oferecidas pela SQL Database. Por exemplo, já não precisa de se preocupar em gerir backups, pois o serviço o faz por si. Pode restaurar a qualquer [ponto no tempo dentro do período de retenção](../../database/recovery-using-backups.md#point-in-time-restore). 

Para reforçar a segurança, considere a utilização da [Autenticação do Diretório Ativo Azure,](../../database/authentication-aad-overview.md) [a auditoria, a](../../database/auditing-overview.md) [deteção de ameaças,](../../database/azure-defender-for-sql.md) [a segurança ao nível da linha](/sql/relational-databases/security/row-level-security)e a máscara dinâmica de [dados.](/sql/relational-databases/security/dynamic-data-masking)

Além de funcionalidades avançadas de gestão e segurança, a SQL Database fornece um conjunto de ferramentas avançadas que podem ajudá-lo a [monitorizar e afinar a sua carga de trabalho.](../../database/monitor-tune-overview.md) [A Azure SQL Analytics (Preview)](../../../azure-monitor/insights/azure-sql.md) é uma solução avançada de monitorização da nuvem para monitorizar o desempenho de todas as suas bases de dados na Base de Dados Azure SQL em escala e em várias subscrições numa única vista. O Azure SQL Analytics recolhe e visualiza as principais métricas de desempenho com inteligência incorporada para resolução de problemas de desempenho.

[Afinação](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   automática Monitoriza continuamente o desempenho das estatísticas do seu plano de execução SQL e corrige automaticamente problemas de desempenho identificados. 


## <a name="migration-assets"></a>Ativos de migração 

Para assistência adicional, consulte os seguintes recursos que foram desenvolvidos para projetos de migração no mundo real.

|Recurso  |Descrição  |
|---------|---------|
|[Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e nível de remediação de aplicações/bases de dados para uma determinada carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda a acelerar as grandes avaliações imobiliárias, fornecendo um processo de decisão de plataforma-alvo automatizado e uniforme.|
|[Utilidade DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|O DBLoader pode ser usado para carregar dados de ficheiros de texto delimitados para o SQL Server. Este utilitário de consola Windows utiliza a interface de volume de pessoal do cliente nativo do SQL Server, que funciona em todas as versões do SQL Server, incluindo a Base de Dados Azure SQL.|
|[Criação de base de dados a granel com PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Isto inclui um conjunto de três scripts PowerShell que criam um grupo de recursos (create_rg.ps1), o [servidor lógico em Azure](../../database/logical-servers.md) (create_sqlserver.ps1) e Azure SQL Database (create_sqldb.ps1). Os scripts incluem capacidades de loop para que possa iterar e criar o maior número de servidores e bases de dados necessários.|
|[Implantação de esquemas a granel com MSSQL-Scripter & PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Este ativo cria um grupo de recursos, um ou múltiplos [servidores lógicos em Azure](../../database/logical-servers.md) para hospedar a Base de Dados Azure SQL, exporta todos os esquemas de um SqL Server (ou múltiplos SQL Servers (2005+) e importa-o para a Base de Dados Azure SQL.|
|[Converter trabalhos de agente de servidor SQL em trabalhos de base de dados elásticas](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Este script migra os trabalhos do seu agente de servidor SQL para trabalhos de base de dados elásticas.|
|[Enviar e-mails da Base de Dados Azure SQL](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Isto fornece uma solução como alternativa à capacidade do SendMail que está disponível no SQL Server no local. A solução utiliza funções Azure e o serviço Azure SendGrid para enviar e-mails da Base de Dados Azure SQL.|
|[Utilitário para mover no local logins do SQL Server para Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Um script PowerShell que cria um script de comando T-SQL para recriar logins e selecionar utilizadores de bases de dados de bases de dados de bases de dados sql server para Azure SQL Database. A ferramenta permite o mapeamento automático das contas AD do Windows para as contas AD do Azure, bem como a migração opcional de logins nativos do SQL Server.|
|[Automação de recolha de dados PerfMon usando Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Uma ferramenta que recolhe dados do PerMon para entender o desempenho da linha de base e ajuda nas recomendações de alvos de migração. Esta ferramenta utiliza logman.exe para criar o comando que irá criar, iniciar, parar e eliminar contadores de desempenho definidos num servidor SQL remoto|
|[Whitepaper - Migração de bases de dados para Azure SQL DB usando BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Este whitepaper fornece orientação e passos para ajudar a acelerar as migrações do SQL Server para a Azure SQL Database usando ficheiros BACPAC.|

Estes recursos foram desenvolvidos como parte do Programa Ninja Data SQL, que é patrocinado pela equipa de engenharia do Azure Data Group. A carta principal do programa Data SQL Ninja é desbloquear e acelerar a modernização complexa e competir oportunidades de migração da plataforma de dados para a plataforma de dados Azure Data da Microsoft. Se acha que a sua organização estaria interessada em participar no programa Data SQL Ninja, contacte a sua equipa de conta e peça-lhes que apresentem uma nomeação.


## <a name="next-steps"></a>Passos seguintes

Para começar a migrar o seu SQL Server para Azure SQL Database, consulte o guia de migração do [SQL Server para o SQL Database](sql-server-to-sql-database-guide.md).

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de base de dados e migração de dados, bem como tarefas especiais, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Base de Dados SQL consulte:
   - [Visão geral da Base de Dados Azure SQL](../../database/sql-database-paas-overview.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 

- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, ver
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para custos e cargas de trabalho de tamanho migram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Para avaliar a camada de acesso à aplicação, consulte o [Kit de Ferramentas de Migração do Acesso a Dados (Pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para mais detalhes sobre como realizar testes da Camada A/B do Acesso de Dados consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).