---
title: 'SQL Server para SQL Managed Instance: Visão geral da migração'
description: Saiba mais sobre as diferentes ferramentas e opções disponíveis para migrar as bases de dados do SQL Server para Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: 9074480f44e75a90c202f0d0813c43aed1f7ba95
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488210"
---
# <a name="migration-overview-sql-server-to-sql-managed-instance"></a>Visão geral da migração: SQL Server para SQL Managed Instance
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Saiba mais sobre diferentes opções e considerações de migração do seu SQL Server para Azure SQL Managed Instance. 

Pode migrar o SQL Server a funcionar no local ou em: 

- SQL Server nas Máquinas Virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de Base de Dados Relacional da Amazon (AWS RDS) 
- Motor compute (Plataforma Google Cloud - GCP)  
- Cloud SQL para SQL Server (Google Cloud Platform – GCP) 

Para outros cenários, consulte o [Guia de Migração da Base de Dados.](https://datamigration.microsoft.com/) 

## <a name="overview"></a>Descrição Geral

[Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md) é uma opção-alvo recomendada para cargas de trabalho do SQL Server que requerem um serviço totalmente gerido sem ter de gerir máquinas virtuais ou seus sistemas operativos. A SQL Managed Instance permite-lhe levantar e transferir as suas aplicações no local para Azure com alterações mínimas de aplicação ou de base de dados, ao mesmo tempo que tem isolamento total das suas instâncias com suporte a redes virtuais nativas (VNet). 

## <a name="considerations"></a>Considerações 

Os factores-chave a ter em conta na avaliação das opções de migração dependem de: 
- Número de servidores e bases de dados
- Tamanho das bases de dados
- Tempo de inatividade de negócios aceitável durante o processo de migração 

Um dos principais benefícios da migração dos seus Servidores SQL para a SQL Managed Instance é que pode optar por migrar todo o caso, ou apenas um subconjunto de bases de dados individuais. Planeie cuidadosamente incluir o seguinte no seu processo de migração: 
- Todas as bases de dados que precisam de ser a mesma instância 
- Objetos de nível de instância necessários para a sua aplicação, incluindo logins, credenciais, empregos e operadores de agentes SQL e gatilhos de nível de servidor. 

> [!NOTE]
> A azure SQL Managed Instance garante disponibilidade de 99,99% mesmo em cenários críticos, pelo que a sobrecarga causada por algumas funcionalidades no SQL MI não pode ser desativada. Para obter mais informações, consulte as causas de raiz que podem causar um desempenho diferente no blog [SQL Server e Azure SQL Managed Instance.](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) 


## <a name="choose-appropriate-target"></a>Escolha o alvo apropriado

Algumas diretrizes gerais para ajudá-lo a escolher o nível de serviço certo e as características de SQL Managed Instance para ajudar a corresponder a sua [linha de base de desempenho](sql-server-to-managed-instance-performance-baseline.md): 

- Utilize a linha de base de utilização do CPU para obter uma instância gerida que corresponda ao número de núcleos que o seu caso de SQL Server utiliza. Pode ser necessário dimensionar recursos para corresponder às características de [geração de hardware.](../../managed-instance/resource-limits.md#hardware-generation-characteristics) 
- Utilize a linha de base de utilização da memória para escolher uma [opção vCore](../../managed-instance/resource-limits.md#service-tier-characteristics) que corresponda adequadamente à sua alocação de memória. 
- Utilize a latência IO de base do subsistema de ficheiros para escolher entre os níveis de serviço De Final Geral (latência superior a 5 ms) e Business Critical (latência inferior a 3 ms). 
- Utilize a produção de linha de base para pré-localizar o tamanho dos dados e registar ficheiros para obter o desempenho esperado do IO. 

Pode escolher recursos de computação e armazenamento durante a implementação e, em seguida, alterá-los depois de usar o [portal Azure](../../database/scale-resources.md) sem incorrer em tempo de inatividade para a sua aplicação. 

> [!IMPORTANT]
> Qualquer discrepância nos [requisitos de rede virtuais geridos](../../managed-instance/connectivity-architecture-overview.md#network-requirements) pode impedi-lo de criar novos casos ou usar os existentes. Saiba mais sobre [a criação](../../managed-instance/virtual-network-subnet-create-arm-template.md)de redes novas   e [configuradas.](../../managed-instance/vnet-existing-add-subnet.md)   

### <a name="sql-server-vm-alternative"></a>Alternativa SQL Server VM

O seu negócio pode ter requisitos que tornem o SQL Server em VMs Azure um alvo mais adequado do que a Azure SQL Managed Instance. 

Se o seguinte se aplicar ao seu negócio, considere mudar-se para um SQL Server VM em vez disso: 

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
| [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment) | O Azure Migrate for Azure SQL permite-lhe descobrir e avaliar a sua propriedade de dados SQL em escala quando em VMware, fornecendo recomendações de implantação do Azure SQL, tamanho alvo e estimativas mensais. | 
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Primeiro serviço Azure que suporta a migração no modo offline para aplicações que podem pagar tempo de inatividade durante o processo de migração. Ao contrário da migração contínua no modo on-line, a migração em modo offline executa uma única vez a restauração de uma cópia de segurança completa da base de dados da fonte para o alvo. | 
|[Backup nativo e restauro](../../managed-instance/restore-sample-database-quickstart.md) | SQL Managed Instance suporta o RESTORE de backups nativos da base de dados do SQL Server (.bak ficheiros), tornando-se a opção de migração mais fácil para clientes que podem fornecer cópias de dados completas para armazenamento Azure. Cópias de segurança completas e diferenciais também são apoiadas e documentadas na [secção de ativos de migração](#migration-assets) mais tarde neste artigo.| 
|[Serviço de reprodução de registo (LRs)](../../managed-instance/log-replay-service-migrate.md) | Este é um serviço de nuvem ativado para Instância Gerida com base na tecnologia de envio de registos SQL Server, tornando-o uma opção de migração para clientes que podem fornecer cópias de segurança completas, diferenciais e de base de dados de registo para armazenamento Azure. O LRS é utilizado para restaurar ficheiros de backup do Azure Blob Storage para SQL Managed Instance.| 
| | |

### <a name="alternative-tools"></a>Ferramentas alternativas

O quadro que se segue lista os instrumentos alternativos de migração: 

|Tecnologia |Descrição  |
|---------|---------|
|[Replicação transacional](../../managed-instance/replication-transactional-overview.md) | Replicar dados da tabela de base de dados do SQL Server de origem para a SQL Managed Instance, fornecendo uma opção de migração tipo de assinante-editor, mantendo a consistência transacional. |  |
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| O [programa de cópias em massa (bcp) copia](/sql/tools/bcp-utility) dados de uma instância do SQL Server num ficheiro de dados. Utilize o utilitário BCP para exportar os dados da sua fonte e importe o ficheiro de dados para o exemplo alvo da SQL Managed Instance.</br></br> Para operações de cópia a granel de alta velocidade para mover dados para Azure SQL Database, a [ferramenta Smart Bulk Copy](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) pode ser utilizada para maximizar as velocidades de transferência, aproveitando tarefas paralelas de cópia. | 
|[Assistente de Exportação de Importações / BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) é um ficheiro Windows com uma `.bacpac` extensão que encapsula o esquema e os dados de uma base de dados. O BACPAC pode ser utilizado tanto para exportar dados de um servidor SQL de origem como para importar o ficheiro de volta para Azure SQL Managed Instance.  |  
|[Fábrica de Dados Azure (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| A [atividade copy](../../../data-factory/copy-activity-overview.md) in Azure Data Factory migra dados da base de dados(s) do SQL Server para a SQL Managed Instance utilizando conectores incorporados e um tempo de [execução de integração](../../../data-factory/concepts-integration-runtime.md).</br> </br> A ADF suporta uma vasta gama de [conectores](../../../data-factory/connector-overview.md) para mover dados de fontes do SQL Server para SQL Managed Instance. |
| | |

## <a name="compare-migration-options"></a>Comparar opções de migração

Compare as opções de migração para escolher o caminho adequado às necessidades do seu negócio. 

### <a name="recommended-options"></a>Opções recomendadas

O quadro a seguir compara as opções de migração recomendadas: 

|Opção de migração  |Quando utilizar  |Considerações  |
|---------|---------|---------|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) | - Migrar bases de dados únicas ou várias bases de dados à escala. </br> - Pode acomodar tempo de inatividade durante o processo de migração. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 - 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM |  - As migrações em escala podem ser automatizadas através do [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> - O tempo para completar a migração depende do tamanho da base de dados e impactado pelo tempo de backup e restauro. </br> - Pode ser necessário um tempo de inatividade suficiente. |
|[Backup nativo e restauro](../../managed-instance/restore-sample-database-quickstart.md) | - Migrar a base de dados de aplicações individuais em linha de negócio.  </br> - Migração rápida e fácil sem serviço ou ferramenta de migração separada.  </br> </br> Fontes apoiadas: </br> - SQL Server (2005 - 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM | - A cópia de segurança da base de dados utiliza vários fios para otimizar a transferência de dados para o armazenamento de Azure Blob, mas a largura de banda isv e o tamanho da base de dados podem ter impacto na taxa de transferência. </br> - O tempo de paragem deve acomodar o tempo necessário para efetuar uma cópia de segurança completa e restaurar (que é um tamanho de operação de dados).| 
|[Serviço de reprodução de registo (LRs)](../../managed-instance/log-replay-service-migrate.md) | - Migrar a base de dados de aplicações individuais em linha de negócio.  </br> - É necessário mais controlo para as migrações na base de dados.  </br> </br> Fontes apoiadas: </br> - SQL Server (2008 - 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM | - A migração implica fazer cópias de dados completas no SQL Server e copiar ficheiros de backup para o Azure Blob Storage. O LRS é utilizado para restaurar ficheiros de backup do Azure Blob Storage para SQL Managed Instance. </br> - As bases de dados que estão a ser restauradas durante o processo de migração estarão em modo de restauração e não podem ser utilizadas para ler ou escrever até que o processo esteja concluído..| 
| | | |

### <a name="alternative-options"></a>Opções alternativas

O quadro a seguir compara as opções alternativas de migração: 

|Método / tecnologia |Quando utilizar |Considerações  |
|---------|---------|---------|
|[Replicação transacional](../../managed-instance/replication-transactional-overview.md) | - Migrar publicando continuamente alterações das tabelas de bases de dados de origem para as tabelas de base de dados de casos geridos sql. </br> - Migrações completas ou parciais de bases de dados de tabelas selecionadas (subconjunto de base de dados).  </br> </br> Fontes apoiadas: </br> - SQL Server (2012 - 2019) com algumas limitações </br> - AWS EC2  </br> - GCP Compute SQL Server VM | </br> - A configuração é relativamente complexa em comparação com outras opções de migração.   </br> - Fornece uma opção de replicação contínua para migrar dados (sem tirar as bases de dados offline).</br> - A replicação transacional tem uma série de limitações a ter em conta na configuração do Editor no Servidor SQL de origem. Consulte [as limitações dos objetos de publicação](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) para saber mais.  </br> - Está disponível a capacidade de monitorizar a [atividade de replicação.](/sql/relational-databases/replication/monitor/monitoring-replication)    |
|[Cópia em massa](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - Migração total ou parcial de migração de dados. </br> - Pode acomodar o tempo de inatividade. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 - 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM   | - Requer tempo de inatividade para exportar dados provenientes da fonte e importar para o alvo. </br> - Os formatos de ficheiros e os tipos de dados utilizados na exportação/importação devem ser consistentes com os esquemas de tabela. |
|[Assistente de Exportação de Importações / BACPAC](../../database/database-import.md)| - Migrar a base de dados de aplicações individuais da Linha das Empresas. </br>- Adequado para bases de dados mais pequenas.  </br>  Não requer um serviço ou ferramenta de migração separado. </br> </br> Fontes apoiadas: </br> - SQL Server (2005 - 2019) no local ou Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP Compute SQL Server VM  |   </br> - Requer tempo de inatividade, uma vez que os dados devem ser exportados na fonte e importados no destino.   </br> - Os formatos de ficheiros e os tipos de dados utilizados na exportação/importação devem ser consistentes com esquemas de tabela para evitar erros de incompatibilidade entre truncações/tipos de dados. </br> - O tempo necessário para exportar uma base de dados com um grande número de objetos pode ser significativamente maior. |
|[Fábrica de Dados Azure (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| - Migrar e/ou transformar dados a partir da base de dados do SQL Server de origem.</br> - Fusão de dados de múltiplas fontes de dados para Azure SQL Gestd Instance tipicamente para cargas de trabalho de Business Intelligence (BI).   </br> - Requer a criação de oleodutos de movimento de dados na ADF para mover dados de origem para destino.   </br> - [O custo](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) é uma consideração importante e baseia-se nos gatilhos do gasoduto, nas operações, na duração do movimento dos dados, etc. |
| | | |

## <a name="feature-interoperability"></a>Interoperabilidade do recurso 

Existem considerações adicionais ao migrar cargas de trabalho que dependem de outras funcionalidades do SQL Server. 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

Migrar pacotes e projetos de serviços e projetos de integração de servidores SQL (SSIS) em SSISDB a Azure SQL Managed Instance utilizando [o Azure Database Migration Service (DMS)](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Apenas os pacotes SSIS em SSISDB a partir do SQL Server 2012 são suportados para migração. Converter pacotes SSIS legados antes da migração. Consulte o [tutorial de conversão](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) do projeto para saber mais. 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Os relatórios dos Serviços de Relato de Servidores sql (SSRS) podem ser migrados para relatórios paginados no Power BI. Utilize a [Ferramenta de Migração RDL](https://github.com/microsoft/RdlMigration) para ajudar a preparar e migrar os seus relatórios. Essa ferramenta foi desenvolvida pela Microsoft para ajudar os clientes a migrar relatórios RDL dos seus servidores do SSRS para o Power BI. Está disponível no GitHub, e documenta uma passagem de ponta a ponta do cenário de migração. 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

Os modelos Tabulares de Serviços de Análise de ServidorES SQL do SQL Server 2012 e acima podem ser migrados para os Serviços de Análise Azure, que é um modelo de implementação paaS para serviços de análise tabular em Azure. Pode saber mais sobre a migração de modelos on-prem para Azure Analysis Services neste [tutorial de vídeo.](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/)

Em alternativa, também pode considerar migrar os seus modelos Tabulares de Serviços de Análise no local para [Power BI Premium utilizando os novos pontos finais de leitura/escrita XMLA.](/power-bi/admin/service-premium-connect-tools) 
> [!NOTE]
> A funcionalidade de leitura/escrita de pontos finais power BI XMLA está atualmente em Visualização Pública e não deve ser considerada para cargas de trabalho de Produção até que a funcionalidade se torne geralmente disponível.

#### <a name="high-availability"></a>Elevada disponibilidade

As funcionalidades de alta disponibilidade do SQL Server estão sempre em casos de cluster falhados e os grupos de disponibilidade sempre em dia tornam-se obsoletos no target Azure SQL Managed Instance, uma vez que a arquitetura de alta disponibilidade já está incorporada tanto no [Modelo Geral de Finalidade (modelo de disponibilidade padrão)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) como [no Business Critical (modelo de disponibilidade premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL Managed Instance. O modelo de disponibilidade premium também fornece escala de leitura que permite ligar-se a um dos nós secundários para fins apenas de leitura.     

Para além da arquitetura de alta disponibilidade que está incluída no SQL Managed Instance, existe também a funcionalidade [de grupos de falha automática](../../database/auto-failover-group-overview.md) que permite gerir a replicação e falha de bases de dados em um caso gerido para outra região. 

#### <a name="sql-agent-jobs"></a>Empregos de agente SQL

Utilize a opção offline Azure Database Migration Service (DMS) para migrar [empregos de Agentes SQL](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). Caso contrário, guie os trabalhos em Transact-SQL (T-SQL) utilizando o SQL Server Management Studio e, em seguida, recrie-os manualmente no target SQL Managed Instance. 

> [!IMPORTANT]
> Atualmente, a Azure DMS apenas suporta empregos com passos de subsistema T-SQL. Os postos de trabalho com os passos do pacote SSIS terão de ser migrados manualmente. 

#### <a name="logins-and-groups"></a>Logins e grupos

Os logins SQL do servidor SQL de origem podem ser transferidos para Azure SQL Managed Instance usando o Serviço de Migração de Bases de Dados (DMS) em modo offline.  Utilize a lâmina **[de inícios de sessão](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** no **Assistente de Migração** para migrar os logins para o seu exemplo gerido sql alvo. 

Por padrão, o Azure Database Migration Service suporta apenas logins SQL migratórios. No entanto, pode ativar a capacidade de migrar os logins do Windows através de:

Garantindo que o sql Managed Instance tem acesso de leitura AZure AD, que pode ser configurado através do portal Azure por um utilizador com a função **De Administrador Global.**
Configurar a sua instância do Serviço de Migração de Bases de Dados Azure para permitir migrações de login de utilizador/grupo do Windows, que é configurada através do portal Azure, na página De Configuração. Depois de ativar esta definição, reinicie o serviço para que as alterações entrem em vigor.

Depois de reiniciar o serviço, os logins de utilizador/grupo do Windows aparecem na lista de logins disponíveis para migração. Para quaisquer logins de utilizador/grupo do Windows que migrar, é solicitado que forneça o nome de domínio associado. As contas de utilizadores de serviço (conta com nome de domínio NT AUTHORITY) e contas de utilizador virtuais (nome de conta com nome de domínio NT SERVICE) não são suportadas.

Para saber mais, consulte [como migrar utilizadores e grupos de janelas num exemplo de SQL Server para Azure SQL Managed Instance usando T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Em alternativa, pode utilizar a [ferramenta de utilitário PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) especialmente concebida pelos Microsoft Data Migration Architects. O utilitário utiliza o PowerShell para criar um script T-SQL para recriar logins e selecionar utilizadores de bases de dados da fonte para o alvo. A ferramenta mapeia automaticamente as contas AD do Windows para as contas AD do Azure e pode fazer uma procura UPN para cada login contra a fonte Ative Directory. A ferramenta scripts personalizado servidor e funções de base de dados, bem como membro de função, função de base de dados e permissões do utilizador. As bases de dados contidas não são atualmente suportadas e apenas um subconjunto de possíveis permissões do SQL Server são scriptados. 

#### <a name="encryption"></a>Encriptação

Ao migrar bases de dados protegidas por  [Encriptação de Dados Transparentes](../../database/transparent-data-encryption-tde-overview.md)   para um caso gerido utilizando a opção de restauro nativo, [migrar o certificado correspondente](../../managed-instance/tde-certificate-migrate.md) do Servidor SQL de origem para o target SQL Managed Instance *antes* da restauração da base de dados. 

#### <a name="system-databases"></a>Bases de dados do sistema

A restauração das bases de dados do sistema não é suportada. Para migrar objetos de nível de instância (armazenados em bases de dados master ou msdb), guie-os utilizando o Transact-SQL (T-SQL) e, em seguida, recrie-os no caso gerido pelo alvo. 

## <a name="leverage-advanced-features"></a>Alavancar recursos avançados 

Certifique-se de aproveitar as funcionalidades avançadas baseadas na nuvem oferecidas pela SQL Managed Instance. Por exemplo, já não precisa de se preocupar em gerir backups, pois o serviço o faz por si. Pode restaurar a qualquer [ponto no tempo dentro do período de retenção](../../database/recovery-using-backups.md#point-in-time-restore). Além disso, não precisa de se preocupar em criar alta disponibilidade, uma vez [que a elevada disponibilidade é incorporada.](../../database/high-availability-sla.md) 

Para reforçar a segurança, considere a utilização da [Autenticação do Diretório Ativo Azure,](../../database/authentication-aad-overview.md) [a auditoria, a](../../managed-instance/auditing-configure.md) [deteção de ameaças,](../../database/azure-defender-for-sql.md) [a segurança ao nível da linha](/sql/relational-databases/security/row-level-security)e a máscara dinâmica de [dados.](/sql/relational-databases/security/dynamic-data-masking)

Além de funcionalidades avançadas de gestão e segurança, a SQL Managed Instance fornece um conjunto de ferramentas avançadas que podem ajudá-lo a [monitorizar e afinar a sua carga de trabalho.](../../database/monitor-tune-overview.md) [O Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) permite-lhe monitorizar um grande conjunto de casos geridos de forma centralizada.  [Afinação](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   automática em casos geridos monitoriza continuamente o desempenho das estatísticas de execução do seu plano SQL e corrige automaticamente os problemas de desempenho identificados. 

Algumas funcionalidades só estão disponíveis quando o [nível de compatibilidade](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) da base de dados é alterado para o nível mais recente de compatibilidade (150). 

## <a name="migration-assets"></a>Ativos de migração 

Para assistência adicional, consulte os seguintes recursos que foram desenvolvidos para projetos de migração no mundo real.

|Recurso  |Descrição  |
|---------|---------|
|[Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e nível de remediação de aplicações/bases de dados para uma determinada carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda a acelerar as grandes avaliações imobiliárias, fornecendo e automatizada e uniforme processo de decisão da plataforma-alvo.|
|[Utilidade DBLoader](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|O DBLoader pode ser usado para carregar dados de ficheiros de texto delimitados para o SQL Server. Este utilitário de consola Windows utiliza a interface de volume de pessoal do cliente nativo do SQL Server, que funciona em todas as versões do SQL Server, incluindo O SQL MI.|
|[Utilitário para mover logins de servidor SQL nas instalações para Azure SQL Gestdited Instance](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Um script PowerShell que cria um script de comando T-SQL para recriar logins e selecionar utilizadores de bases de dados de servidor SQL para Azure SQL Managed Instance. A ferramenta permite o mapeamento automático das contas AD do Windows para as contas AD do Azure, bem como a migração opcional de logins nativos do SQL Server.|
|[Automação de recolha de dados perfmon usando Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Uma ferramenta que recolhe dados de realização para entender o desempenho de base que ajuda na recomendação do alvo de migração. Esta ferramenta que utiliza logman.exe para criar o comando que irá criar, iniciar, parar e eliminar contadores de desempenho definidos num servidor SQL remoto.|
|[Whitepaper - Migração de bases de dados para Azure SQL Managed Instance restaurando cópias de segurança completas e diferenciais](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Este whitepaper fornece orientação e passos para ajudar a acelerar as migrações do SQL Server para Azure SQL Managed Instance se tiver apenas cópias de segurança completas e diferenciais (e sem capacidade de backup de registo).|

Estes recursos foram desenvolvidos como parte do Programa Ninja Data SQL, que é patrocinado pela equipa de engenharia do Azure Data Group. A carta principal do programa Data SQL Ninja é desbloquear e acelerar a modernização complexa e competir oportunidades de migração da plataforma de dados para a plataforma de dados Azure Data da Microsoft. Se acha que a sua organização estaria interessada em participar no programa Data SQL Ninja, contacte a sua equipa de conta e peça-lhes que apresentem uma nomeação.


## <a name="next-steps"></a>Passos seguintes

Para começar a migrar o seu SqL Server para Azure SQL Managed Instance, consulte o [guia de migração SQL Server para SQL Managed Instance](sql-server-to-managed-instance-guide.md).

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de base de dados e migração de dados, bem como tarefas especiais, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Azure SQL Managed Instance consulte:
   - [Níveis de serviço em Azure SQL Gestão De Instância](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Diferenças entre o SQL Server e o Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, ver
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para custos e cargas de trabalho de tamanho migram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Para avaliar a camada de acesso à aplicação, consulte o [Kit de Ferramentas de Migração do Acesso a Dados (Pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para mais detalhes sobre como realizar testes da Camada A/B do Acesso de Dados consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).
