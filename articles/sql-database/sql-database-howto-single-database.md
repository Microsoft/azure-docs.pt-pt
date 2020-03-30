---
title: Como configurar uma única base de dados
description: Saiba como configurar e gerir a Base de Dados Azure SQL - base de dados única
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 01/14/2020
ms.openlocfilehash: 4283e1a2c92b7fe738fb57a8103cea1deb0015a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76027710"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Como utilizar uma única base de dados na Base de Dados Azure SQL

Nesta secção pode encontrar vários guias, scripts e explicações que podem ajudá-lo a gerir e configurar a sua única base de dados na Base de Dados Azure SQL

## <a name="migrate"></a>Migrar

- Migrar para a Base de [Dados SQL](sql-database-single-database-migrate.md) – Conheça o processo de migração recomendado e ferramentas para a migração para um caso gerido.
- Saiba como gerir a base de [dados SQL após](sql-database-manage-after-migration.md)a migração .

## <a name="configure-features"></a>Configurar funcionalidades

- [Configure a replicação transacional](replication-to-sql-database.md) para replicar a sua data entre bases de dados.
- [Configure](sql-database-threat-detection.md) a deteção de ameaças para permitir que a Base de Dados Azure SQL identifique atividades suspeitas como a Injeção SQL ou o acesso a partir de locais suspeitos.
- [Configure](sql-database-dynamic-data-masking-get-started-portal.md) a máscara de dados dinâmicos para proteger os seus dados sensíveis.
- [Configure](sql-database-long-term-backup-retention-configure.md) a retenção de cópia de segurança para uma base de dados para manter as suas cópias de segurança no Armazenamento De Blob Azure. Como alternativa, existe a retenção de backup Configure utilizando a abordagem [do cofre Azure (depreciado).](sql-database-long-term-backup-retention-configure-vault.md)
- [Configure a geo-replicação](sql-database-geo-replication-portal.md) para manter uma réplica da sua base de dados noutra região.
- [Configure a segurança para geo-réplicas](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Monitorize e sintonize a sua base de dados

- Ative a [sintonização automática](sql-database-automatic-tuning-enable.md) para permitir que a Base de Dados Azure SQL otimize o desempenho da sua carga de trabalho.
- [Ativar notificações de e-mail para afinação automática](sql-database-automatic-tuning-email-notifications.md) para obter informações sobre recomendações de afinação.
- [Aplique recomendações](sql-database-advisor-portal.md) de desempenho e otimize a sua base de dados.
- [Crie alertas](sql-database-insights-alerts-portal.md) para receber notificações da Base de Dados Azure SQL.
- [Problemas de conectividade](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md) se notar alguns problemas de conectividade entre as aplicações e a base de dados. Também pode utilizar a Saúde dos [Recursos para problemas](sql-database-resource-health.md)de conectividade.
- [Gerencie o espaço](sql-database-file-space-management.md) de ficheiros para monitorizar o uso do armazenamento na sua base de dados.

## <a name="query-distributed-data"></a>Consultar dados distribuídos

- [Consulta de dados verticalmente divididos](sql-database-elastic-query-getting-started-vertical.md) em várias bases de dados.
- [Relatório através do nível de dados escaldados.](sql-database-elastic-query-horizontal-partitioning.md)
- [Consulta através das mesas com diferentes schemas.](sql-database-elastic-query-vertical-partitioning.md)

## <a name="elastic-database-jobs"></a>Tarefas de Base de Dados Elástica

- [Criar e gerir](elastic-jobs-powershell.md) Trabalhos de base de dados elásticos usando powerShell.
- [Criar e gerir](elastic-jobs-tsql.md) Trabalhos de base de dados elásticos usando Transact-SQL.
- [Migrar do antigo trabalho elástico.](elastic-jobs-migrate.md)

## <a name="database-sharding"></a>Fragmentação de base de dados

- Atualizar biblioteca de clientes de base de [dados elástica.](sql-database-elastic-scale-upgrade-client-library.md)
- [Criar uma aplicação de sharded](sql-database-elastic-scale-get-started.md).
- [Consulta de dados horizontalmente esfumaçados](sql-database-elastic-query-getting-started.md).
- Executar [consultas multi-fragmentos.](sql-database-elastic-scale-multishard-querying.md)
- [Mova dados fragmentos.](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
- [Configure a segurança](sql-database-elastic-scale-split-merge-security-configuration.md) nos fragmentos de base de dados.
- [Adicione um fragmento](sql-database-elastic-scale-add-a-shard.md) ao conjunto atual de fragmentos de base de dados.
- [Corrigir problemas](sql-database-elastic-database-recovery-manager.md)no mapa do fragmento.
- [Migrar DB espumoso.](sql-database-elastic-convert-to-use-elastic-tools.md)
- [Criar balcões.](sql-database-elastic-database-perf-counters.md)
- [Utilize](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) a estrutura da entidade para consultar dados fragmentos.
- [Use](sql-database-elastic-scale-working-with-dapper.md) a estrutura dapper para consultar dados fragmentos.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [guias como fazer para o exemplo gerido](sql-database-howto-managed-instance.md)
