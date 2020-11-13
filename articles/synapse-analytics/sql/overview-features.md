---
title: T-SQL apresenta diferenças no Sinaapse SQL
description: Lista de funcionalidades Transact-SQL que estão disponíveis no Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: cdb6fa5e1721d023edc93307ca146d1599bac219
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579050"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Características Transact-SQL suportadas no Azure Synapse SQL

O Azure Synapse SQL é um serviço analítico de big data que lhe permite consultar e analisar os seus dados utilizando a linguagem T-SQL. Pode utilizar um dialeto padrão compatível com ANSI de linguagem SQL utilizado no SQL Server e na Base de Dados Azure SQL para análise de dados. 

O idioma Transact-SQL é utilizado no modelo sinapse SQL sem servidor e o modelo dedicado pode referenciar diferentes objetos e tem algumas diferenças no conjunto de funcionalidades suportadas. Nesta página, você pode encontrar diferenças linguísticas transact-SQL de alto nível entre os modelos de consumo de Synapse SQL.

## <a name="database-objects"></a>Objetos de base de dados

Os modelos de consumo em Synapse SQL permitem-lhe utilizar diferentes objetos de base de dados. A comparação dos tipos de objetos suportados é apresentada no quadro seguinte:

|   | Dedicada | Sem servidor |
| --- | --- | --- |
| **Tabelas** | [Sim](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Não, o modelo sem servidor só pode consultar dados externos colocados no [Azure Storage](#storage-options) |
| **Vistas** | [Sim](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). As vistas podem usar [elementos de linguagem](#query-language) de consulta que estão disponíveis em modelo dedicado. | [Sim](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). As vistas podem utilizar [elementos de linguagem](#query-language) de consulta que estão disponíveis no modelo sem servidor. |
| **Esquemas** | [Sim](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Sim](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| **Tabelas temporárias** | [Sim](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Procedimentos** | [Sim](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Yes |
| **Funções** | [Sim](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sim, apenas funções de valor de mesa em linha. |
| **Acionadores** | Não | Não |
| **Tabelas externas** | [Sim](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Consulte [os formatos de dados suportados.](#data-formats) | [Sim](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Consulte [os formatos de dados suportados.](#data-formats) |
| **Consultas de caching** | Sim, múltiplas formas (caching baseado em SSD, na memória, caching resultset). Além disso, a Vista Materializada é suportada | Não |
| **Variáveis de tabela** | [Não,](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)use mesas temporárias | Não |
| **[Distribuição de mesas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Yes | Não |
| **[Índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Yes | Não |
| **[Divisórias de mesa](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Yes | Não |
| **[Estatísticas](develop-tables-statistics.md)**            | Yes | Yes |
| **[Gestão da carga de trabalho, classes de recursos e controlo da conuscção](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Yes    | Não |
| **[Gestão da carga de trabalho, classes de recursos e controlo da conuscção](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Sim, usando ações de escala e de escala. | Sim, utilizando [o portal Azure ou o procedimento T-SQL](https://docs.microsoft.com/azure/synapse-analytics/sql/data-processed#cost-control). |

## <a name="query-language"></a>Linguagem da consulta

As línguas de consulta utilizadas no Sinaapse SQL podem ter diferentes funcionalidades suportadas dependendo do modelo de consumo. O quadro que se segue descreve as diferenças linguísticas de consulta mais importantes nos dialetos Transact-SQL:

|   | Dedicada | Sem servidor |
| --- | --- | --- |
| **Declaração SELECT** | Yes. As cláusulas de consulta Transact-SQL [FOR XML/FOR JSON,](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) não são suportadas. | Yes. As cláusulas de consulta Transact-SQL [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [PREDICT,](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e dicas de consulta não são suportadas. [OFFSET/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) e [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) podem ser utilizados para consultar objetos do sistema (não dados externos). |
| **Declaração de INSERÇÃO** | Yes | Não |
| **Declaração de ATUALIZAÇÃO** | Yes | Não |
| **Declaração de EXCLUSÃO** | Yes | Não |
| **Declaração da MERGE** | Não | Não |
| **[Transações](develop-transactions.md)** | Yes | Não |
| **[Etiquetas](develop-label.md)** | Yes | Não |
| **Carga de dados** | Yes. Utilitário preferencial é a declaração [COPY,](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) mas o sistema suporta tanto a carga BULK (BCP) como [o CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para carregamento de dados. | Não |
| **Exportar dados** | Yes. Utilização [de CETAs](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Yes. Utilização [de CETAs](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). |
| **Tipos** | Sim, todos os tipos Transact-SQL exceto [cursor,](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [hierarquia,](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [ntext, texto e imagem,](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [rowversion,](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [Spatial Types,](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [ \_ sql variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), e [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sim, todos os tipos Transact-SQL exceto [cursor,](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [hierarquia,](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [ntext, texto e imagem,](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [rowversion,](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [Spatial Types,](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [ \_ variante sql,](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [xml,](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e tipo de tabela |
| **Consultas entre bases de dados** | Não | Sim, incluindo a declaração [do USO.](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| **Funções incorporadas (análise)** | Sim, todos os [Analíticos](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)Transact-SQL, Conversão, [Data e Hora,](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)Funções Lógicas, [Matemáticas,](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) exceto [CHOOSE,](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [IIF,](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sim, todas as [funções analíticas](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)Transact-SQL, Conversão, [Data e Hora,](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)Lógica, [Matemática.](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| **Funções incorporadas (texto)** | Yes. Todas as funções Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e Collation, exceto [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [TRADUZIR](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Yes. Todas as funções Transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e Collation. |
| **Funções de valor de mesa incorporadas** | Sim, [funções de conjunto de linha Transact-SQL,](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions)com exceção [do OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [OPENDATASOURCE,](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sim, [funções de Conjunto de Linha Transact-SQL,](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions)com exceção [do OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  |
| **Agregados** |  Agregados transact-SQL incorporados, [exceto,](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) exceto CHECKSUM_AGG e [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Agregados integrados Transact-SQL, com exceção [do STRING \_ AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| **Operadores** | Sim, todos os [operadores Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) exceto [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sim, todos os [operadores Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  |
| **Controlo do fluxo** | Yes. Todas as [demonstrações de controlo de fluxo Transact-SQL](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) exceto [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [GOTO,](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [RETURN,](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Yes. Todas as [demonstrações de controlo de fluxo Transact-SQL,](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) exceto a consulta [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e SELECT em `WHILE (...)` condições |
| **Declarações DDL (CREATE, ALTER, DROP)** | Yes. Todas as declarações DDL Transact-SQL aplicáveis aos tipos de objetos suportados | Yes. Todas as declarações DDL Transact-SQL aplicáveis aos tipos de objetos suportados |

## <a name="security"></a>Segurança

O Sinaapse SQL permite-lhe utilizar funcionalidades de segurança incorporadas para proteger os seus dados e controlar o acesso. O quadro a seguir compara diferenças de alto nível entre os modelos de consumo de SQL da Synapse.

|   | Dedicada | Sem servidor |
| --- | --- | --- |
| **Inícios de sessão** | N/A (apenas os utilizadores contidos são suportados em bases de dados) | Yes |
| **Utilizadores** |  N/A (apenas os utilizadores contidos são suportados em bases de dados) | Yes |
| **[Utilizadores contidos](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Yes. **Nota:** apenas um utilizador AZure AD pode ser administrador sem restrições | Não |
| **Autenticação de nome de utilizador/palavra-passe SQL**| Yes | Yes |
| **Autenticação do Azure Ative Directory (Azure AD)**| Sim, utilizadores de AD Azure | Sim, logins e utilizadores da AD Azure |
| **Armazenamento Azure Ative Directy (Azure AD) através da autenticação** | Yes | Yes |
| **Armazenamento sas token autenticação** | Não | Sim, utilizando [a CREDENCIAL SCOPED DE BASE DE DADOS](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) em [FONTE DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ou [CREDENCIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)DE NÍVEL DE INSTÂNCIA . |
| **Autenticação da chave de acesso ao armazenamento** | Sim, utilizando [a CREDENCIAL SCOPED DE BASE DE DADOS](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) EM [FONTE DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Não |
| **Autenticação [de identidade gerida de](../security/synapse-workspace-managed-identity.md) armazenamento** | Sim, usando [a Credencial de Identidade de Serviço Gerido](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Sim, usando `Managed Identity` credencial. |
| **Autenticação de identidade de aplicação de armazenamento** | [Sim](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Não |
| **Permissões - Nível de objeto** | Sim, incluindo a capacidade de conceder, negar e revogar permissões aos utilizadores | Sim, incluindo a capacidade de grant, DENY e REVOGAR permissões para utilizadores/logins nos objetos do sistema que são suportados |
| **Permissões - Nível de Schema** | Sim, incluindo a capacidade de concessão, NEGAção e REVOGAção de permissões aos utilizadores/logins no esquema | Sim, incluindo a capacidade de concessão, NEGAção e REVOGAção de permissões aos utilizadores/logins no esquema |
| **Permissões - [Nível de base de dados](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Yes | Yes |
| **Permissões - [Nível de servidor](/sql/relational-databases/security/authentication-access/server-level-roles)** | Não | Sim, sysadmin e outros servidor-roles são suportados |
| **Permissões - [Segurança ao nível da coluna](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Yes | Yes |
| **Funções/grupos** | Sim (base de dados incluída) | Sim (tanto o servidor como a base de dados) |
| **Funções &amp; de identidade de segurança** | Algumas funções e operadores de segurança Transact-SQL:  `CURRENT_USER` , , , , , , , , , `HAS_DBACCESS` , , `IS_MEMBER` , `IS_ROLEMEMBER` `SESSION_USER` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` `EXECUTE AS` , `OPEN/CLOSE MASTER KEY` | Algumas funções e operadores de segurança Transact-SQL: , , , , , , , , ,  `CURRENT_USER` `HAS_DBACCESS` , , , `HAS_PERMS_BY_NAME` `IS_MEMBER', 'IS_ROLEMEMBER` e `IS_SRVROLEMEMBER` `SESSION_USER` `SESSION_CONTEXT` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` `EXECUTE AS` `REVERT` . As funções de segurança não podem ser utilizadas para consultar dados externos (armazenar o resultado em variável que pode ser utilizada na consulta).  |
| **CREDENCIAL SCOPED BASE DE DADOS** | Yes | Yes |
| **CREDENCIAL DE ÂMBITO DO SERVIDOR** | Não | Yes |
| **Segurança ao nível da linha** | [Sim](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | Não |
| **Encriptação de Dados Transparente (TDE)** | [Sim](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | Não | 
| **Deteção e Classificação de Dados** | [Sim](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Avaliação de Vulnerabilidades** | [Sim](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Advanced Threat Protection** | [Sim](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **Auditoria** | [Sim](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **[Regras da firewall](../security/synapse-workspace-ip-firewall.md)**| Yes | Yes |
| **[Ponto final privado](../security/synapse-workspace-managed-private-endpoints.md)**| Yes | Yes |

Piscina SQL dedicada e piscina SQL sem servidor usam linguagem padrão Transact-SQL para consultar dados. Para obter diferenças detalhadas, consulte a [referência linguística Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Ferramentas

Pode utilizar várias ferramentas para ligar ao Sinaapse SQL para consultar dados.

|   | Dedicada | Sem servidor |
| --- | --- | --- |
| **Synapse Studio** | Sim, scripts SQL | Sim, scripts SQL |
| **Power BI** | Yes | [Sim](tutorial-connect-power-bi-desktop.md) |
| **Serviço de Análise Azure** | Yes | Yes |
| **Azure Data Studio** | Yes | Sim, versão 1.18.0 ou superior. Os scripts SQL e os Cadernos SQL são suportados. |
| **SQL Server Management Studio** | Yes | Sim, versão 18.5 ou superior |

> [!NOTE]
> Pode utilizar SSMS para ligar à piscina SQL sem servidor (pré-visualização) e consultar. É parcialmente suportado a partir da versão 18.5, pode usá-lo apenas para ligar e consultar.

A maioria das aplicações usam linguagem padrão Transact-SQL pode consultar modelos de consumo dedicados e sem servidor do Synapse SQL.

## <a name="storage-options"></a>Opções de armazenamento

Os dados que são analisados podem ser armazenados em vários tipos de armazenamento. A tabela que se segue lista todas as opções de armazenamento disponíveis:

|   | Dedicada | Sem servidor |
| --- | --- | --- |
| **Armazenamento interno** | Yes | Não |
| **Lago Azure Data v2** | Yes | Yes |
| **Armazenamento de Blobs do Azure** | Yes | Yes |
| **Azure SQL (remoto)** | Não | Não |
| **Armazenamento transacional Azure CosmosDB** | Não | Não |
| **Armazenamento analítico Azure CosmosDB** | Não | Sim, utilizando [o Synapse Link (pré-visualização)](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (sob [pré-visualização fechada)](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations) |
| **Mesas Apache Spark (no espaço de trabalho)** | Não | Tabelas PARQUET apenas usando [sincronização de metadados](develop-storage-files-spark-tables.md) |
| **Tabelas de faíscas Apache (remota)** | Não | Não |
| **Tabelas databricks (remota)** | Não | Não |

## <a name="data-formats"></a>Formatos de dados

Os dados que são analisados podem ser armazenados em vários formatos de armazenamento. A tabela a seguir lista todos os formatos de dados disponíveis que podem ser analisados:

|   | Dedicada | Sem servidor |
| --- | --- | --- |
| **Delimitado** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Sim](query-single-csv-file.md) |
| **CSV** | Sim (delimiters multi-caracteres não suportados) | [Sim](query-single-csv-file.md) |
| **Parquet** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Sim,](query-parquet-files.md)incluindo ficheiros com [tipos aninhados](query-parquet-nested-types.md) |
| **Colmeia ORC** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Não |
| **Colmeia RC** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Não |
| **JSON** | Yes | [Sim](query-json-files.md) |
| **Avro** | Não | Não |
| **[Delta-lake](https://delta.io/)** | Não | Não |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | Não | Não |

## <a name="next-steps"></a>Próximos passos
Informações adicionais sobre as melhores práticas para piscina SQL dedicada e piscina SQL sem servidor podem ser encontradas nos seguintes artigos:

- [Boas Práticas para piscina SQL dedicada](best-practices-sql-pool.md)
- [Melhores práticas para piscina SQL sem servidor](best-practices-sql-on-demand.md)
