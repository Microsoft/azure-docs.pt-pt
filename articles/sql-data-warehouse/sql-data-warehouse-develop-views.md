---
title: Usando modos de exibição T-SQL no Azure SQL Data Warehouse | Microsoft Docs
description: Dicas para usar modos de exibição T-SQL no Azure SQL Data Warehouse para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1fd406243f0f2f5339c4170c4ec17286fcf2ef6d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901716"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Modos de exibição no Azure SQL Data Warehouse
As exibições podem ser usadas de várias maneiras diferentes para melhorar a qualidade de sua solução. 

O Azure SQL Data Warehouse dá suporte a exibições padrão e materializadas. Ambas são tabelas virtuais criadas com expressões SELECT e apresentadas a consultas como tabelas lógicas. As exibições encapsulam a complexidade da computação de dados comum e adicionam uma camada de abstração às alterações de computação para que não seja necessário reescrever consultas.

## <a name="standard-view"></a>Exibição padrão
Uma exibição padrão calcula seus dados a cada vez que a exibição é usada. Não há dados armazenados em disco. Normalmente, as pessoas usam modos de exibição padrão como uma ferramenta que ajuda a organizar os objetos lógicos e as consultas em um banco de dados. Para usar um modo de exibição padrão, uma consulta precisa fazer referência direta a ele. Para obter mais informações, consulte a documentação [criar exibição](/sql/t-sql/statements/create-view-transact-sql) .

Os modos de exibição no SQL Data Warehouse são armazenados somente como metadados. Consequentemente, as seguintes opções não estão disponíveis:
* Não há opção de associação de esquema
* As tabelas base não podem ser atualizadas por meio da exibição
* As exibições não podem ser criadas em tabelas temporárias
* Não há suporte para as dicas expandir/desexpandir
* Não há exibições indexadas no SQL Data Warehouse

As exibições padrão podem ser utilizadas para impor junções otimizadas de desempenho entre tabelas. Por exemplo, um modo de exibição pode incorporar uma chave de distribuição redundante como parte dos critérios de junção para minimizar a movimentação de dados. Outro benefício de uma exibição pode ser forçar uma consulta específica ou uma dica de junção. Usar modos de exibição dessa maneira garante que as junções sejam sempre executadas de maneira ideal, evitando a necessidade de os usuários se lembrarem da construção correta para suas junções.

## <a name="materialized-view"></a>Vista materializada
Uma exibição materializada computa previamente, armazena e mantém seus dados no Azure SQL Data Warehouse assim como uma tabela. Não há necessidade de recomputação toda vez que uma exibição materializada é usada. À medida que os dados são carregados em tabelas base, o Azure SQL Data Warehouse atualiza de forma síncrona as exibições materializadas.  O otimizador de consulta usa automaticamente exibições materializadas implantadas para melhorar o desempenho da consulta, mesmo se as exibições não forem referenciadas na consulta.  As consultas que mais se beneficiam com exibições materializadas são consultas complexas (normalmente consultas com junções e agregações) em tabelas grandes que produzem pequenos conjuntos de resultados.  

Para obter detalhes sobre a sintaxe de exibição materializada e outros requisitos, consulte [criar exibição materializada como SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  

Para obter diretrizes de ajuste de consulta, verifique o [ajuste de desempenho com exibições materializadas](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views). 

## <a name="example"></a>Exemplo
Um padrão de aplicativo comum é recriar tabelas usando CREATE TABLE como SELECT (CTAS) seguido por um padrão de renomeação de objeto durante o carregamento de dados.  O exemplo a seguir adiciona novos registros de data a uma dimensão de data. Observe como uma nova tabela, DimDate_New, é criada primeiro e, em seguida, renomeada para substituir a versão original da tabela.

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
No entanto, essa abordagem pode resultar em tabelas aparecendo e desaparecendo da exibição de um usuário, bem como das mensagens de erro "a tabela não existe". As exibições podem ser usadas para fornecer aos usuários uma camada de apresentação consistente, enquanto os objetos subjacentes são renomeados. Ao fornecer acesso aos dados por meio de exibições, os usuários não precisam de visibilidade para as tabelas subjacentes. Essa camada fornece uma experiência de usuário consistente, garantindo que os designers de data warehouse possam desenvolver o modelo de dados. Ser capaz de desenvolver as tabelas subjacentes significa que os designers podem usar o CTAS para maximizar o desempenho durante o processo de carregamento de dados.   

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte [Descrição geral do desenvolvimento no SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


