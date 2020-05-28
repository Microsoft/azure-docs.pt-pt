---
title: T-SQL apresentam diferenças no Azure Synapse SQL
description: Lista de funcionalidades Transact-SQL que estão disponíveis no Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 6b4c44f0097b2896a063f3f3922760860e1d4a22
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118301"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Características Transact-SQL suportadas no Azure Synapse SQL

O Azure Synapse SQL é um serviço analítico de big data que lhe permite consultar e analisar os seus dados utilizando o idioma T-SQL. Pode utilizar o dialeto padrão de linguagem SQL compatível com ANSI utilizado no SQL Server e na Base de Dados Azure SQL para análise de dados. 

O idioma Transact-SQL é usado no modelo synapse SQL sem servidor e provisionado pode referenciar diferentes objetos e tem algumas diferenças no conjunto de funcionalidades suportadas. Nesta página, você pode encontrar diferenças de linguagem Transact-SQL de alto nível entre modelos de consumo de Synapse SQL.

## <a name="database-objects"></a>Objetos de base de dados

Os modelos de consumo em Synapse SQL permitem-lhe utilizar diferentes objetos de base de dados. A comparação dos tipos de objetos suportados é mostrada na tabela seguinte:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Tabelas** | [Sim](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não, o modelo sem servidor só pode consultar dados externos colocados no [Armazenamento Do Azure](#storage-options) |
| **Vistas** | [Sim, é](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)um pouco. Os pontos de vista podem usar [elementos linguísticos de consulta](#query-language) que estão disponíveis no modelo provisionado. | [Sim, é](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)um pouco. As opiniões podem usar [elementos de linguagem de consulta](#query-language) que estão disponíveis no modelo sem servidores. |
| **Rio Schemas** | [Sim](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sim](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Tabelas temporárias** | [Sim](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Procedimentos** | [Sim](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Funções** | [Sim](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Acionadores** | Não | Não |
| **Tabelas externas** | [Sim, é](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)um pouco. Consulte [formatos](#data-formats)de dados suportados . | [Sim, é](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)um pouco. Consulte [formatos](#data-formats)de dados suportados . |
| **Consultas de caching** | Sim, várias formas (cache, memória, enfumes de resultados). Além disso, a Visão Materializada é suportada | Não |
| **Variáveis de tabela** | [Não,](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)use mesas temporárias | Não |
| **[Distribuição de mesas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Sim | Não |
| **[Índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Sim | Não |
| **[Divisórias de mesa](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Sim | Não |
| **[Estatísticas](develop-tables-statistics.md)**            | Sim | Sim |
| **[Gestão da carga de trabalho, classes de recursos e controlo de moedas](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Sim    | Não |

## <a name="query-language"></a>Linguagem da consulta

As línguas de consulta utilizadas no Synapse SQL podem ter diferentes funcionalidades suportadas dependendo do modelo de consumo. A tabela que se segue descreve as diferenças linguísticas mais importantes nos dialetos Transact-SQL:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Declaração SELECT** | Sim. As cláusulas de consulta Transact-SQL [para XML/FOR JSON,](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), e [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) não são suportadas. | Sim. As cláusulas de consulta Transact-SQL [para XML,](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [MATCH,](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e pistas de consulta não são suportadas. [OFFSET/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) e [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) podem ser utilizados para consultar objetos do sistema (não dados externos). |
| **Declaração de INSIRA** | Sim | Não |
| **Declaração de aTUALIZAÇÃO** | Sim | Não |
| **Excluir declaração** | Sim | Não |
| **Declaração da MERGE** | Sim | Não |
| **[Transações](develop-transactions.md)** | Sim | Não |
| **[Etiquetas](develop-label.md)** | Sim | Não |
| **Carga de dados** | Sim. O utilitário preferido é a declaração [copy,](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) mas o sistema suporta tanto a carga bulk (BCP) [como a CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para carregamento de dados. | Não |
| **Exportação de dados** | Sim. Utilização do [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Sim. Utilização do [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| **Tipos** | Sim, todos os tipos Transact-SQL exceto [cursor,](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [hierárquico,](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ntext, texto e imagem,](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [rowversion,](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [Spatial Types,](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ \_ variante sql,](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, todos os tipos Transact-SQL exceto [cursor,](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [hierárquico,](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ntext, texto e imagem,](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [rowversion,](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [Types Espaciais,](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ \_ variante sql,](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [xml,](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e tipo de mesa |
| **Consultas entre bases de dados** | Não | Sim, incluindo a declaração [use.](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Funções incorporadas (análise)** | Sim, todas as Transact-SQL [Analytic,](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Conversão, [Data e Hora,](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Funções Lógicas, [Matemáticas,](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) exceto [ESCOLHA,](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [IIF,](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, todas as funções transact-SQL [Analytic,](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Conversão, [Data e Tempo,](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Lógicas, [Matemáticas.](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Funções incorporadas (texto)** | Sim. Todas as funções transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e Collation, exceto [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim. Todas as funções transact-SQL [String](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e Collation. |
| **Funções de valor de mesa incorporadas** | Sim, [funções Transact-SQL Rowset,](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions)exceto [OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENDATASOURCE,](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, [funções Transact-SQL Rowset,](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions)exceto [OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Agregados** |  Agregados transact-SQL incorporados exceto, exceto [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Agregados transact-SQL incorporados, exceto [STRING \_ AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Operadores** | Sim, todos os [operadores Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) exceto [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, todos os [operadores Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Controlo do fluxo** | Sim. Toda a declaração de [controlo de fluxo transact-SQL,](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) com exceção [de CONTINUE,](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [GOTO,](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [RETURN,](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim. Toda a declaração de [controlo de fluxo transact-SQL,](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) exceto [a declaração](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) de RETORNO e SELECT em `WHILE (...)` condições |
| **Declarações DDL (CREATE, ALTER, DROP)** | Sim. Todas as declarações dDL da Transact-SQL aplicáveis aos tipos de objetos suportados | Sim. Todas as declarações dDL da Transact-SQL aplicáveis aos tipos de objetos suportados |

## <a name="security"></a>Segurança

O Synapse SQL permite-lhe utilizar funcionalidades de segurança incorporadas para proteger os seus dados e controlar o acesso. O quadro seguinte compara diferenças de alto nível entre os modelos de consumo Synapse SQL.

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Inícios de sessão** | N/A (apenas os utilizadores contidos são suportados em bases de dados) | Sim |
| **Utilizadores** |  N/A (apenas os utilizadores contidos são suportados em bases de dados) | Sim |
| **[Utilizadores contidos](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Sim. **Nota:** apenas um utilizador da AD Azure pode ser administrado sem restrições | Sim |
| **Autenticação de nome de utilizador/palavra-passe SQL**| Sim | Sim |
| **Autenticação do Diretório Ativo Azure (AAD)**| Sim, utilizadores de Anúncios Azure | Sim, logins e utilizadores da AD Azure |
| **Storage Azure Ative Directory (AAD) passa a autenticação** | Sim | Sim |
| **Autenticação simbólica sas de armazenamento** | Não | Sim, utilizando a [BASE DE DADOS SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) em FONTE DE [DADOS EXTERNOS](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ou [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)de nível de instância . |
| **Autenticação da chave de acesso ao armazenamento** | Sim, usando base [de dados SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) em [FONTE DE DADOS EXTERNOS](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **[Autenticação de Identidade Gerida](../security/synapse-workspace-managed-identity.md) de Armazenamento** | Sim, usando credenciais de identidade de [serviço geridos](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, usando `Managed Identity` credencial. |
| **Autenticação de identidade de pedido de armazenamento** | [Sim](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Permissões - Nível de objeto** | Sim, incluindo a capacidade de conceder, NEGAR e REVOGAR permissões aos utilizadores | Sim, incluindo a capacidade de conceder, NEGAR e REVOGAR permissões aos utilizadores/logins nos objetos do sistema que são suportados |
| **Permissões - Nível Schema** | Sim, incluindo a capacidade de conceder, NEGAR e REVOGAR permissões aos utilizadores/logins no esquema | Sim, incluindo a capacidade de conceder, NEGAR e REVOGAR permissões aos utilizadores/logins no esquema |
| **Permissões - [Nível de base de dados](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Sim | Sim |
| **Permissões - [Nível de servidor](/sql/relational-databases/security/authentication-access/server-level-roles)** | Não | Sim, a sysadmina e outros servidores são suportados |
| **Permissões - [Segurança ao nível da coluna](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Sim | Sim |
| **Funções/grupos** | Sim (base de dados com âmbito de dados) | Sim (tanto o servidor como a base de dados) |
| **Funções de identidade de &amp; segurança** | Algumas funções e operadores de segurança Transact-SQL: , , , `CURRENT_USER` `HAS_DBACCESS` , , , `IS_MEMBER` , `IS_ROLEMEMBER` `SESSION_USER` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` , `EXECUTE AS``OPEN/CLOSE MASTER KEY` | Algumas funções de segurança transact-SQL e operadores: , , , , `CURRENT_USER` `HAS_DBACCESS` , , , , `HAS_PERMS_BY_NAME` , , , `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER` e `SESSION_USER` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` `EXECUTE AS` `REVERT` . As funções de segurança não podem ser utilizadas para consultar dados externos (armazenar o resultado em variável que pode ser usado na consulta).  |
| **CREDENCIAL DE BASE DE DADOS** | Sim | Sim |
| **CREDENCIAL DE MIRA DE SERVIDOR** | Não | Sim |
| **Segurança ao Nível da Linha** | [Sim](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | Não |
| **Encriptação de Dados Transparente (TDE)** | [Sim](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | Não | 
| **Classificação & de Descoberta de Dados** | [Sim](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Avaliação de Vulnerabilidade** | [Sim](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Advanced Threat Protection** | [Sim](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **Auditoria** | [Sim](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **[Regras da firewall](../security/synapse-workspace-ip-firewall.md)**| Sim | Sim |
| **[Ponto final privado](../security/synapse-workspace-managed-private-endpoints.md)**| Sim | Sim |

O pool SQL e o SQL a pedido utilizam o idioma padrão Transact-SQL para consultar dados. Para obter diferenças detalhadas, consulte a [referência linguística Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Ferramentas

Pode utilizar várias ferramentas para ligar ao Synapse SQL a dados de consulta.

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Synapse Studio** | Sim, scripts SQL | Sim, scripts SQL |
| **Power BI** | Sim | [Sim](tutorial-connect-power-bi-desktop.md) |
| **Serviço de Análise Azure** | Sim | Sim |
| **Azure Data Studio** | Sim | Sim, versão 1.18.0 ou superior. Os scripts SQL e os cadernos SQL são suportados. |
| **Estúdio de Gestão de Servidores SQL** | Sim | Sim, versão 18.5 ou superior |

> [!NOTE]
> Pode utilizar SSMS para ligar à SQL on-demand (pré-visualização) e consulta. É parcialmente suportado a partir da versão 18.5, pode usá-lo apenas para ligar e consultar.

A maioria das aplicações usam o idioma transact-SQL padrão pode consultar modelos de consumo provisionados e sem servidor estoiros do Synapse SQL.

## <a name="storage-options"></a>Opções de armazenamento

Os dados analisados podem ser armazenados em vários tipos de armazenamento. A tabela seguinte lista todas as opções de armazenamento disponíveis:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Armazenamento interno** | Sim | Não |
| **Lago de Dados Azure v2** | Sim | Sim |
| **Armazenamento de Blobs do Azure** | Sim | Sim |
| **Armazenamento analítico Azure CosmosDB** | Não | Sim usando [synapse Link](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (sob [pré-visualização fechada)](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations) |

## <a name="data-formats"></a>Formatos de dados

Os dados analisados podem ser armazenados em vários formatos de armazenamento. A tabela seguinte lista todos os formatos de dados disponíveis que podem ser analisados:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Delimitado** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sim](query-single-csv-file.md) |
| **CSV** | Sim (delimitadores multi-caracteres não suportados) | [Sim](query-single-csv-file.md) |
| **Parquet** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sim,](query-parquet-files.md)incluindo ficheiros com [tipos aninhados](query-parquet-nested-types.md) |
| **Colmeia ORC** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Hive RC** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **JSON** | Sim | [Sim](query-json-files.md) |
| **Avro** | Não | Não |
| **[Lago Delta](https://delta.io/)** | Não | Não |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | Não | Não |

## <a name="next-steps"></a>Próximos passos
Informações adicionais sobre as melhores práticas para o pool SQL e a pedido da SQL podem ser encontradas nos seguintes artigos:

- [Boas Práticas para piscina SQL](best-practices-sql-pool.md)
- [Boas práticas para sQL a pedido](best-practices-sql-on-demand.md)
