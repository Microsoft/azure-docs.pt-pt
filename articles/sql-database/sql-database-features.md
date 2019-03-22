---
title: Comparação de funcionalidades de base de dados SQL do Azure | Documentos da Microsoft
description: Este artigo compara as funcionalidades do SQL Server que estão disponíveis em diferentes tipos de base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 82ed8783444a414d4706f97c8c9b6ea18734561e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311752"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Comparação de recursos: Base de dados SQL do Azure e do SQL Server

Base de dados SQL do Azure partilha uma base de código comum com o SQL Server. Os recursos do SQL Server suportada pelo Azure SQL Database dependem do tipo de base de dados SQL do Azure que criar. Base de dados SQL do Azure, pode criar uma base de dados como parte de um [instância gerida](sql-database-managed-instance.md), como uma base de dados, ou como parte de um conjunto elástico.

A Microsoft continua a adicionar funcionalidades à base de dados do Azure SQL. Visite a página de atualizações de serviço Web do Azure para as atualizações mais recentes com estes filtros:

- Filtrado para o [serviço da Base de Dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrado para [anúncios](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) de Disponibilidade Geral (DG) para funcionalidades de Base de Dados SQL.

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Suporte de funcionalidades do SQL Server na base de dados do Azure SQL

A tabela seguinte lista os principais recursos do SQL Server e fornece informações sobre se a funcionalidade é total ou parcialmente suportada e uma ligação para obter mais informações sobre a funcionalidade.

| **Funcionalidade SQL** | **É suportada por bases de dados individuais e conjuntos elásticos** | **Suportado por instâncias geridas** |
| --- | --- | --- |
| [Georreplicação ativa](sql-database-active-geo-replication.md) | Sim - todas as camadas que não seja de hiperescala de serviço | Não, consulte [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md) |
| [Grupos de ativação pós-falha automática](sql-database-auto-failover-group.md) | Sim - todas as camadas que não seja de hiperescala de serviço | Sim, em [pré-visualização pública](sql-database-auto-failover-group.md)|
| [Sempre encriptado](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Sim - consulte [arquivo de certificados](sql-database-always-encrypted.md) e [Cofre de chaves](sql-database-always-encrypted-azure-key-vault.md) | Sim - consulte [arquivo de certificados](sql-database-always-encrypted.md) e [Cofre de chaves](sql-database-always-encrypted-azure-key-vault.md) |
| [Grupos de disponibilidade Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Elevada disponibilidade](sql-database-high-availability.md) está incluído com cada base de dados. Recuperação após desastre é abordada em [descrição geral da continuidade do negócio com a base de dados do Azure SQL](sql-database-business-continuity.md) | [Elevada disponibilidade](sql-database-high-availability.md) está incluído com cada base de dados. Recuperação após desastre é abordada em [descrição geral da continuidade do negócio com a base de dados do Azure SQL](sql-database-business-continuity.md) |
| [Anexar uma base de dados](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Não | Não |
| [Funções de aplicação](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Sim | Sim |
|[Auditoria](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Sim](sql-database-auditing.md)| [Sim](sql-database-managed-instance-auditing.md) |
| [Cópias de segurança automáticas](sql-database-automated-backups.md) | Sim | Sim |
| [(Forçar o plano) de otimização automática](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Sim](sql-database-automatic-tuning.md)| [Sim](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [(Índices) de otimização automática](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Sim](sql-database-automatic-tuning.md)| Não |
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | Sim | Sim |
| [Ficheiro BACPAC (exportação)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Sim - veja [exportação da base de dados SQL](sql-database-export.md) | Sim |
| [Ficheiro BACPAC (importação)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Sim - veja [importar da base de dados SQL](sql-database-import.md) | Sim |
| [Comando de cópia de segurança](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Não, apenas iniciada pelo sistema de cópias de segurança automáticas - veja [cópias de segurança automatizadas](sql-database-automated-backups.md) | Cópias de segurança automáticas iniciadas pelo sistema e o utilizador iniciou as cópias de segurança apenas de cópia - veja [diferenças de cópia de segurança](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Funções incorporadas](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - Consulte funções individuais | Sim - veja [procedimentos armazenados, funções, aciona as diferenças](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Captura de dados de alteração](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Não | Sim |
| [Registo de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Sim |Sim |
| [Agrupamento - base de dados](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | Sim | Sim |
| [Agrupamento - servidor/instância](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | Não | Sim, em [pré-visualização pública](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)|
| [Índices Columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Sim - [escalão Premium, o escalão Standard - S3 e superior, o escalão fins gerais e crítico para a empresa camadas](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Sim |
| [Tempo de execução comum para a linguagem (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Não | Sim - veja [diferenças CLR](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Bases de dados contidas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Sim | Não [devido a defeitos no RESTAURO, incluindo o RESTAURO de ponto no tempo](sql-database-managed-instance-transact-sql-information.md#cannot-restore-contained-database) |
| [Utilizadores contidos](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Sim | Sim |
| [Controlo de palavras-chave language do fluxo](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Sim | Sim |
| [Consultas entre bases de dados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não, consulte [consultas elásticas](sql-database-elastic-query-overview.md) | Sim, além de [consultas elásticas](sql-database-elastic-query-overview.md) |
| [Transações entre bases de dados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não | Sim, dentro da instância. Ver [ligado diferenças de servidor](sql-database-managed-instance-transact-sql-information.md#linked-servers) para instância para várias consultas. |
| [Cursores](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Sim |Sim |
| [Compressão de dados](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Sim |Sim |
| [Correio de base de dados](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Não | Sim |
| [Serviço de migração de dados (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Sim | Sim |
| [Espelhamento de banco de dados](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Não | Não |
| [Definições de configuração de base de dados](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Sim | Sim |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Não | Não |
| [Instantâneos de base de dados](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Não | Não |
| [Tipos de dados](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Sim |Sim |
| [Declarações DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Maioria - Consulte declarações individuais | Sim - veja [diferenças DBCC](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [Instruções DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Maioria - Consulte declarações individuais | Sim - veja [diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Acionadores DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Apenas base de dados |  Sim |
| [Vistas distribuídas de partição](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Não | Sim |
| [Transações distribuídas - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Não, consulte [transações elásticas](sql-database-elastic-transactions-overview.md) |  Não, consulte [ligado diferenças de servidor](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [Instruções DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Sim | Sim |
| [Acionadores DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | Maioria - Consulte declarações individuais |  Sim |
| [DMVs](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Maioria - Consulte DMVs individuais |  Sim - veja [diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
|[Máscara de dados dinâmica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Sim](sql-database-dynamic-data-masking-get-started.md)| [Sim](sql-database-dynamic-data-masking-get-started.md) |
| [Conjuntos elásticos](sql-database-elastic-pool.md) | Sim | Interno-uma única instância gerida, pode ter várias bases de dados que partilham o mesmo conjunto de recursos |
| [Notificações de eventos](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Não, consulte [alertas](sql-database-insights-alerts-portal.md) | Não |
| [Expressões](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Sim | Sim |
| [Eventos expandidos](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Alguns - consulte [estendido de eventos na base de dados SQL](sql-database-xevent-db-diff-from-svr.md) | Sim - veja [estendido diferenças de eventos](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Procedimentos armazenados expandidos](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Não | Não |
[Ficheiros e grupos de ficheiros](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Grupo de ficheiros primário apenas | Sim |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Não | Não |
| [Pesquisa em texto completo](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Separadores de palavras de terceiros não são suportados |Separadores de palavras de terceiros não são suportados |
| [Funções](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - Consulte funções individuais | Sim - veja [procedimentos armazenados, funções, aciona as diferenças](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Restauro geográfico](sql-database-recovery-using-backups.md#geo-restore) | Sim - todas as camadas que não seja de hiperescala de serviço | Não, pode restaurar COPY_ONLY cópias de segurança completas que efetuar periodicamente - consulte [diferenças de cópia de segurança](sql-database-managed-instance-transact-sql-information.md#backup) e [restaurar diferenças](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Processamento de gráficos](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Sim | Sim |
| [Otimização dentro da memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Sim - [apenas escalões Premium e crítico para a empresa](sql-database-in-memory.md) | Sim - [comerciais críticos apenas no escalão](sql-database-managed-instance.md) |
| [Suporte de dados JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Sim](sql-database-json-features.md) | [Sim](sql-database-json-features.md) |
| [Elementos de linguagem](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Maioria - Consulte elementos individuais |  Sim - veja [diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Servidores ligados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não, consulte [consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) | Apenas para o SQL Server e base de dados SQL |
| [Envio de log](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Elevada disponibilidade](sql-database-high-availability.md) está incluído com cada base de dados. Recuperação após desastre é abordada em [descrição geral da continuidade do negócio com a base de dados do Azure SQL](sql-database-business-continuity.md) |[Elevada disponibilidade](sql-database-high-availability.md) está incluído com cada base de dados. Recuperação após desastre é abordada em [descrição geral da continuidade do negócio com a base de dados do Azure SQL](sql-database-business-continuity.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Não | Não |
| [Registo mínimo na importação em massa](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Não | Não |
| [Modificar os dados de sistema](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Não | Sim |
| [Automação OLE](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | Não | Não |
| [Operações de índice online](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Sim | Sim |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Não|Sim - veja [diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Sim|Sim|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Não|Sim - veja [diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Não|Sim - veja [diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Sim|Sim|
| [Operadores](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Maioria - Consulte operadores individuais |Sim - veja [diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Criação de partições](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Sim | Sim |
| [Um ponto anterior no restauro de base de dados de tempo](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Sim - todos os escalões de serviço que não seja de hiperescala - veja [recuperação de base de dados SQL](sql-database-recovery-using-backups.md#point-in-time-restore) | Sim - veja [recuperação de base de dados SQL](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Não | Não |
| [Gestão baseada em políticas](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Não | Não |
| [Predicados](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Sim | Sim |
| [Notificações de consulta](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | Não | Sim |
| [Query Performance Insights](sql-database-query-performance.md) (Informações de Desempenho de Consultas) | Sim | Não |
| [Serviços R](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Sim, em [pré-visualização pública](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Não |
| [Governador de recursos](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Não | Sim |
| [Instruções de RESTAURO](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Não | Sim - veja [restaurar diferenças](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Restaurar a base de dados de cópia de segurança](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Apenas - uma cópias de segurança automáticas ver [recuperação de base de dados SQL](sql-database-recovery-using-backups.md) | A partir de cópias de segurança automáticas - ver [recuperação de base de dados SQL](sql-database-recovery-using-backups.md) e a partir de cópias de segurança completas - ver [diferenças de cópia de segurança](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Segurança ao nível da linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Sim | Sim |
| [Pesquisa semântica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Não | Não |
| [Números de sequência](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Sim | Sim |
| [Mediador de serviço](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Não | Sim - veja [diferenças de Mediador de serviço](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Definições de configuração do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Não | Sim - veja [diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Conjunto de instruções](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Maioria - Consulte declarações individuais | Sim - veja [diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Sim | Sim |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Sim | Sim |
| [Análise de SQL](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | Sim | Sim |
| [Sincronização de dados SQL](sql-database-get-started-sql-data-sync.md) | Sim | Não |
| [Agente do SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Não, consulte [tarefas elásticas](sql-database-elastic-jobs-getting-started.md) | Sim - veja [diferenças de agente do SQL Server](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Não, consulte [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Não, consulte [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [Auditoria do SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Não, consulte [auditoria de base de dados SQL](sql-database-auditing.md) | Sim - veja [diferenças de auditoria](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Sim | Sim |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Sim, com um SSIS geridos no ambiente do Azure Data Factory (ADF), onde os pacotes são armazenados em SSISDB alojado pela base de dados do Azure SQL e executado no Runtime de integração de SSIS do Azure (IR), consulte [criar IR do Azure-SSIS no ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Para comparar as funcionalidades SSIS no servidor de base de dados SQL e a instância gerida, veja [conjuntos de bases de dados única/elástico de comparar a base de dados de SQL do Azure e a instância gerida](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). | Sim, com um SSIS geridos no ambiente do Azure Data Factory (ADF), onde os pacotes são armazenados em SSISDB alojado pela instância gerida e executado no Runtime de integração de SSIS do Azure (IR), consulte [criar IR do Azure-SSIS no ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Para comparar os recursos do SSIS na base de dados SQL e a instância gerida, veja [conjuntos de bases de dados única/elástico de comparar a base de dados de SQL do Azure e a instância gerida](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Sim | Sim |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Sim | Sim |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Não, consulte [eventos expandidos](sql-database-xevent-db-diff-from-svr.md) | Sim |
| [Replicação do SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Apenas subscritor de replicação de instantâneo e transacional](sql-database-single-database-migrate.md) | Sim, em [pré-visualização pública](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Não - [ver o Power BI](https://docs.microsoft.com/power-bi/) | Não - [ver o Power BI](https://docs.microsoft.com/power-bi/) |
| [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Sim | Sim |
| [Funções armazenadas do sistema](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Maioria - Consulte funções individuais | Sim - veja [procedimentos armazenados, funções, aciona as diferenças](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Procedimentos armazenados do sistema](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Alguns - Consulte individuais procedimentos armazenados | Sim - veja [procedimentos armazenados, funções, aciona as diferenças](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Tabelas de sistema](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Alguns - Consulte tabelas individuais | Sim - veja [diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Exibições de catálogo do sistema](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Alguns - Consulte vistas individuais | Sim - veja [diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Tabelas temporárias](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Tabelas temporárias globais locais e no âmbito da base de dados | Tabelas temporárias globais locais e com âmbito de instância |
| [Tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Sim](sql-database-temporal-tables.md) | [Sim](sql-database-temporal-tables.md) |
|Deteção de ameaças|  [Sim](sql-database-threat-detection.md)|[Sim](sql-database-managed-instance-threat-detection.md)|
| [Sinalizadores de rastreio](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Não | Não |
| [Variáveis](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Sim | Sim |
| [Encriptação de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Sim - fins gerais e crítico para a empresa apenas os escalões de serviço| [Sim](transparent-data-encryption-azure-sql.md) |
[VNet](../virtual-network/virtual-networks-overview.md) | Parcial - consulte [pontos finais de VNet](sql-database-vnet-service-endpoint-rule-overview.md) | Sim, apenas modelo do Resource Manager |
| [Clustering de Failover do Windows Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [Elevada disponibilidade](sql-database-high-availability.md) está incluído com cada base de dados. Recuperação após desastre é abordada em [descrição geral da continuidade do negócio com a base de dados do Azure SQL](sql-database-business-continuity.md) | [Elevada disponibilidade](sql-database-high-availability.md) está incluído com cada base de dados. Recuperação após desastre é abordada em [descrição geral da continuidade do negócio com a base de dados do Azure SQL](sql-database-business-continuity.md) |
| [Índices XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Sim | Sim |

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre o serviço Base de Dados SQL do Azure, veja [O que é a Base de Dados SQL?](sql-database-technical-overview.md)
- Para obter informações sobre uma instância gerida, veja [o que é uma instância gerida?](sql-database-managed-instance.md).
