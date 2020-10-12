---
title: Vistas T-SQL usando O SQL de Sinapse
description: Dicas para a utilização de vistas T-SQL e desenvolvimento de soluções com o Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: fafa0c2e1b02cc49bfb852ed7770b0927b0e9334
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032729"
---
# <a name="t-sql-views-using-synapse-sql"></a>Vistas T-SQL usando O SQL de Sinapse
Neste artigo, encontrará dicas para usar vistas T-SQL e desenvolver soluções com o Synapse SQL. 

## <a name="why-use-views"></a>Por que usar vistas

As vistas podem ser usadas de várias maneiras para melhorar a qualidade da sua solução.  Este artigo destaca alguns exemplos de como enriquecer a sua solução com pontos de vista e inclui as limitações que devem ser consideradas.

### <a name="sql-pool---create-view"></a>Piscina SQL - crie vista

> [!NOTE]
> **Piscina SQL**: Sintaxe para CREATE VIEW não é discutida neste artigo. Para mais informações, consulte a documentação [CREATE VIEW.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="sql-on-demand-preview---create-view"></a>SQL on demand (pré-visualização) - criar vista

> [!NOTE]
> **SQL on-demand**: Sintaxe para CREATE VIEW não é discutida neste artigo. Para mais informações, consulte a documentação [CREATE VIEW.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="architectural-abstraction"></a>Abstração arquitetónica

Um padrão de aplicação comum é recriar tabelas usando [o CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), que é seguido por um padrão de renomeação de objetos enquanto carrega dados.

O exemplo a seguir adiciona novos registos de datas a uma dimensão da data. Note como uma nova tabela, DimDate_New, é criada pela primeira vez e depois renomeada para substituir a versão original da tabela.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Tenha em mente que esta abordagem pode resultar em tabelas que aparecem e desaparecem do ponto de vista de um utilizador, e solicita mensagens de erro "tabela não existe". As vistas podem ser usadas para fornecer aos utilizadores uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados.

Ao fornecer acesso aos dados através de visualizações, os utilizadores não precisam de visibilidade para as tabelas subjacentes. Além de uma experiência de utilizador consistente, esta camada garante que os designers de análise podem evoluir o modelo de dados. A capacidade de evoluir as tabelas subjacentes significa que os designers podem usar CTAS para maximizar o desempenho durante o processo de carregamento de dados.

## <a name="performance-optimization"></a>Otimização do desempenho

As vistas também podem ser usadas para impor o desempenho otimizado entre tabelas. Por exemplo, uma visão pode incorporar uma chave de distribuição redundante como parte dos critérios de adesão para minimizar o movimento de dados.

Forçar uma consulta específica ou uma dica de adesão é outro benefício da utilização de vistas T-SQL. Como tal, a capacidade de vista garante que as juntas são sempre realizadas de forma ideal. Evitará a necessidade de os utilizadores se lembrarem da construção correta para as suas junções.

## <a name="limitations"></a>Limitações

As vistas no Synapse SQL são armazenadas apenas como metadados. Consequentemente, as seguintes opções não estão disponíveis:

* Não há uma opção de ligação de esquemas
* As tabelas base não podem ser atualizadas através da vista
* Vistas não podem ser criadas sobre tabelas temporárias
* Não há suporte para as dicas EXPAND / NOEXPAND
* Não existem pontos de vista indexados no SQL de Sinaapse

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a visão geral do [desenvolvimento do Synapse SQL.](develop-overview.md)



