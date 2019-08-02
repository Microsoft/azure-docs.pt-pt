---
title: Como configurar o banco de dados SQL do Azure | Microsoft Docs
description: Saiba como configurar e gerenciar o banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 98c522f3d2718691e32b4e78180027b69f28289f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568012"
---
# <a name="how-to-use-azure-sql-database"></a>Como usar o banco de dados SQL do Azure

Nesta seção, você pode encontrar vários guias, scripts e explicações que podem ajudá-lo a gerenciar e configurar seu banco de dados SQL do Azure. Você também pode encontrar guias de instruções específicas para [um único banco de dados](sql-database-howto-single-database.md) e [instância gerenciada](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Carregar dados

- [Copiar um banco de dados individual ou um banco de dados em pool no Azure](sql-database-copy.md)
- [Importar um BD de um BACPAC](sql-database-import.md)
- [Exportar um BD para BACPAC](sql-database-export.md)
- [Carregar dados com o BCP](sql-database-load-from-csv-with-bcp.md)
- [Carregar dados com o ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Sincronização de dados

- [Sincronização de Dados SQL](sql-database-sync-data.md)
- [Agente de sincronização de dados](sql-database-data-sync-agent.md)
- [Replicar alterações de esquema](sql-database-update-sync-schema.md)
- [Monitorizar com o OMS](sql-database-sync-monitor-oms.md)
- [Práticas recomendadas para sincronização de dados](sql-database-best-practices-data-sync.md)
- [Solucionar problemas de sincronização de dados](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Monitorização e otimização

- [Ajuste manual](sql-database-performance-guidance.md)
- [Usar DMVs para monitorar o desempenho](sql-database-monitoring-with-dmvs.md)
- [Usar o repositório de consultas para monitorar o desempenho](sql-database-operate-query-store.md)
- [Solucionar problemas de desempenho com Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Usar Intelligent Insights log de diagnóstico](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Monitorar o espaço OLTP na memória](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Eventos expandidos

- [Eventos expandidos](sql-database-xevent-db-diff-from-svr.md)
- [Armazenar eventos estendidos no arquivo de evento](sql-database-xevent-code-event-file.md)
- [Armazenar eventos estendidos no buffer de anéis](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Configurar funcionalidades

- [Configurar a autenticação do Azure AD](sql-database-aad-authentication-configure.md)
- [Configurar o acesso condicional](sql-database-conditional-access.md)
- [Autenticação do AAD multifator](sql-database-ssms-mfa-authentication.md)
- [Configurar autenticação multifator](sql-database-ssms-mfa-authentication-configure.md)
- [Configurar a política de retenção temporal](sql-database-temporal-tables-retention-policy.md)
- [Configurar o TDE com BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Girar chaves de BYOK TDE](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Remover o protetor de TDE](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Configurar o OLTP dentro da memória](sql-database-in-memory-oltp-migration.md)
- [Configurar a automação do Azure](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Programar aplicações

- [Conetividade](sql-database-libraries.md)
- [Usar o conector do Spark](sql-database-spark-connector.md)
- [Autenticar aplicativo](sql-database-client-id-keys.md)
- [Mensagens de erro](sql-database-develop-error-messages.md)
- [Usar o envio em lote para melhorar o desempenho](sql-database-use-batching-to-improve-performance.md)
- [Orientações para conectividade](sql-database-connectivity-issues.md)
- [Aliases de DNS](dns-alias-overview.md)
- [Configurar PowerShell do alias DNS](dns-alias-powershell.md)
- [Portas - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C e C++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Conceber aplicações

- [Conceber para a recuperação após desastre](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Design para pools elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Design para atualizações de aplicativo](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Criar aplicativos SaaS de vários locatários

- [Padrões de design de SaaS](saas-tenancy-app-design-patterns.md)
- [Indexador de vídeo SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Segurança de aplicativo SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os [guias de instruções para instâncias gerenciadas](sql-database-howto-managed-instance.md).
- Saiba mais sobre [os guias de instruções para bancos de dados individuais](sql-database-howto-single-database.md).
