---
title: Configure & gerir a referência do conteúdo
description: Encontre uma referência de conteúdo que o ensine a configurar e gerir a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 01/14/2020
ms.openlocfilehash: 6f7c90791025f14e743a83693503fe235792c603
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051956"
---
# <a name="configure--manage-content-reference---azure-sql-database"></a>Configure & gerir referência de conteúdo - Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste artigo pode encontrar uma referência de conteúdo de vários guias, scripts e explicações que podem ajudá-lo a gerir e configurar a sua Base de Dados Azure SQL. 

## <a name="load-data"></a>Carregar dados

- [Migrar para base de dados SQL](migrate-to-database-from-sql-server.md)
- Saiba como gerir a base de [dados SQL após](manage-data-after-migrating-to-database.md)a migração .
- [Copiar uma Base de Dados](database-copy.md)
- [Importar uma BD de um BACPAC](database-import.md)
- [Exportar uma BD de um BACPAC](database-export.md)
- [Carregar dados com o BCP](../load-from-csv-with-bcp.md)
- [Carregar dados com o ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>Configurar funcionalidades

- [Configurar a autenticação do Azure AD](authentication-aad-configure.md)
- [Configurar acesso condicional](conditional-access-configure.md)
- [Autenticação multifator do AAD](authentication-mfa-ssms-overview.md)
- [Configurar a autenticação multifator](authentication-mfa-ssms-configure.md)
- [Configurar a política de retenção temporal](temporal-tables-retention-policy.md)
- [Configurar o TDE com o BYOK](transparent-data-encryption-byok-configure.md)
- [Rodar chaves BYOK de TDE](transparent-data-encryption-byok-key-rotation.md)
- [Remover o protetor de TDE](transparent-data-encryption-byok-remove-tde-protector.md)
- [Configurar o OLTP dentro da memória](../in-memory-oltp-configure.md)
- [Configure Automação Azure](automation-manage.md)
- [Configure a replicação transacional](replication-to-sql-database.md) para replicar a sua data entre bases de dados.
- [Configure](threat-detection-configure.md) a deteção de ameaças para permitir que a Base de Dados Azure SQL identifique atividades suspeitas como a Injeção SQL ou o acesso a partir de locais suspeitos.
- [Configure](dynamic-data-masking-configure-portal.md) a máscara de dados dinâmicos para proteger os seus dados sensíveis.
- [Configure](long-term-backup-retention-configure.md) a retenção de cópia de segurança para uma base de dados para manter as suas cópias de segurança no Armazenamento De Blob Azure. 
- [Configure a geo-replicação](active-geo-replication-overview.md) para manter uma réplica da sua base de dados noutra região.
- [Configure a segurança para geo-réplicas](active-geo-replication-security-configure.md).

## <a name="monitor-and-tune-your-database"></a>Monitorize e sintonize a sua base de dados

- [Ajuste manual](performance-guidance.md)
- [Utilizar DMVs para monitorizar o desempenho](monitoring-with-dmvs.md)
- [Utilizar o Arquivo de consultas para monitorizar o desempenho](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- Ative a [sintonização automática](automatic-tuning-enable.md) para permitir que a Base de Dados Azure SQL otimize o desempenho da sua carga de trabalho.
- [Ativar notificações de e-mail para afinação automática](automatic-tuning-email-notifications-configure.md) para obter informações sobre recomendações de afinação.
- [Aplique recomendações](database-advisor-find-recommendations-portal.md) de desempenho e otimize a sua base de dados.
- [Crie alertas](alerts-insights-configure-portal.md) para receber notificações da Base de Dados Azure SQL.
- [Problemas de conectividade](troubleshoot-common-errors-issues.md) se notar alguns problemas de conectividade entre as aplicações e a base de dados. Também pode utilizar a Saúde dos [Recursos para problemas](resource-health-to-troubleshoot-connectivity.md)de conectividade.
- [Resolução de problemas de desempenho com o Intelligent Insights](intelligent-insights-troubleshoot-performance.md)
- [Gerencie o espaço](file-space-manage.md) de ficheiros para monitorizar o uso do armazenamento na sua base de dados.
- [Utilizar registo de diagnóstico do Intelligent Insights](intelligent-insights-use-diagnostics-log.md)
- [Monitorizar o espaço OLTP dentro da memória](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Eventos expandidos

- [Eventos expandidos](xevent-db-diff-from-svr.md)
- [Armazenar eventos estendidos em arquivo de eventos](xevent-code-event-file.md)
- [Armazenar eventos estendidos em tampão de anel](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>Consultar dados distribuídos

- [Consulta de dados verticalmente divididos](elastic-query-getting-started-vertical.md) em várias bases de dados.
- [Relatório através do nível de dados escaldados.](elastic-query-horizontal-partitioning.md)
- [Consulta através das mesas com diferentes schemas.](elastic-query-vertical-partitioning.md)

### <a name="data-sync"></a>Sincronização de dados

- [Sincronização de Dados SQL](sql-data-sync-data-sql-server-sql-database.md)
- [Agente de Sincronização de Dados](sql-data-sync-agent-overview.md)
- [Replicar as alterações do esquema](sql-data-sync-update-sync-schema.md)
- [Monitorizar com o OMS](sql-data-sync-monitor-sync.md)
- [Melhores práticas para a Sincronização de Dados](sql-data-sync-best-practices.md)
- [Resolver problemas de Sincronização de Dados](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Tarefas de Base de Dados Elástica

- [Criar e gerir](elastic-jobs-powershell-create.md) Trabalhos de base de dados elásticos usando powerShell.
- [Criar e gerir](elastic-jobs-tsql-create-manage.md) Trabalhos de base de dados elásticos usando Transact-SQL.
- [Migrar do antigo trabalho elástico.](elastic-jobs-migrate.md)

## <a name="database-sharding"></a>Fragmentação de base de dados

- Atualizar biblioteca de clientes de base de [dados elástica.](elastic-scale-upgrade-client-library.md)
- [Criar uma aplicação de sharded](elastic-scale-get-started.md).
- [Consulta de dados horizontalmente esfumaçados](elastic-query-getting-started.md).
- Executar [consultas multi-fragmentos.](elastic-scale-multishard-querying.md)
- [Mova dados fragmentos.](elastic-scale-configure-deploy-split-and-merge.md)
- [Configure a segurança](elastic-scale-split-merge-security-configuration.md) nos fragmentos de base de dados.
- [Adicione um fragmento](elastic-scale-add-a-shard.md) ao conjunto atual de fragmentos de base de dados.
- [Corrigir problemas](elastic-database-recovery-manager.md)no mapa do fragmento.
- [Migrar DB espumoso.](elastic-convert-to-use-elastic-tools.md)
- [Criar balcões.](elastic-database-perf-counters.md)
- [Utilize](elastic-scale-use-entity-framework-applications-visual-studio.md) a estrutura da entidade para consultar dados fragmentos.
- [Use](elastic-scale-working-with-dapper.md) a estrutura dapper para consultar dados fragmentos.

## <a name="develop-applications"></a>Programar aplicações

- [Conetividade](connect-query-content-reference-guide.md#libraries)
- [Utilizar o Conector do Spark](spark-connector.md)
- [Autenticar aplicação](application-authentication-get-client-id-keys.md)
- [Utilize lotes para um melhor desempenho](../performance-improve-use-batching.md)
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

### <a name="design-multi-tenant-saas-applications"></a>Design Aplicações SaaS multi-inquilinos

- [Padrões de design de SaaS](saas-tenancy-app-design-patterns.md)
- [Indexador de vídeos de SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Segurança de aplicação SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Próximos passos
- Saiba mais sobre [guias de Como fazer para A Instância Gerida SQL](../managed-instance/how-to-content-reference-guide.md)
