---
title: T-SQL apresenta diferenças no Sinaapse SQL
description: Lista de funcionalidades Transact-SQL que estão disponíveis no Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: ce7238aaa10b1e73f7fa3c061045f47cbc472e23
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378700"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Características Transact-SQL suportadas no Azure Synapse SQL

O Azure Synapse SQL é um serviço analítico de big data que lhe permite consultar e analisar os seus dados utilizando a linguagem T-SQL. Pode utilizar um dialeto padrão compatível com ANSI de linguagem SQL utilizado no SQL Server e na Base de Dados Azure SQL para análise de dados. 

O idioma Transact-SQL é utilizado no sinapse SQL sem servidor e o modelo a provisionado pode referenciar diferentes objetos e tem algumas diferenças no conjunto de funcionalidades suportadas. Nesta página, você pode encontrar diferenças linguísticas transact-SQL de alto nível entre os modelos de consumo de Synapse SQL.

## <a name="database-objects"></a>Objetos de base de dados

Os modelos de consumo em Synapse SQL permitem-lhe utilizar diferentes objetos de base de dados. A comparação dos tipos de objetos suportados é apresentada no quadro seguinte:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Tabelas** | [Sim](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não, o modelo sem servidor só pode consultar dados externos colocados no [Azure Storage](#storage-options) |
| **Vistas** | [Sim, é um](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)trabalho que sim. As vistas podem usar [elementos linguísticos](#query-language) de consulta que estão disponíveis no modelo a provisionado. | [Sim, é um](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)trabalho que sim. As vistas podem utilizar [elementos de linguagem](#query-language) de consulta que estão disponíveis no modelo sem servidor. |
| **Esquemas** | [Sim](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sim](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Tabelas temporárias** | [Sim](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Procedimentos** | [Sim](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Funções** | [Sim](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, apenas funções de valor de mesa em linha. |
| **Acionadores** | Não | Não |
| **Tabelas externas** | [Sim, é um](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)trabalho que sim. Consulte [os formatos de dados suportados.](#data-formats) | [Sim, é um](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)trabalho que sim. Consulte [os formatos de dados suportados.](#data-formats) |
| **Consultas de caching** | Sim, múltiplas formas (caching baseado em SSD, na memória, caching resultset). Além disso, a Vista Materializada é suportada | Não |
| **Variáveis de tabela** | [Não,](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)use mesas temporárias | Não |
| **[Distribuição de mesas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Sim | Não |
| **[Índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Sim | Não |
| **[Divisórias de mesa](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Sim | Não |
| **[Estatísticas](develop-tables-statistics.md)**            | Sim | Sim |
| **[Gestão da carga de trabalho, classes de recursos e controlo da conuscção](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Sim    | Não |

## <a name="query-language"></a>Linguagem da consulta

As línguas de consulta utilizadas no Sinaapse SQL podem ter diferentes funcionalidades suportadas dependendo do modelo de consumo. O quadro que se segue descreve as diferenças linguísticas de consulta mais importantes nos dialetos Transact-SQL:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Declaração SELECT** | Yes. As cláusulas de consulta Transact-SQL [FOR XML/FOR JSON,](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) não são suportadas. | Yes. As cláusulas de consulta Transact-SQL [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [PREDICT,](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e dicas de consulta não são suportadas. [OFFSET/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) e [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) podem ser utilizados para consultar objetos do sistema (não dados externos). |
| **Declaração de INSERÇÃO** | Sim | Não |
| **Declaração de ATUALIZAÇÃO** | Sim | Não |
| **Declaração de EXCLUSÃO** | Sim | Não |
| **Declaração da MERGE** | Não | Não |
| **[Transações](develop-transactions.md)** | Sim | Não |
| **[Etiquetas](develop-label.md)** | Sim | Não |
| **Carga de dados** | Yes. Utilitário preferencial é a declaração [COPY,](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) mas o sistema suporta tanto a carga BULK (BCP) como [o CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para carregamento de dados. | Não |
| **Exportação de dados** | Yes. Utilização [de CETAs](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Yes. Utilização [de CETAs](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| **Tipos** | Sim, todos os tipos Transact-SQL exceto [cursor,](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [hierarquia,](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ntext, texto e imagem,](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [rowversion,](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [Spatial Types,](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ \_ sql variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), e [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, todos os tipos Transact-SQL exceto [cursor,](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [hierarquia,](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ntext, texto e imagem,](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [rowversion,](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [Spatial Types,](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ \_ variante sql,](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [xml,](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e tipo de tabela |
| **Consultas entre bases de dados** | Não | Sim, incluindo a declaração [do USO.](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Funções incorporadas (análise)** | Sim, todos os [Analíticos](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Transact-SQL, Conversão, [Data e Hora,](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Funções Lógicas, [Matemáticas,](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) exceto [CHOOSE,](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [IIF,](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, todas as [funções analíticas](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Transact-SQL, Conversão, [Data e Hora,](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Lógica, [Matemática.](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Funções incorporadas (texto)** | Yes. Todas as funções Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e Collation, exceto [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [TRADUZIR](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Yes. Todas as funções Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e Collation. |
| **Funções de valor de mesa incorporadas** | Sim, [funções de conjunto de linha Transact-SQL,](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions)com exceção [do OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENDATASOURCE,](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, [funções de Conjunto de Linha Transact-SQL,](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions)com exceção [do OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Agregados** |  Agregados transact-SQL incorporados, [exceto,](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) exceto CHECKSUM_AGG e [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Agregados integrados Transact-SQL, com exceção [do STRING \_ AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Operadores** | Sim, todos os [operadores Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) exceto [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, todos os [operadores Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Controlo do fluxo** | Yes. Todas as [demonstrações de controlo de fluxo Transact-SQL](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) exceto [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [GOTO,](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [RETURN,](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Yes. Todas as [demonstrações de controlo de fluxo Transact-SQL,](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) exceto a consulta [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e SELECT em `WHILE (...)` condições |
| **Declarações DDL (CREATE, ALTER, DROP)** | Yes. Todas as declarações DDL Transact-SQL aplicáveis aos tipos de objetos suportados | Yes. Todas as declarações DDL Transact-SQL aplicáveis aos tipos de objetos suportados |

## <a name="security"></a>Segurança

O Sinaapse SQL permite-lhe utilizar funcionalidades de segurança incorporadas para proteger os seus dados e controlar o acesso. O quadro a seguir compara diferenças de alto nível entre os modelos de consumo de SQL da Synapse.

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Inícios de sessão** | N/A (apenas os utilizadores contidos são suportados em bases de dados) | Sim |
| **Utilizadores** |  N/A (apenas os utilizadores contidos são suportados em bases de dados) | Sim |
| **[Utilizadores contidos](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Yes. **Nota:** apenas um utilizador AZure AD pode ser administrador sem restrições | Sim |
| **Autenticação de nome de utilizador/palavra-passe SQL**| Sim | Sim |
| **Autenticação do Diretório Ativo Azure (AAD)**| Sim, utilizadores de AD Azure | Sim, logins e utilizadores da AD Azure |
| **Armazenamento Azure Ative Directy (AAD) através da autenticação** | Sim | Sim |
| **Armazenamento sas token autenticação** | Não | Sim, utilizando [a CREDENCIAL SCOPED DE BASE DE DADOS](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) em [FONTE DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ou [CREDENCIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)DE NÍVEL DE INSTÂNCIA . |
| **Autenticação da chave de acesso ao armazenamento** | Sim, utilizando [a CREDENCIAL SCOPED DE BASE DE DADOS](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) EM [FONTE DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Autenticação [de identidade gerida de](../security/synapse-workspace-managed-identity.md) armazenamento** | Sim, usando [a Credencial de Identidade de Serviço Gerido](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, usando `Managed Identity` credencial. |
| **Autenticação de identidade de aplicação de armazenamento** | [Sim](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Permissões - Nível de objeto** | Sim, incluindo a capacidade de conceder, negar e revogar permissões aos utilizadores | Sim, incluindo a capacidade de grant, DENY e REVOGAR permissões para utilizadores/logins nos objetos do sistema que são suportados |
| **Permissões - Nível de Schema** | Sim, incluindo a capacidade de concessão, NEGAção e REVOGAção de permissões aos utilizadores/logins no esquema | Sim, incluindo a capacidade de concessão, NEGAção e REVOGAção de permissões aos utilizadores/logins no esquema |
| **Permissões - [Nível de base de dados](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Sim | Sim |
| **Permissões - [Nível de servidor](/sql/relational-databases/security/authentication-access/server-level-roles)** | Não | Sim, sysadmin e outros servidor-roles são suportados |
| **Permissões - [Segurança ao nível da coluna](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Sim | Sim |
| **Funções/grupos** | Sim (base de dados incluída) | Sim (tanto o servidor como a base de dados) |
| **Funções &amp; de identidade de segurança** | Algumas funções e operadores de segurança Transact-SQL:  `CURRENT_USER` , , , , , , , , , `HAS_DBACCESS` , , `IS_MEMBER` , `IS_ROLEMEMBER` `SESSION_USER` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` `EXECUTE AS` , `OPEN/CLOSE MASTER KEY` | Algumas funções e operadores de segurança Transact-SQL: , , , , ,  `CURRENT_USER` , , , `HAS_DBACCESS` , , `HAS_PERMS_BY_NAME` , , `IS_MEMBER', 'IS_ROLEMEMBER` e `IS_SRVROLEMEMBER` `SESSION_USER` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` `EXECUTE AS` `REVERT` . As funções de segurança não podem ser utilizadas para consultar dados externos (armazenar o resultado em variável que pode ser utilizada na consulta).  |
| **CREDENCIAL SCOPED BASE DE DADOS** | Sim | Sim |
| **CREDENCIAL DE ÂMBITO DO SERVIDOR** | Não | Sim |
| **Segurança ao Nível da Linha** | [Sim](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | Não |
| **Encriptação de Dados Transparente (TDE)** | [Sim](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | Não | 
| **Deteção e Classificação de Dados** | [Sim](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Avaliação de Vulnerabilidades** | [Sim](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Advanced Threat Protection** | [Sim](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **Auditoria** | [Sim](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **[Regras da firewall](../security/synapse-workspace-ip-firewall.md)**| Sim | Sim |
| **[Ponto final privado](../security/synapse-workspace-managed-private-endpoints.md)**| Sim | Sim |

Piscina SQL e SQL on-demand usam linguagem padrão Transact-SQL para consultar dados. Para obter diferenças detalhadas, consulte a [referência linguística Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Ferramentas

Pode utilizar várias ferramentas para ligar ao Sinaapse SQL para consultar dados.

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Synapse Studio** | Sim, scripts SQL | Sim, scripts SQL |
| **Power BI** | Sim | [Sim](tutorial-connect-power-bi-desktop.md) |
| **Serviço de Análise Azure** | Sim | Sim |
| **Azure Data Studio** | Sim | Sim, versão 1.18.0 ou superior. Os scripts SQL e os Cadernos SQL são suportados. |
| **O SQL Server Management Studio** | Sim | Sim, versão 18.5 ou superior |

> [!NOTE]
> Pode utilizar SSMS para ligar ao SQL on-demand (pré-visualização) e consultar. É parcialmente suportado a partir da versão 18.5, pode usá-lo apenas para ligar e consultar.

A maioria das aplicações usam linguagem padrão Transact-SQL pode consultar os modelos de consumo a provisionados e sem servidor do Synapse SQL.

## <a name="storage-options"></a>Opções de armazenamento

Os dados que são analisados podem ser armazenados em vários tipos de armazenamento. A tabela que se segue lista todas as opções de armazenamento disponíveis:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Armazenamento interno** | Sim | Não |
| **Lago Azure Data v2** | Sim | Sim |
| **Armazenamento de Blobs do Azure** | Sim | Sim |
| **Armazenamento analítico Azure CosmosDB** | Não | Sim usando [Synapse Link](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (sob [pré-visualização fechada)](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations) |

## <a name="data-formats"></a>Formatos de dados

Os dados que são analisados podem ser armazenados em vários formatos de armazenamento. A tabela a seguir lista todos os formatos de dados disponíveis que podem ser analisados:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Delimitado** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sim](query-single-csv-file.md) |
| **CSV** | Sim (delimiters multi-caracteres não suportados) | [Sim](query-single-csv-file.md) |
| **Parquet** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sim,](query-parquet-files.md)incluindo ficheiros com [tipos aninhados](query-parquet-nested-types.md) |
| **Colmeia ORC** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Colmeia RC** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **JSON** | Sim | [Sim](query-json-files.md) |
| **Avro** | Não | Não |
| **[Delta-lake](https://delta.io/)** | Não | Não |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | Não | Não |

## <a name="next-steps"></a>Próximos passos
Informações adicionais sobre as melhores práticas para piscina SQL e SQL on-demand podem ser encontradas nos seguintes artigos:

- [Boas Práticas para piscina SQL](best-practices-sql-pool.md)
- [Melhores práticas para o SQL a pedido](best-practices-sql-on-demand.md)
