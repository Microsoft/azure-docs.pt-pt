---
title: Usando vistas T-SQL
description: Dicas para a utilização de vistas T-SQL e desenvolvimento de soluções na piscina Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26eb3a495fd1c896416265687d92da66dfc3599b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85212297"
---
# <a name="views-in-synapse-sql-pool"></a>Vistas para a piscina Synapse SQL

As vistas podem ser usadas de várias maneiras para melhorar a qualidade da sua solução.

A piscina SQL suporta vistas standard e materializada. Ambas são tabelas virtuais criadas com expressões SELECT e apresentadas a consultas como tabelas lógicas.

As vistas encapsulam a complexidade da computação comum de dados e adicionam uma camada de abstração às alterações de cálculo para que não haja necessidade de reescrever consultas.

## <a name="standard-view"></a>Vista padrão

Uma visão padrão calcula os seus dados cada vez que a vista é usada. Não há dados armazenados no disco. As pessoas normalmente usam vistas padrão como uma ferramenta que ajuda a organizar os objetos lógicos e consultas numa base de dados.

Para utilizar uma visão padrão, uma consulta precisa de fazer referência direta a ela. Para mais informações, consulte a documentação [CREATE VIEW.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

As vistas na piscina SQL são armazenadas apenas como metadados. Consequentemente, as seguintes opções não estão disponíveis:

* Não há opção de ligação de esquemas
* As tabelas base não podem ser atualizadas através da vista
* Vistas não podem ser criadas sobre tabelas temporárias
* Não há suporte para as dicas EXPAND / NOEXPAND
* Não há vistas indexadas na piscina SQL

As vistas padrão podem ser utilizadas para impor o desempenho otimizado entre tabelas. Por exemplo, uma visão pode incorporar uma chave de distribuição redundante como parte dos critérios de adesão para minimizar o movimento de dados.

Outro benefício de uma vista poderia ser forçar uma consulta específica ou uma dica de adesão. A utilização de pontos de vista desta forma garante que as juntas são sempre realizadas de forma ótima, evitando a necessidade de os utilizadores se lembrarem da construção correta para as suas junções.

## <a name="materialized-view"></a>Vista materializada

Uma vista materializada pré-computa, armazena e mantém os seus dados em piscina SQL como uma mesa. Não é necessária recomputação de cada vez que uma vista materializada é usada.

À medida que os dados são carregados em tabelas base, o pool SQL refresca sincronizadamente as vistas materializadas.  O otimizador de consultas utiliza automaticamente vistas materializadas implementadas para melhorar o desempenho da consulta, mesmo que as vistas não sejam referenciadas na consulta.  

Consultas que mais beneficiam de vistas materializadas são consultas complexas (tipicamente consultas com juntas e agregações) em grandes mesas que produzem pequenos conjuntos de resultados.  

Para obter mais informações sobre a sintaxe de vista materializada e outros requisitos, consulte a [VISÃO MATERIALIZADA COMO SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Para obter orientação de afinação de consultas, verifique [a sintonização de desempenho com vistas materializadas](performance-tuning-materialized-views.md).

## <a name="example"></a>Exemplo

Um padrão de aplicação comum é recriar tabelas usando o CREATE TABLE AS SELECT (CTAS) seguido de um padrão de renomeação de objetos enquanto carrega dados.  

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
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

No entanto, esta abordagem pode resultar em tabelas que aparecem e desaparecem da opinião de um utilizador, juntamente com a emissão de uma "tabela não existe".

As vistas podem ser usadas para fornecer aos utilizadores uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados. Ao fornecer acesso aos dados através de visualizações, os utilizadores não precisam de visibilidade para as tabelas subjacentes.

Esta camada proporciona uma experiência consistente do utilizador, garantindo ao mesmo tempo que os designers de armazéns de dados podem evoluir o modelo de dados. Ser capaz de evoluir as tabelas subjacentes significa que os designers podem usar CTAS para maximizar o desempenho durante o processo de carregamento de dados.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento da piscina SQL.](sql-data-warehouse-overview-develop.md)
