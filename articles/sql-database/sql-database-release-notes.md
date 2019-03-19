---
title: Notas de versão de base de dados SQL do Azure | Documentos da Microsoft
description: Saiba mais sobre as novas funcionalidades e melhorias no serviço de base de dados do Azure SQL e na documentação do SQL Database do Azure
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: carlrab
ms.openlocfilehash: 6600a578ba9c73c8a2c71466fd0b008f19058b80
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861299"
---
# <a name="sql-database-release-notes"></a>Notas de versão da base de dados SQL

Este artigo lista os novos recursos e melhorias no serviço de base de dados SQL e na documentação de base de dados SQL. Aprimoramentos do serviço de base de dados SQL, consulte também [atualizações de serviço de base de dados SQL](https://azure.microsoft.com/updates/?product=sql-database). Para aprimoramentos a outros serviços do Azure, consulte [as atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="march-2019"></a>Março de 2019

### <a name="service-improvements"></a>Melhorias de serviço

| Melhorias de serviço | Detalhes |
| --- | --- |
| Brevemente ||
| &nbsp; |

### <a name="documentation-improvements"></a>Melhorias de documentação

| Melhorias de documentação | Detalhes |
| --- | --- |
| Limites de registo foi adicionado para bases de dados individuais|Para obter mais informações, consulte [único limites de recursos do banco de dados vCore](sql-database-vcore-resource-limits-single-databases.md).|
| Limites de registo foi adicionado para conjuntos elásticos e bases de dados agrupadas|Para obter mais informações, consulte [limites dos recursos de vCore conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md).|
| Governação de taxa de registo de transação foi adicionada| Adicionados novos conteúdos [governação de taxa de registo de transação](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).|
| Exemplos de PowerShell atualizados para bases de dados individuais e conjuntos elásticos para utilizar o módulo de az.sql | Para obter mais informações, consulte [exemplos do PowerShell para conjuntos elásticos e bases de dados individuais](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>Fevereiro de 2019

### <a name="service-improvements"></a>Melhorias de serviço

| Melhorias de serviço | Detalhes |
| --- | --- |
|Criar uma recriação de índices online está agora em disponibilidade geral| Para obter mais informações, consulte [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Instância gerida de suporte para tabelas de rota melhorada| Para obter mais informações, consulte [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Mudar o nome de base de dados suportado na instância gerida | Para obter mais detalhes, consulte a [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) e [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) sintaxe.|
|Base de dados SQL como uma origem de dados de referência para o Stream Analytics. | Para obter mais informações, consulte [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/).|
|Assistência de migração de dados adiciona suporte para a instância gerida. |Para obter mais informações, consulte [o que há de novo no DMA](https://docs.microsoft.com/sql/dma/dma-whatsnew).|
|SQL Server Migration Assistant-adiciona suporte para avaliação de preparação do destino para a instância gerida. | Para obter mais informações, consulte [SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Serviço de migração de dados suporta a migração do Amazon RDS para instância gerida | Para obter mais informações, consulte [Tutorial: Migrar o servidor de SQL de RDS para a base de dados do Azure SQL ou uma base de dados do Azure SQL instância gerida online com o DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Melhorias de documentação

| Melhorias de documentação | Detalhes |
| --- | --- |
|Adicionar geridos esclarecimentos de opção de implementação de instância|Atualizar vários artigos para esclarecer a aplicabilidade da base de dados, o conjunto elástico e opções de implementação de instância gerida. |
|Tamanhos de tempdb atualizado para o modelo de compra baseado em DTU | Para obter mais informações, consulte [base de dados Tempdb na base de dados SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
|Atualizada a importação e exportação com o ficheiro bacpac para o suporte de instância gerida| Para obter mais informações, consulte [importação de BACPAC](sql-database-import.md) e [exportar para BACPAC](sql-database-export.md). |
| &nbsp; |


## <a name="january-2019"></a>Janeiro de 2019

### <a name="service-improvements"></a>Melhorias de serviço

| Melhorias de serviço | Detalhes |
| --- | --- |
| Opções de granularidade adicionais para recursos de computação | Os fins gerais e crítico para a empresa escalões para [bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md) e [conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md) agora tem mais opções de computação refinado.|
| Visualização de registos de auditoria para a instância gerida no portal do Azure | Visualizar [registos para instâncias geridas de auditoria](sql-database-managed-instance-auditing.md) no Azure portal é agora suportado.|
| Funcionalidade de deteção de ameaças avançadas mudada para segurança de dados avançada | Funcionalidade de deteção de ameaças avançadas mudado para [segurança de dados avançada](sql-advanced-threat-protection.md) para instâncias geridas, os conjuntos elásticos e bases de dados individuais. |
| &nbsp; |

### <a name="documentation-improvements"></a>Melhorias de documentação

| Melhorias de documentação | Detalhes |
| --- | --- |
| Instâncias geridas e a replicação transacional | Foi adicionado artigo sobre como utilizar [replicação transacional com instâncias geridas](replication-with-sql-database-managed-instance.md) |
| Foi adicionado AD do Azure com tutorial de instância gerida | Isso [do Azure AD com a instância gerida](sql-database-managed-instance-aad-security-tutorial.md) mostra tutorial, terá de configurar e testar geridos de segurança de instância com inícios de sessão do Azure AD. |
| Conteúdo atualizado para a automatização de tarefas utilizando scripts de Transact-SQL | Atualizada e foi esclarecido o conteúdo para usar [automatização de tarefas utilizando scripts de Transact-SQL](sql-database-job-automation-overview.md) para bases de dados individuais, conjuntos elásticos e instâncias geridas |
| Conteúdo de segurança para as instâncias geridas atualizado | Atualizada e foi esclarecido o conteúdo para o [modelo de segurança para instâncias geridas](sql-database-security-overview.md)e contrastada com o modelo de segurança para conjuntos elásticos e bases de dados individuais |
| Atualizar todos os inícios rápidos e tutoriais | Todos os inícios rápidos e tutoriais a [documentação](https://docs.microsoft.com/azure/sql-database) foram atualizados e atualizados de acordo com as alterações no portal do Azure |
| Guias de visão geral do guia de introdução foi adicionado | Adicionado um guia de visão geral do guia de início rápido para [bases de dados únicas](sql-database-quickstart-guide.md) e para [instâncias geridas](sql-database-managed-instance-quickstart-guide.md) |
| Foi adicionado base de dados SQL Glossário de termos | Isso [Glossário de termos](sql-database-glossary-terms.md) artigo fornece uma lista definitiva de termos de base de dados SQL e ligações para a página principal de conceitual que explica o termo no contexto. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Contribuir com conteúdos aperfeiçoamento

O conjunto de documentação do SQL do Azure é open source. Trabalhar no open oferece várias vantagens:

- Planeie os repositórios de código-fonte aberto no open para obter feedback sobre quais os documentos mais necessários.
- Reveja os repositórios de código-fonte aberto para publicar os conteúdos mais úteis no nosso primeiro lançamento.
- Atualização transparente de repositórios de código-fonte aberto no open para facilitar a melhorar continuamente o conteúdo.

Para contribuir para o conteúdo de documentação do Azure SQL Database, consulte a [descrição geral do guia de contribuinte Microsoft Docs](https://docs.microsoft.com/contribute/). A experiência do utilizador num [docs.microsoft.com](https://docs.microsoft.com/) integra [GitHub](https://github.com/) fluxos de trabalho diretamente para facilitar ainda mais. Comece por [editando o documento que está a ver](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). Ou, ajudar [rever novos tópicos](https://docs.microsoft.com/contribute/#review-open-prs), ou [criar problemas de qualidade](https://docs.microsoft.com/contribute/#create-quality-issues).
