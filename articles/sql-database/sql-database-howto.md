---
title: Configurar e gerir
description: Saiba como configurar e gerir a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 11/14/2019
ms.openlocfilehash: c3f7b33e4b42b08334cfb687024985c878dc3713
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209438"
---
# <a name="how-to-use-azure-sql-database"></a>Como utilizar a Base de Dados Azure SQL

Nesta secção pode encontrar vários guias, scripts e explicações que podem ajudá-lo a gerir e configurar a sua Base de Dados Azure SQL. Pode também encontrar guias específicos para uma [única base](sql-database-howto-single-database.md) de dados e [instância gerida.](sql-database-howto-managed-instance.md)

## <a name="load-data"></a>Carregar dados

- [Copiar uma única base de dados ou base de dados agréis dentro do Azure](sql-database-copy.md)
- [Importar um DB de um BACPAC](sql-database-import.md)
- [Exportar um DB para bacpac](sql-database-export.md)
- [Carregar dados com o BCP](sql-database-load-from-csv-with-bcp.md)
- [Carregar dados com o ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Sincronização de dados

- [Sincronização de dados SQL](sql-database-sync-data.md)
- [Agente de sincronização de dados](sql-database-data-sync-agent.md)
- [Replicar alterações de esquema](sql-database-update-sync-schema.md)
- [Monitorizar com o OMS](sql-database-sync-monitor-oms.md)
- [Boas práticas para Data Sync](sql-database-best-practices-data-sync.md)
- [Sincronização de dados de resolução de problemas](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Monitorização e otimização

- [Afinação manual](sql-database-performance-guidance.md)
- [Use DMVs para monitorizar o desempenho](sql-database-monitoring-with-dmvs.md)
- [Utilize a loja de consultas para monitorizar o desempenho](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Desempenho de resolução de problemas com Insights Inteligentes](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Use o registo de diagnósticos De Insights Inteligentes](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Monitor Espaço OLTP na memória](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Eventos expandidos

- [Eventos expandidos](sql-database-xevent-db-diff-from-svr.md)
- [Armazenar eventos estendidos em arquivo de eventos](sql-database-xevent-code-event-file.md)
- [Armazenar eventos estendidos em tampão de anel](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Configurar funcionalidades

- [Configure Azure AUth](sql-database-aad-authentication-configure.md)
- [Configurar acesso condicional](sql-database-conditional-access.md)
- [AUth AAD multifactor](sql-database-ssms-mfa-authentication.md)
- [Configurar auth multifactor](sql-database-ssms-mfa-authentication-configure.md)
- [Configurar a política de retenção temporal](sql-database-temporal-tables-retention-policy.md)
- [Configure TDE com BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Rodar as teclas TDE BYOK](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Remover o protetor TDE](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Configurar o OLTP dentro da memória](sql-database-in-memory-oltp-migration.md)
- [Configure Automação Azure](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Programar aplicações

- [Conetividade](sql-database-libraries.md)
- [Utilizar o Conector de Faíscas](sql-database-spark-connector.md)
- [App autenticada](sql-database-client-id-keys.md)
- [Utilize lotes para um melhor desempenho](sql-database-use-batching-to-improve-performance.md)
- [Orientações para conectividade](sql-database-connectivity-issues.md)
- [Pseudónimos do DNS](dns-alias-overview.md)
- [Configuração DNS pseudónimo PowerShell](dns-alias-powershell.md)
- [Portas - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C e C++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Conceber aplicações

- [Conceber para a recuperação após desastre](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Design para piscinas elásticas](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Design para upgrades de aplicativos](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Design Aplicações SaaS multi-inquilinos

- [Padrões de design SaaS](saas-tenancy-app-design-patterns.md)
- [Indexador de vídeo SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Segurança de aplicativos SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os guias como fazer para casos geridos](sql-database-howto-managed-instance.md).
- Saiba mais sobre [os guias como fazer para bases de dados individuais](sql-database-howto-single-database.md).
