---
title: Como configurar a base de dados SQL do Azure - único | Documentos da Microsoft
description: Saiba como configurar e gerir o Azure SQL Database - base de dados
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 4c6799372f203f021a07ae52a1d7f591aae5afad
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770839"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Como utilizar uma base de dados na base de dados do Azure SQL

Nesta seção encontrará várias guias, scripts e explicações que podem ajudar a gerir e configurar a sua base de dados na base de dados do Azure SQL

## <a name="migrate"></a>Migrar

- [Migrar para a base de dados SQL](sql-database-single-database-migrate.md) – Saiba mais sobre o processo de migração recomendado e ferramentas para a migração para uma instância gerida.
- Saiba como [gerir a base de dados SQL após a migração](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Configurar funcionalidades

- [Configurar a replicação transacional](replication-to-sql-database.md) para replicar a data entre bases de dados.
- [Configurar a deteção de ameaças](sql-database-threat-detection.md) para permitir que a base de dados do SQL Azure identificar atividades suspeitas, como Injeção de SQL ou o acesso a partir de localizações suspeitas.
- [Configurar a máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started-portal.md) para proteger os dados confidenciais.
- [Configurar a retenção de cópia de segurança](sql-database-long-term-backup-retention-configure.md) para uma base de dados manter as cópias de segurança no armazenamento de Blobs do Azure. Como alternativa há [configurar a retenção de cópia de segurança com o cofre do Azure (preterido)](sql-database-long-term-backup-retention-configure-vault.md) abordagem.
- [Configurar georreplicação](sql-database-geo-replication-portal.md) para manter uma réplica da base de dados noutra região.
- [Configurar a segurança para réplicas geo](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Monitorizar e otimizar a sua base de dados

- [Ativar o ajuste automático](sql-database-automatic-tuning-enable.md) para permitir que a base de dados do SQL Azure otimizar o desempenho da carga de trabalho.
- [Ativar notificações por correio eletrónico para a otimização automática](sql-database-automatic-tuning-email-notifications.md) para obter informações sobre recomendações de otimização.
- [Aplicar recomendações de desempenho](sql-database-advisor-portal.md) e otimizar a sua base de dados.
- [Criar alertas](sql-database-insights-alerts-portal.md) para receber notificações a partir da base de dados do Azure SQL.
- [Resolver problemas de conectividade](sql-database-troubleshoot-common-connection-issues.md) se observar alguns problemas de conectividade entre os aplicativos e a base de dados. Também pode utilizar [estado de funcionamento de recursos para problemas de conectividade](sql-database-resource-health.md).
- [Gerir o espaço de ficheiro](sql-database-file-space-management.md) para monitorizar a utilização de armazenamento na sua base de dados.

## <a name="query-distributed-data"></a>Consultar dados distribuídos

- [Consultar dados particionados verticalmente](sql-database-elastic-query-getting-started-vertical.md) entre várias bases de dados.
- [Relatório entre a camada de dados aumentadas horizontalmente](sql-database-elastic-query-horizontal-partitioning.md).
- [Consultas em tabelas com esquemas diferentes](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Tarefas de Base de Dados Elástica

- [Criar e gerir](elastic-jobs-powershell.md) tarefas elásticas da base de dados com o PowerShell.
- [Criar e gerir](elastic-jobs-tsql.md) tarefas elásticas da base de dados com o Transact-SQL.
- [Migrar de tarefa elástica antiga](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Fragmentação de base de dados

- [Biblioteca de cliente de atualização de bases de dados elásticas](sql-database-elastic-scale-upgrade-client-library.md).
- [Criar aplicação em partição horizontal](sql-database-elastic-scale-get-started.md).
- [Consultar dados fragmentados horizontalmente](sql-database-elastic-query-getting-started.md).
- Execute [consultas de vários fragmentos](sql-database-elastic-scale-multishard-querying.md).
- [Mover dados em partição horizontal](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Configurar a segurança](sql-database-elastic-scale-split-merge-security-configuration.md) em partições horizontais da base de dados.
- [Adicionar uma partição horizontal](sql-database-elastic-scale-add-a-shard.md) para o conjunto atual de partições horizontais da base de dados.
- [Corrigir problemas de mapa de partições horizontais](sql-database-elastic-database-recovery-manager.md).
- [Migrar BD em partição horizontal](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Criar contadores](sql-database-elastic-database-perf-counters.md).
- [Estrutura de entidades de utilização](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) para consultar dados em partição horizontal.
- [Framework de usar o Dapper](sql-database-elastic-scale-working-with-dapper.md) para consultar dados em partição horizontal.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [guias de procedimentos para a instância gerida](sql-database-howto-managed-instance.md)
