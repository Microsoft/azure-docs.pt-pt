---
title: Configurar & gerir referência de conteúdo
description: Encontre uma referência de conteúdo que o ensine a configurar e gerir a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 19cec1deeef344ea5897061db0fec88066b90b05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92791771"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>Configurar e gerir referência de conteúdo - Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste artigo pode encontrar uma referência de conteúdo de vários guias, scripts e explicações que podem ajudá-lo a gerir e configurar a sua Base de Dados Azure SQL. 

## <a name="load-data"></a>Carregar dados

- [Migrar para a Base de Dados SQL](migrate-to-database-from-sql-server.md)
- Saiba como gerir a [Base de Dados SQL após a migração.](manage-data-after-migrating-to-database.md)
- [Copiar uma Base de Dados](database-copy.md)
- [Importar uma BD de um BACPAC](database-import.md)
- [Exportar uma BD de um BACPAC](database-export.md)
- [Carregar dados com o BCP](../load-from-csv-with-bcp.md)
- [Carregar dados com o ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>Configurar funcionalidades

- [Configure Azure Ative Directory (Azure AD) auth](authentication-aad-configure.md)
- [Configure acesso condicional](conditional-access-configure.md)
- [Autenticação multifator do Azure AD](authentication-mfa-ssms-overview.md)
- [Configurar a autenticação multi-factor](authentication-mfa-ssms-configure.md)
- [Configurar a política de retenção temporal](temporal-tables-retention-policy.md)
- [Configurar o TDE com o BYOK](transparent-data-encryption-byok-configure.md)
- [Rodar chaves BYOK de TDE](transparent-data-encryption-byok-key-rotation.md)
- [Remover o protetor de TDE](transparent-data-encryption-byok-remove-tde-protector.md)
- [Configurar o OLTP dentro da memória](../in-memory-oltp-configure.md)
- [Configurar a azure Automation](automation-manage.md)
- [Configure a replicação transacional](replication-to-sql-database.md) para replicar a sua data entre bases de dados.
- [Configure](threat-detection-configure.md) a deteção de ameaças para permitir que a Base de Dados Azure SQL identifique atividades suspeitas, como injeção de SQL ou acesso a locais suspeitos.
- [Configure uma máscara dinâmica de dados](dynamic-data-masking-configure-portal.md) para proteger os seus dados sensíveis.
- [Configure a retenção de backup](long-term-backup-retention-configure.md) para uma base de dados para manter as suas cópias de segurança no Azure Blob Storage. 
- [Configure a geo-replicação](active-geo-replication-overview.md) para manter uma réplica da sua base de dados noutra região.
- [Configure a segurança para as geo-réplicas.](active-geo-replication-security-configure.md)

## <a name="monitor-and-tune-your-database"></a>Monitorize e afina a sua base de dados

- [Ajuste manual](performance-guidance.md)
- [Utilizar DMVs para monitorizar o desempenho](monitoring-with-dmvs.md)
- [Utilizar o Arquivo de consultas para monitorizar o desempenho](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Ative a sintonização automática](automatic-tuning-enable.md) para permitir que a Base de Dados Azure SQL otimize o desempenho da sua carga de trabalho.
- [Habilita notificações por e-mail para sintonização automática](automatic-tuning-email-notifications-configure.md) para obter informações sobre recomendações de sintonização.
- [Aplique recomendações de desempenho](database-advisor-find-recommendations-portal.md) e otimize a sua base de dados.
- [Crie alertas](alerts-insights-configure-portal.md) para obter notificações da Base de Dados Azure SQL.
- [A conectividade de resolução de problemas](troubleshoot-common-errors-issues.md) se notar alguns problemas de conectividade entre as aplicações e a base de dados. Também pode utilizar [a Saúde dos Recursos para problemas de conectividade.](resource-health-to-troubleshoot-connectivity.md)
- [Resolução de problemas de desempenho com o Intelligent Insights](intelligent-insights-troubleshoot-performance.md)
- [Gerencie o espaço de ficheiros](file-space-manage.md) para monitorizar o uso do armazenamento na sua base de dados.
- [Utilizar registo de diagnóstico do Intelligent Insights](intelligent-insights-use-diagnostics-log.md)
- [Monitorizar o espaço OLTP dentro da memória](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Eventos expandidos

- [Eventos expandidos](xevent-db-diff-from-svr.md)
- [Armazenar eventos estendidos em arquivo de eventos](xevent-code-event-file.md)
- [Armazenar eventos estendidos em tampão de anel](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>Consultar dados distribuídos

- [Consulta dados divisórias verticalmente divididos](elastic-query-getting-started-vertical.md) em várias bases de dados.
- [Relatório através do nível de dados escalonado](elastic-query-horizontal-partitioning.md).
- [Consulta através de tabelas com esquemas diferentes.](elastic-query-vertical-partitioning.md)

### <a name="data-sync"></a>Sincronização de dados

- [Sincronização de Dados SQL](sql-data-sync-data-sql-server-sql-database.md)
- [Agente de Sincronização de Dados](sql-data-sync-agent-overview.md)
- [Replicar as alterações do esquema](sql-data-sync-update-sync-schema.md)
- [Monitorizar com o OMS](./monitor-tune-overview.md)
- [Melhores práticas para a Sincronização de Dados](sql-data-sync-best-practices.md)
- [Resolver problemas de Sincronização de Dados](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Tarefas de Base de Dados Elástica

- [Criar e gerir](elastic-jobs-powershell-create.md) Trabalhos de base de dados elásticos usando PowerShell.
- [Criar e gerir](elastic-jobs-tsql-create-manage.md) Trabalhos de base de dados elásticos utilizando o Transact-SQL.
- [Migrar do antigo trabalho elástico.](elastic-jobs-migrate.md)

## <a name="database-sharding"></a>Fragmentação de base de dados

- [Atualizar biblioteca de clientes de base de dados elásticas.](elastic-scale-upgrade-client-library.md)
- [Criar aplicativo sharded](elastic-scale-get-started.md).
- [Consulta dados horizontalmente fragmentos](elastic-query-getting-started.md).
- Executar [consultas multi-fragmentos](elastic-scale-multishard-querying.md).
- [Mover dados fragmentos.](elastic-scale-configure-deploy-split-and-merge.md)
- [Configure a segurança](elastic-scale-split-merge-security-configuration.md) em fragmentos de base de dados.
- [Adicione um fragmento](elastic-scale-add-a-shard.md) ao conjunto atual de fragmentos de base de dados.
- [Corrija problemas de mapa de fragmentos.](elastic-database-recovery-manager.md)
- [Migrar DB de caco.](elastic-convert-to-use-elastic-tools.md)
- [Criar balcões](elastic-database-perf-counters.md).
- [Utilize](elastic-scale-use-entity-framework-applications-visual-studio.md) a estrutura da entidade para consultar dados fragmentos.
- [Use a estrutura Dapper](elastic-scale-working-with-dapper.md) para consultar dados fragmentos.

## <a name="develop-applications"></a>Programar aplicações

- [Conetividade](connect-query-content-reference-guide.md#libraries)
- [Utilizar o Conector do Spark](spark-connector.md)
- [Autenticar aplicação](application-authentication-get-client-id-keys.md)
- [Utilize o lote para um melhor desempenho](../performance-improve-use-batching.md)
- [Orientações para conectividade](troubleshoot-common-connectivity-issues.md)
- [Aliases DNS](dns-alias-overview.md)
- [Configuração DNS pseudónimo PowerShell](dns-alias-powershell-create.md)
- [Portas - ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C e C ++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>Conceber aplicações

- [Conceber para a recuperação após desastre](designing-cloud-solutions-for-disaster-recovery.md)
- [Conceção para conjuntos elásticos](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Conceber para atualizações da aplicação](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>Design software multi-inquilino como um serviço (SaaS) aplicações

- [Padrões de design de SaaS](saas-tenancy-app-design-patterns.md)
- [Indexador de vídeos de SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Segurança de aplicação SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [como fazer guias para Azure SQL Managed Instance](../managed-instance/how-to-content-reference-guide.md)