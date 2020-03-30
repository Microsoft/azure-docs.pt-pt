---
title: Usando vistas T-SQL
description: Dicas para utilizar vistas T-SQL no Azure SQL Data Warehouse para desenvolver soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9eccc225c5473291b7535339ff64a036730894ae
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351523"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Vistas em Azure SQL Data Warehouse
As vistas podem ser usadas de várias formas diferentes para melhorar a qualidade da sua solução. 

O Azure SQL Data Warehouse suporta vistas padrão e materializadas. Ambas são tabelas virtuais criadas com expressões SELECT e apresentadas a consultas como tabelas lógicas. As opiniões encapsulam a complexidade da computação comum de dados e adicionam uma camada de abstração às alterações de cálculo, pelo que não há necessidade de reescrever consultas.

## <a name="standard-view"></a>Vista padrão
Uma visão padrão calcula os seus dados sempre que a vista é utilizada. Não há dados armazenados no disco. As pessoas normalmente usam vistas padrão como uma ferramenta que ajuda a organizar os objetos lógicos e consultas numa base de dados. Para utilizar uma visão padrão, uma consulta precisa fazer referência direta à sua. Para mais informações, consulte a documentação [CREATE VIEW.](/sql/t-sql/statements/create-view-transact-sql)

As vistas no SQL Data Warehouse são armazenadas apenas como metadados. Consequentemente, não estão disponíveis as seguintes opções:
* Não há nenhuma opção de ligação de esquemas
* As tabelas base não podem ser atualizadas através da vista
* Não se podem criar pontos de vista sobre tabelas temporárias
* Não há suporte para as dicas EXPAND /NOEXPAND
* Não há vistas indexadas no Armazém de Dados SQL

As vistas padrão podem ser utilizadas para impor o desempenho otimizado entre as tabelas. Por exemplo, uma vista pode incorporar uma chave de distribuição redundante como parte dos critérios de adesão para minimizar o movimento de dados. Outro benefício de uma visão poderia ser forçar uma consulta específica ou juntar-se a uma dica. Utilizando pontos de vista desta forma, garante que as uniões são sempre realizadas de forma ótima, evitando a necessidade de os utilizadores se lembrarem da construção correta para as suas juntas.

## <a name="materialized-view"></a>Vista materializada
Uma visão materializada pré-computação, lojas e mantém os seus dados no Azure SQL Data Warehouse tal como uma mesa. Não é necessária recomputação de cada vez que uma vista materializada é usada. À medida que os dados são carregados em tabelas base, o Azure SQL Data Warehouse atualiza sincronizadamente as vistas materializadas.  O optimizador de consultas utiliza automaticamente vistas materializadas implantadas para melhorar o desempenho da consulta, mesmo que as vistas não sejam referenciadas na consulta.  Consultas que mais beneficiam de vistas materializadas são consultas complexas (consultas tipicamente com juntas e agregações) em grandes tabelas que produzem pequenos resultados.  

Para mais detalhes sobre a sintaxe de visualização materializada e outros requisitos, consulte a [CREATE MATERIALIZED VIEW AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  

Para obter orientações de afinação de consultas, verifique a [sintonização do desempenho com vistas materializadas](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views). 

## <a name="example"></a>Exemplo
Um padrão de aplicação comum é recriar tabelas utilizando a TABELA CREATE AS SELECT (CTAS) seguida de um padrão de renomeação de objetos enquanto carrega dados.  O exemplo seguinte adiciona novos registos de datas a uma dimensão de data. Note como uma nova tabela, DimDate_New, é criada pela primeira vez e depois renomeada para substituir a versão original da tabela.

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
No entanto, esta abordagem pode resultar em tabelas que aparecem e desaparecem da visão de um utilizador, bem como de mensagens de erro "a tabela não existe". As vistas podem ser usadas para fornecer aos utilizadores uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados. Ao fornecer acesso aos dados através de vistas, os utilizadores não precisam de visibilidade para as tabelas subjacentes. Esta camada proporciona uma experiência consistente do utilizador, garantindo que os designers de armazéns de dados podem evoluir o modelo de dados. Ser capaz de evoluir as tabelas subjacentes significa que os designers podem usar CTAS para maximizar o desempenho durante o processo de carregamento de dados.   

## <a name="next-steps"></a>Passos seguintes
Para obter mais sugestões de desenvolvimento, veja [SQL Data Warehouse development overview (Descrição geral do desenvolvimento no SQL Data Warehouse)](sql-data-warehouse-overview-develop.md).


