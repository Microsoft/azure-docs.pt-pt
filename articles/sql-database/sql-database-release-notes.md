---
title: Notas de versão do banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre os novos recursos e aprimoramentos no serviço de banco de dados SQL do Azure e na documentação do banco de dados SQL do Azure
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: ed41ccea0754f3eeffdd0248bac567859db1492c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001515"
---
# <a name="sql-database-release-notes"></a>Notas de versão do banco de dados SQL

Este artigo lista os recursos do banco de dados SQL que estão atualmente em visualização pública. Para atualizações e aprimoramentos do banco de dados SQL, consulte [atualizações do serviço de banco de dados SQL](https://azure.microsoft.com/updates/?product=sql-database). Para obter atualizações e aprimoramentos em outros serviços do Azure, consulte [atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Recursos na visualização pública

### <a name="single-databasetabsingle-database"></a>[Banco de dados individual](#tab/single-database)

| Funcionalidade | Detalhes |
| ---| --- |
| Recuperação de banco de dados acelerada com bancos de dados individuais e pools elásticos | Para obter informações, consulte [recuperação de banco de dados acelerada](sql-database-accelerated-database-recovery.md).|
|Contagem aproximada distinta|Para obter informações, veja [contagem aproximada distinta](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modo de lote no repositório de armazenamento (no nível de compatibilidade 150)|Para obter informações, consulte [modo de lote no repositório de armazenamento](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Deteção e classificação de dados  |Para saber mais, confira [banco de dados SQL do Azure e SQL data warehouse a descoberta de Data & classificação](sql-database-data-discovery-and-classification.md).|
| Tarefas de base de dados elástica | Para obter informações, consulte [criar, configurar e gerenciar trabalhos elásticos](elastic-jobs-overview.md). |
| Consultas elásticas | Para obter informações, consulte [visão geral de consulta elástica](sql-database-elastic-query-overview.md). |
| Transações elásticas | [Transações distribuídas entre bancos de dados na nuvem](sql-database-elastic-transactions-overview.md). |
|Comentários de concessão de memória (modo de linha) (em nível de compatibilidade 150)|Para obter informações, consulte [comentários de concessão de memória (modo de linha)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor de consultas no portal do Azure |Para obter informações, consulte [usar o editor de consultas SQL do portal do Azure para se conectar e consultar dados](sql-database-connect-query-portal.md).|
| Serviços de R/Machine Learning com bancos de dados individuais e pools elásticos |Para obter informações, consulte [serviços de Machine Learning no banco de dados SQL do Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Escalão de serviço de computação sem servidor | Para obter informações, consulte [banco de dados SQL sem servidor (visualização)](sql-database-serverless.md).|
|Análise de SQL|Para obter informações, consulte [análise de SQL do Azure](../azure-monitor/insights/azure-sql.md).|
|Compilação adiada da variável de tabela (no nível de compatibilidade 150)|Para obter informações, consulte [compilação adiada da variável de tabela](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Instância Gerenciada](#tab/managed-instance)

| Funcionalidade | Detalhes |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pools de instância</a> | Uma maneira conveniente e econômica de migrar instâncias SQL menores para a nuvem. |
| <a href="https://aka.ms/managed-instance-tde-byok">TDE (Transparent Data Encryption) com Bring Your Own Key (BYOK)</a> |Para obter informações, [consulte Azure SQL Transparent Data Encryption com chaves gerenciadas pelo cliente no Azure Key Vault: Suporte](transparent-data-encryption-byok-azure-sql.md)a Bring your own Key.|
| <a href="https://aka.ms/managed-instance-failover-groups">Grupos de failover distribuídos geograficamente</a> | Mantenha uma cópia da instância em outra região e verifique se seus dados estarão disponíveis mesmo no cenário de desastre regional. |
| <a href="https://aka.ms/managed-instance-aadlogins">Entidades de segurança de nível de instância do Azure AD Server (logons)</a> | Crie logons no nível de servidor usando a instrução <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create login from external Provider</a> . |
| [Replicação Transacional](sql-database-managed-instance-transactional-replication.md) | Replique as alterações de suas tabelas em outros bancos de dados colocados em instâncias gerenciadas, bancos de dados individuais ou instâncias de SQL Server, ou atualize suas tabelas quando algumas linhas forem alteradas em outras instâncias gerenciadas ou SQL Server instância. Para obter informações, consulte [Configurar replicação em um banco de dados de instância gerenciada do banco de dados SQL do Azure](replication-with-sql-database-managed-instance.md). |
| Deteção de ameaças |Para obter informações, consulte [Configurar a detecção de ameaças na instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-threat-detection.md).|
| Recriar bancos de dados descartados com instâncias gerenciadas |Para obter informações, consulte [recriar bancos de dados descartados no Azure SQL instância gerenciada](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="fixed-known-issues"></a>Correção de problemas conhecidos

- Os bancos de dados independentes de **agosto de 2019** têm suporte total na instância gerenciada.

## <a name="updates"></a>Atualizações

Para obter uma lista de atualizações e melhorias do banco de dados SQL, consulte [atualizações do serviço de banco de dados SQL](https://azure.microsoft.com/updates/?product=sql-database).

Para obter atualizações e aprimoramentos em todos os serviços do Azure, consulte [atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuir para conteúdo

Para contribuir com a documentação do banco de dados SQL do Azure, consulte o [Guia do colaborador do docs](https://docs.microsoft.com/contribute/).
