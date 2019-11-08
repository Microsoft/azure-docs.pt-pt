---
title: Como configurar um banco de dados individual
description: Saiba como configurar e gerenciar o banco de dados SQL do Azure-banco de dados individual
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 02/08/2019
ms.openlocfilehash: 3c03797c97a354eb06b6c3b1d76ba5c39d512c18
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811208"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Como usar um banco de dados individual no banco de dados SQL do Azure

Nesta seção, você pode encontrar vários guias, scripts e explicações que podem ajudá-lo a gerenciar e configurar seu banco de dados individual no banco de dados SQL do Azure

## <a name="migrate"></a>Migrar

- [Migrar para o banco de dados SQL](sql-database-single-database-migrate.md) – saiba mais sobre o processo de migração e as ferramentas recomendadas para migração para uma instância gerenciada.
- Saiba como [gerenciar o banco de dados SQL após a migração](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Configurar funcionalidades

- [Configure a replicação transacional](replication-to-sql-database.md) para replicar sua data entre bancos de dados.
- [Configure a detecção de ameaças](sql-database-threat-detection.md) para permitir que o banco de dados SQL do Azure identifique atividades suspeitas, como injeção de SQL ou acesso de locais suspeitos.
- [Configure a máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started-portal.md) para proteger seus dados confidenciais.
- [Configure a retenção de backup](sql-database-long-term-backup-retention-configure.md) para um banco de dados para manter seus backups no armazenamento de BLOBs do Azure. Como alternativa, há a abordagem [Configurar a retenção de backup usando o cofre do Azure (preterido)](sql-database-long-term-backup-retention-configure-vault.md) .
- [Configure a replicação geográfica](sql-database-geo-replication-portal.md) para manter uma réplica do seu banco de dados em outra região.
- [Configure a segurança para réplicas geográficas](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Monitorar e ajustar seu banco de dados

- [Habilite o ajuste automático](sql-database-automatic-tuning-enable.md) para permitir que o banco de dados SQL do Azure Otimize o desempenho da carga de trabalho.
- [Habilite notificações por email para ajuste automático](sql-database-automatic-tuning-email-notifications.md) para obter informações sobre as recomendações de ajuste.
- [Aplique recomendações de desempenho](sql-database-advisor-portal.md) e Otimize seu banco de dados.
- [Crie alertas](sql-database-insights-alerts-portal.md) para obter notificações do banco de dados SQL do Azure.
- [Solucione problemas de conectividade](sql-database-troubleshoot-common-connection-issues.md) se você observar alguns problemas de conectividade entre os aplicativos e o banco de dados. Você também pode usar [Resource Health para problemas de conectividade](sql-database-resource-health.md).
- [Gerencie o espaço de arquivo](sql-database-file-space-management.md) para monitorar o uso de armazenamento em seu banco de dados.

## <a name="query-distributed-data"></a>Consultar dados distribuídos

- [Consultar dados particionados verticalmente](sql-database-elastic-query-getting-started-vertical.md) em vários bancos de dado.
- [Relatório na camada de dados escalada](sql-database-elastic-query-horizontal-partitioning.md)horizontalmente.
- [Consulte entre tabelas com esquemas diferentes](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Tarefas de Base de Dados Elástica

- [Criar e gerenciar](elastic-jobs-powershell.md) Trabalhos de banco de dados elástico usando o PowerShell.
- [Criar e gerenciar](elastic-jobs-tsql.md) Trabalhos de banco de dados elástico usando o Transact-SQL.
- [Migre do antigo trabalho elástico](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Fragmentação de base de dados

- [Atualizar biblioteca de cliente do banco de dados elástico](sql-database-elastic-scale-upgrade-client-library.md).
- [Criar aplicativo fragmentado](sql-database-elastic-scale-get-started.md).
- [Consultar dados fragmentados horizontalmente](sql-database-elastic-query-getting-started.md).
- Executar [consultas de vários fragmentos](sql-database-elastic-scale-multishard-querying.md).
- [Mover dados fragmentados](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Configure a segurança](sql-database-elastic-scale-split-merge-security-configuration.md) em fragmentos de banco de dados.
- [Adicione um fragmento](sql-database-elastic-scale-add-a-shard.md) ao conjunto atual de fragmentos de banco de dados.
- [Corrigir problemas de mapa de fragmentos](sql-database-elastic-database-recovery-manager.md).
- [Migre o banco de BD fragmentado](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Criar contadores](sql-database-elastic-database-perf-counters.md).
- [Use o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) para consultar dados fragmentados.
- [Use a estrutura Dapper](sql-database-elastic-scale-working-with-dapper.md) para consultar dados fragmentados.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os [guias de instruções para a instância gerenciada](sql-database-howto-managed-instance.md)
