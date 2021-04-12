---
title: 'SQL Server para SQL Managed Instance: Visão geral da migração'
description: Saiba mais sobre as ferramentas e opções disponíveis para migrar as bases de dados do SQL Server para Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: 0a3fd1b492d19e241d89cc5477891c7c836e4640
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078983"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Visão geral da migração: SQL Server para Azure SQL Gestd Instance
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Saiba mais sobre as opções e considerações para migrar as bases de dados do SQL Server para Azure SQL Managed Instance. 

Pode migrar bases de dados do SQL Server que executam no local ou em: 

- Sql Server em Azure Virtual Machines.  
- Amazon Web Services (AWS) Elastic Compute Cloud (EC2). 
- Serviço de Base de Dados Relacional (RDS). 
- Motor compute na Plataforma Google Cloud (GCP).  
- SqL cloud para SQL Server em GCP. 

Para outros guias de migração, consulte [a Migração da Base de Dados.](https://docs.microsoft.com/data-migration) 

## <a name="overview"></a>Descrição Geral

[Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md) é uma opção-alvo recomendada para cargas de trabalho do SQL Server que requerem um serviço totalmente gerido sem ter de gerir máquinas virtuais ou seus sistemas operativos. A SQL Managed Instance permite-lhe mover as suas aplicações no local para Azure com alterações mínimas de aplicação ou de base de dados. Oferece isolamento total dos seus casos com suporte de rede virtual nativa. 

## <a name="considerations"></a>Considerações 

Os principais fatores a ter em conta quando se avalia as opções de migração são: 
- Número de servidores e bases de dados
- Tamanho das bases de dados
- Tempo de inatividade de negócios aceitável durante o processo de migração 

Um dos principais benefícios da migração das bases de dados do SQL Server para a SQL Managed Instance é que pode optar por migrar todo o caso ou apenas um subconjunto de bases de dados individuais. Planeie cuidadosamente incluir o seguinte no seu processo de migração: 
- Todas as bases de dados que precisam de ser a mesma instância 
- Objetos de nível de instância necessários para a sua aplicação, incluindo logins, credenciais, empregos e operadores de agentes SQL e gatilhos de nível de servidor 

> [!NOTE]
> A Azure SQL Managed Instance garante disponibilidade de 99,99%, mesmo em cenários críticos. A sobrecarga causada por algumas funcionalidades em SQL Managed Instance não pode ser desativada. Para obter mais informações, consulte as [principais causas das diferenças de desempenho entre a sql Managed Instance e a](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) entrada no blog SQL Server. 


## <a name="choose-an-appropriate-target"></a>Escolha um alvo apropriado

As seguintes diretrizes gerais podem ajudá-lo a escolher o nível de serviço certo e as características da SQL Managed Instance para ajudar a corresponder à sua [linha de base de desempenho](sql-server-to-managed-instance-performance-baseline.md): 

- Utilize a linha de base de utilização do CPU para obter uma instância gerida que corresponda ao número de núcleos que o seu exemplo de SQL Server utiliza. Pode ser necessário dimensionar recursos para corresponder às características de [geração de hardware.](../../managed-instance/resource-limits.md#hardware-generation-characteristics) 
- Utilize a linha de base de utilização da memória para escolher uma [opção vCore](../../managed-instance/resource-limits.md#service-tier-characteristics) que corresponda adequadamente à sua alocação de memória. 
- Utilize a latência de base I/O do subsistema de ficheiros para escolher entre os níveis de serviço De Finalidade Geral (latência superior a 5 ms) e Business Critical (latência inferior a 3 ms). 
- Utilize a produção de linha de base para pré-localizar o tamanho dos dados e registar ficheiros para obter o desempenho esperado de I/S. 

Pode escolher os recursos de computação e armazenamento durante a implementação e, em seguida, [alterá-los depois, utilizando o portal Azure,](../../database/scale-resources.md)sem incorrer em tempo de inatividade para a sua aplicação. 

> [!IMPORTANT]
> Qualquer discrepância nos [requisitos de rede virtual para casos geridos](../../managed-instance/connectivity-architecture-overview.md#network-requirements) pode impedi-lo de criar novos casos ou usar os existentes. Saiba mais sobre [a criação](../../managed-instance/virtual-network-subnet-create-arm-template.md)de redes novas   e [configuradas.](../../managed-instance/vnet-existing-add-subnet.md)   

Outra consideração chave na seleção do nível de serviço alvo em Azure SQL Managed Instance (General Purpose versus Business Critical) é a disponibilidade de determinadas funcionalidades, como In-Memory OLTP, que estão disponíveis apenas no nível Business Critical. 

### <a name="sql-server-vm-alternative"></a>Alternativa SQL Server VM

O seu negócio pode ter requisitos que tornem [o SQL Server em Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) um alvo mais adequado do que a Azure SQL Managed Instance. 

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
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Este serviço Azure suporta a migração no modo offline para aplicações que podem pagar tempo de inatividade durante o processo de migração. Ao contrário da migração contínua no modo on-line, a migração em modo offline executa uma única vez a restauração de uma cópia de segurança completa da base de dados da fonte para o alvo. | 
|[Backup nativo e restauro](../../managed-instance/restore-sample-database-quickstart.md) | SQL Managed Instance suporta a restauração de backups de base de dados do SQL Server nativos (.bak ficheiros). É a opção de migração mais fácil para os clientes que podem fornecer cópias de dados completas para o Azure Storage. Cópias de segurança completas e diferenciais também são apoiadas e documentadas na [secção sobre os ativos de migração](#migration-assets) mais tarde neste artigo.| 
|[Serviço de Reprodução de Registo](../../managed-instance/log-replay-service-migrate.md) | Este serviço de nuvem está ativado para sql Managed Instance com base na tecnologia de envio de registos sql Server. É uma opção de migração para clientes que podem fornecer cópias de dados completas, diferenciais e de base de dados para o Azure Storage. O Serviço de Reprodução de Registos é utilizado para restaurar ficheiros de backup do Azure Blob Storage para SQL Managed Instance.| 
| | |

O quadro que se segue lista os instrumentos alternativos de migração: 

|**Tecnologia** |**Descrição**  |
|---------|---------|
|[Replicação transacional](../../managed-instance/replication-transactional-overview.md) | Replicar dados das tabelas de base de dados do SQL Server de origem para SQL Managed Instance, fornecendo uma opção de migração tipo de assinante-editor, mantendo a consistência transacional. | 
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| A [ferramenta do programa de cópias a granel (bcp)](/sql/tools/bcp-utility) copia dados de uma instância do SQL Server num ficheiro de dados. Utilize a ferramenta para exportar os dados da sua fonte e importe o ficheiro de dados para a instância gerida pelo SQL alvo. </br></br> Para operações de cópia a granel de alta velocidade para mover dados para Azure SQL Managed Instance, pode utilizar a [ferramenta Smart Bulk Copy](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) para maximizar a velocidade de transferência, tirando partido de tarefas paralelas de cópia. | 
|[Assistente de Exportação de Importações/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) é um ficheiro Windows com uma extensão .bacpac que encapsula o esquema e os dados de uma base de dados. Pode utilizar o BACPAC para exportar dados de uma fonte do SQL Server e importar os dados de volta para a Azure SQL Managed Instance. |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)|  A [atividade copy](../../../data-factory/copy-activity-overview.md) in Azure Data Factory migra dados das bases de dados do SQL Server para o SQL Managed Instance utilizando conectores incorporados e um tempo de [execução de integração](../../../data-factory/concepts-integration-runtime.md).</br> </br> A Data Factory suporta uma vasta gama de [conectores](../../../data-factory/connector-overview.md) para mover dados de fontes do SQL Server para SQL Managed Instance. |

## <a name="compare-migration-options"></a>Comparar opções de migração

Compare as opções de migração para escolher o caminho adequado às necessidades do seu negócio. 

O quadro a seguir compara as opções de migração que recomendamos: 

|Opção de migração  |Quando utilizar  |Considerações  |
|---------|---------|---------|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md) | - Migrar bases de dados únicas ou várias bases de dados à escala. </br> - Pode acomodar tempo de inatividade durante o processo de migração. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 a 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM |  - As migrações em escala podem ser automatizadas através do [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> - O tempo para completar a migração depende do tamanho da base de dados e é afetado pelo tempo de backup e restauro. </br> - Poderá ser necessário um tempo de paragem suficiente. |
|[Backup nativo e restauro](../../managed-instance/restore-sample-database-quickstart.md) | - Migrar bases de dados individuais de aplicações de linha de negócio.  </br> - Migração rápida e fácil sem serviço ou ferramenta de migração separada.  </br> </br> Fontes apoiadas: </br> - SQL Server (2005 a 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM | - A cópia de segurança da base de dados utiliza vários fios para otimizar a transferência de dados para o Azure Blob Storage, mas a largura de banda do parceiro e o tamanho da base de dados podem afetar a taxa de transferência. </br> - O tempo de paragem deve acomodar o tempo necessário para efetuar uma cópia de segurança completa e restaurar (que é um tamanho de operação de dados).| 
|[Serviço de Reprodução de Registo](../../managed-instance/log-replay-service-migrate.md) | - Migrar bases de dados individuais de aplicações de linha de negócio.  </br> - É necessário mais controlo para as migrações na base de dados.  </br> </br> Fontes apoiadas: </br> - SQL Server (2008 a 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM | - A migração implica fazer cópias de dados completas no SQL Server e copiar ficheiros de backup para o Azure Blob Storage. O Serviço de Reprodução de Registos é utilizado para restaurar ficheiros de backup do Azure Blob Storage para SQL Managed Instance. </br> - As bases de dados que estão a ser restauradas durante o processo de migração estarão em modo de restauração e não podem ser usadas para ler ou escrever até que o processo esteja concluído.| 
| | | |

O quadro a seguir compara as opções alternativas de migração: 

|Método ou tecnologia |Quando utilizar |Considerações  |
|---------|---------|---------|
|[Replicação transacional](../../managed-instance/replication-transactional-overview.md) | - Migrar publicando continuamente alterações das tabelas de bases de dados de origem para as tabelas de base de dados de casos geridos sql. </br> - Faça migrações na base de dados completas ou parciais de tabelas selecionadas (subconjunto de uma base de dados).  </br> </br> Fontes apoiadas: </br> - SQL Server (2012 a 2019) com algumas limitações </br> - AWS EC2  </br> - GCP Compute SQL Server VM | </br> - A configuração é relativamente complexa em comparação com outras opções de migração.   </br> - Fornece uma opção de replicação contínua para migrar dados (sem tirar as bases de dados offline).</br> - A replicação transacional tem limitações a ter em conta quando está a configurar o editor na origem sql Server. Consulte [as limitações na publicação de objetos](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) para saber mais.  </br> - Está disponível a capacidade de monitorizar a [atividade de replicação.](/sql/relational-databases/replication/monitor/monitoring-replication)    |
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - Faça migrações de dados completas ou parciais. </br> - Pode acomodar o tempo de inatividade. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 a 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM   | - Requer tempo de paragem para exportar dados da fonte e importar para o objetivo. </br> - Os formatos de ficheiros e os tipos de dados utilizados na exportação ou importação devem ser coerentes com os esquemas de tabela. |
|[Assistente de Exportação de Importações/BACPAC](../../database/database-import.md)| - Migrar bases de dados individuais de aplicações de linha de negócio. </br>- Adequado para bases de dados mais pequenas.  </br>  Não requer um serviço ou ferramenta de migração separado. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 a 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM  |   </br> - Requer tempo de inatividade porque os dados precisam de ser exportados na fonte e importados no destino.   </br> - Os formatos de ficheiros e os tipos de dados utilizados na exportação ou importação devem ser coerentes com os esquemas de tabelas para evitar erros de truncação ou de incompatibilidade do tipo dados. </br> - O tempo necessário para exportar uma base de dados com um grande número de objetos pode ser significativamente maior. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| - Migrar e/ou transformar dados a partir de bases de dados do SQL Server de origem.</br> - A fusão de dados de múltiplas fontes de dados para a Azure SQL Managed Instance é normalmente para cargas de trabalho de inteligência empresarial (BI).   </br> - Requer a criação de oleodutos de movimento de dados na Data Factory para mover dados de origem para destino.   </br> - [O custo](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) é uma consideração importante e baseia-se em fatores como os gatilhos do gasoduto, as operações e a duração do movimento de dados. |
| | | |

## <a name="feature-interoperability"></a>Interoperabilidade do recurso 

Há mais considerações quando está a migrar cargas de trabalho que dependem de outras funcionalidades do SQL Server. 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

Migrar pacotes e projetos de integração de servidores SQL (SSIS) em SSISDB a Azure SQL Managed Instance utilizando [o Azure Database Migration Service](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Apenas os pacotes SSIS em SSISDB a partir do SQL Server 2012 são suportados para migração. Converter pacotes SSIS mais antigos antes da migração. Consulte o [tutorial de conversão](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) do projeto para saber mais. 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Pode migrar relatórios sql server reporting Services (SSRS) para relatórios paginados no Power BI. Utilize a [Ferramenta de Migração RDL](https://github.com/microsoft/RdlMigration) para ajudar a preparar e migrar os seus relatórios. A Microsoft desenvolveu esta ferramenta para ajudar os clientes a migrar relatórios de Linguagem de Definição de Relatório (RDL) dos seus servidores SSRS para o Power BI. Está disponível no GitHub e inclui um guia ponto a ponto do cenário de migração. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

Os modelos tabulares dos Serviços de Análise de Servidores SQL do SQL Server 2012 e posteriormente podem ser migrados para os Serviços de Análise Azure, que é uma plataforma como um modelo de implementação de serviço (PaaS) para o modelo tabular de Serviços de Análise em Azure. Você pode saber mais sobre a migração de modelos no local para Azure Analysis Services [neste tutorial de vídeo.](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/)

Em alternativa, pode considerar migrar os seus modelos tabulares de Serviços de Análise no local para [Power BI Premium utilizando os novos pontos finais de leitura/escrita XMLA.](/power-bi/admin/service-premium-connect-tools) 

### <a name="high-availability"></a>Elevada disponibilidade

As funcionalidades de alta disponibilidade do SQL Server estão sempre em casos de cluster de failover e os grupos de disponibilidade always on tornam-se obsoletos na instância gerida pelo SQL alvo. A arquitetura de alta disponibilidade já está incorporada nos níveis de serviço [de Finalidade Geral (modelo de disponibilidade padrão)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) e [Business Critical (modelo de disponibilidade premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) para sql Managed Instance. O modelo de disponibilidade premium também fornece escala de leitura que permite ligar-se a um dos nós secundários para fins apenas de leitura.     

Além da arquitetura de alta disponibilidade que está incluída em SQL Managed Instance, o recurso [de grupos de falha automática permite-lhe](../../database/auto-failover-group-overview.md) gerir a replicação e falha de bases de dados em um caso gerido para outra região. 

### <a name="sql-agent-jobs"></a>Empregos de agente SQL

Utilize a opção offline Azure Database Migration Service para migrar [empregos de Agentes SQL](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). Caso contrário, guie os trabalhos em Transact-SQL (T-SQL) utilizando o SQL Server Management Studio e, em seguida, recrie-os manualmente na instância gerida pelo SQL alvo. 

> [!IMPORTANT]
> Atualmente, o Azure Database Migration Service suporta apenas empregos com passos de subsistema T-SQL. Os postos de trabalho com os passos do pacote SSIS têm de ser migrados manualmente. 

### <a name="logins-and-groups"></a>Logins e grupos

Mover os logins SQL da fonte do SQL Server para Azure SQL Managed Instance utilizando o Serviço de Migração de Bases de Dados em modo offline.  Utilize o painel [de logins Select](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) no Assistente de Migração para migrar logins para o seu exemplo gerido pelo SQL alvo. 

Por padrão, o Azure Database Migration Service suporta a migração apenas de logins SQL. No entanto, pode ativar a migração de logins do Windows através de:

- Garantindo que o exemplo gerido pela SQL tem acesso ao Azure Ative Directory (Azure AD). Um utilizador que tenha a função de Administrador Global pode configurar esse acesso através do portal Azure.
- Configurar o Serviço de Migração da Base de Dados Azure para permitir migrações de login de utilizador do Windows ou de grupo. Configura-se isto através do portal Azure, na página **Configuração.** Depois de ativar esta definição, reinicie o serviço para que as alterações entrem em vigor.

Depois de reiniciar o serviço, os logins do utilizador do Windows ou de grupo aparecem na lista de logins disponíveis para migração. Para qualquer utilizador do Windows ou logins de grupo que migra, é solicitado que forneça o nome de domínio associado. As contas de utilizadores de serviço (contas com o nome de domínio NT AUTHORITY) e contas de utilizadores virtuais (contas com o nome de domínio NT SERVICE) não são suportadas. Para saber mais, consulte [como migrar utilizadores e grupos windows numa instância do SQL Server para Azure SQL Managed Instance usando T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Em alternativa, pode utilizar o [utilitário PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) especialmente concebido pelos arquitetos da migração de dados da Microsoft. O utilitário utiliza o PowerShell para criar um script T-SQL para recriar logins e selecionar utilizadores de bases de dados da fonte para o alvo. 

O utilitário PowerShell mapeia automaticamente as contas do Windows Server Ative Directory para contas AZure AD, e pode fazer uma procura UPN para cada login contra a origem Ative Directory instance. O utilitário scripts personalizado servidor e funções de base de dados, juntamente com a adesão à função e permissões do utilizador. As bases de dados contidas ainda não estão suportadas e apenas um subconjunto de possíveis permissões do SQL Server são scriptados. 

### <a name="encryption"></a>Encriptação

Quando estiver a migrar bases de dados protegidas pela  [Encriptação de Dados Transparente](../../database/transparent-data-encryption-tde-overview.md)   para uma instância gerida utilizando a opção de restauro nativo, [migrar o certificado correspondente](../../managed-instance/tde-certificate-migrate.md) da instância do SqL Server de origem para a instância gerida pelo SQL alvo *antes* da restauração da base de dados. 

### <a name="system-databases"></a>Bases de dados do sistema

A restauração das bases de dados do sistema não é suportada. Para migrar objetos de nível de instância (armazenados nas bases de dados master e msdb), guie-os utilizando o T-SQL e, em seguida, recrie-os no caso gerido pelo alvo. 

### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (tabelas otimizadas pela memória)

O SQL Server fornece uma capacidade OLTP In-Memory. Permite o uso de tabelas otimizadas pela memória, tipos de mesa otimizados pela memória e módulos SQL compilados de forma nativa para executar cargas de trabalho que têm requisitos de alta produção e baixa latência para o processamento transacional. 

> [!IMPORTANT]
> In-Memory OLTP é suportado apenas no nível Business Critical em Azure SQL Managed Instance. Não é suportado no nível de propósito geral.

Se tiver tabelas otimizadas para a memória ou tipos de mesa otimizados para a memória na sua instância do SQL Server no local e quiser migrar para a Azure SQL Managed Instance, deve:

- Escolha o nível Business Critical para o seu exemplo gerido pelo SQL que suporta In-Memory OLTP.
- Se pretender migrar para o nível de Finalidade Geral em Azure SQL Managed Instance, remova as tabelas otimizadas pela memória, os tipos de tabelas otimizados pela memória e os módulos SQL compilados de forma nativa que interagem com objetos otimizados para a memória antes de migrar as suas bases de dados. Pode utilizar a seguinte consulta T-SQL para identificar todos os objetos que precisam de ser removidos antes da migração para o nível final:

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

Para saber mais sobre as tecnologias de memória, consulte [o desempenho otimizar utilizando tecnologias de memória na Base de Dados Azure SQL e na Azure SQL Managed Instance](../../in-memory-oltp-overview.md).

## <a name="advanced-features"></a>Funcionalidades avançadas 

Certifique-se de aproveitar as funcionalidades avançadas baseadas em nuvem em SQL Managed Instance. Por exemplo, não precisa de se preocupar em gerir backups porque o serviço o faz por si. Pode restaurar a qualquer [ponto no tempo dentro do período de retenção](../../database/recovery-using-backups.md#point-in-time-restore). Além disso, não precisa de se preocupar em criar alta disponibilidade, porque [a alta disponibilidade é incorporada.](../../database/high-availability-sla.md) 

Para reforçar a segurança, considere a utilização da [autenticação AZURE AD,](../../database/authentication-aad-overview.md) [auditoria,](../../managed-instance/auditing-configure.md) [deteção de ameaças,](../../database/azure-defender-for-sql.md) [segurança ao nível da linha](/sql/relational-databases/security/row-level-security)e [mascaramento dinâmico de dados.](/sql/relational-databases/security/dynamic-data-masking)

Além de funcionalidades avançadas de gestão e segurança, a SQL Managed Instance fornece ferramentas avançadas que podem ajudá-lo a [monitorizar e afinar a sua carga de trabalho.](../../database/monitor-tune-overview.md) [O Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) permite-lhe monitorizar um grande conjunto de casos geridos de forma centralizada.  [Afinação](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   automática em casos geridos monitoriza continuamente o desempenho da execução do seu plano SQL e corrige automaticamente os problemas de desempenho identificados. 

Algumas funcionalidades só estão disponíveis após a alteração do [nível de compatibilidade](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) da base de dados para o nível mais recente de compatibilidade (150). 

## <a name="migration-assets"></a>Ativos de migração 

Para obter mais assistência, consulte os seguintes recursos que foram desenvolvidos para projetos de migração no mundo real.

|Recurso  |Description  |
|---------|---------|
|[Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e um nível de remediação de aplicação/base de dados para uma carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda a acelerar as grandes avaliações imobiliárias, fornecendo um processo de decisão automatizado e uniforme para plataformas-alvo.|
|[Utilidade DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Pode utilizar o DBLoader para carregar dados de ficheiros de texto delimitados para o SQL Server. Este utilitário de consola Windows utiliza a interface de carga a granel do cliente nativo do SQL Server. A interface funciona em todas as versões do SQL Server, juntamente com a Azure SQL Managed Instance.|
|[Utilitário para mover no local logins do SQL Server para Azure SQL Managed Instance](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Um script PowerShell pode criar um script de comando T-SQL para recriar logins e selecionar utilizadores de bases de dados de servidor SQL para Azure SQL Managed Instance. A ferramenta permite o mapeamento automático das contas do Windows Server Ative Directory para contas Azure AD, juntamente com logins nativos do SQL Server.|
|[Automatização de recolha de dados perfmon usando Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Pode utilizar a ferramenta Logman para recolher dados perfmon (para ajudá-lo a compreender o desempenho da linha de base) e obter recomendações de destino de migração. Esta ferramenta utiliza logman.exe para criar o comando que irá criar, iniciar, parar e eliminar contadores de desempenho definidos numa instância remota do SQL Server.|
|[Migração de bases de dados para Azure SQL Caso Gerido restaurando cópias de segurança completas e diferenciais](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Este livro branco fornece orientações e passos para ajudar a acelerar as migrações do SQL Server para a Azure SQL Managed Instance se tiver apenas cópias de segurança completas e diferenciais (e sem capacidade de backup de registo).|

A equipa de Engenharia Data SQL desenvolveu estes recursos. A principal carta desta equipa é desbloquear e acelerar a modernização complexa dos projetos de migração da plataforma de dados para a plataforma de dados Azure da Microsoft.


## <a name="next-steps"></a>Passos seguintes

- Para começar a migrar as bases de dados do SQL Server para Azure SQL Managed Instance, consulte o [guia de migração do SQL Server para Azure SQL Managed Instance](sql-server-to-managed-instance-guide.md).

- Para uma matriz de serviços e ferramentas que podem ajudá-lo com cenários de base de dados e migração de dados, bem como tarefas de especialidade, consulte [Serviços e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Azure SQL Managed Instance, consulte:
   - [Níveis de serviço em Azure SQL Gestão De Instância](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Diferenças entre o SQL Server e o Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Custo Total da Calculadora de Propriedade Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, consulte:
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Best practices for costing and sizing workloads migrated to Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) (Melhores práticas para avaliar custos e dimensionar as cargas de trabalho migradas para o Azure) 

- Para avaliar a camada de acesso à aplicação, consulte o [Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Para obter mais informações sobre como realizar testes A/B na camada de acesso a dados, consulte o [Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).
