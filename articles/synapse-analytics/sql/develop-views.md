---
title: Vistas T-SQL usando Synapse SQL
description: Dicas para utilizar vistas T-SQL e desenvolver soluções com Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428657"
---
# <a name="t-sql-views-using-synapse-sql"></a>Vistas T-SQL usando Synapse SQL
Neste artigo, você encontrará dicas para usar vistas T-SQL e desenvolver soluções com Synapse SQL. 

## <a name="why-use-views"></a>Por que usar vistas?

As vistas podem ser usadas de várias formas diferentes para melhorar a qualidade da sua solução.  Este artigo destaca alguns exemplos de como enriquecer a sua solução com pontos de vista e inclui as limitações que devem ser consideradas.

### <a name="sql-pool---create-view"></a>Piscina SQL - crie vista

> [!NOTE]
> **Piscina SQL**: A sintaxe para create VIEW não é discutida neste artigo. Para mais informações, consulte a documentação [CREATE VIEW.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="sql-on-demand-preview---create-view"></a>SQL on-demand (pré-visualização) - criar vista

> [!NOTE]
> **SQL on-demand**: Syntax for CREATE VIEW não é discutido neste artigo. Para mais informações, consulte a documentação [CREATE VIEW.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="architectural-abstraction"></a>Abstração arquitetónica

Um padrão de aplicação comum é recriar tabelas utilizando a [TABELA CREATE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), que é seguida por um padrão de renomeação de objetos durante o carregamento de dados.

O exemplo seguinte adiciona novos registos de datas a uma dimensão de data. Note como uma nova tabela, DimDate_New, é criada pela primeira vez e depois renomeada para substituir a versão original da tabela.

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

Tenha em mente que esta abordagem pode resultar em tabelas que aparecem e desaparecem da opinião de um utilizador, e solicita mensagens de erro "a mesa não existe". As vistas podem ser usadas para fornecer aos utilizadores uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados.

Ao fornecer acesso aos dados através de vistas, os utilizadores não precisam de visibilidade para as tabelas subjacentes. Além de uma experiência consistente do utilizador, esta camada garante que os designers de análise podem evoluir o modelo de dados. A capacidade de evoluir as tabelas subjacentes significa que os designers podem usar CTAS para maximizar o desempenho durante o processo de carregamento de dados.

## <a name="performance-optimization"></a>Otimização do desempenho

As vistas também podem ser usadas para impor o desempenho otimizado junta-se entre tabelas. Por exemplo, uma vista pode incorporar uma chave de distribuição redundante como parte dos critérios de adesão para minimizar o movimento de dados.

Forçar uma consulta específica ou juntar uma dica é outro benefício de usar vistas T-SQL. Como tal, a capacidade de visualização garante que as juntas são sempre realizadas de forma ótima. Evitará a necessidade de os utilizadores se lembrarem da construção correta para as suas juntas.

## <a name="limitations"></a>Limitações

As vistas em Synapse SQL são armazenadas apenas como metadados. Consequentemente, as seguintes opções não estão disponíveis:

* Não há uma opção de ligação de esquemas.
* As tabelas base não podem ser atualizadas através da vista
* Vistas não podem ser criadas sobre mesas temporárias
* Não há suporte para as dicas expand/ NOEXPAND
* Não há pontos de vista indexados em Synapse SQL

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a visão geral do [desenvolvimento da Synapse SQL](develop-overview.md).



