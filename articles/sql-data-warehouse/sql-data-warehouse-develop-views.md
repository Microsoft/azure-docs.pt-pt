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
ms.openlocfilehash: 8a770e66120e69271744942899186ece39b2a3c3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479529"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Modos de exibição no Azure SQL Data Warehouse
Dicas para usar modos de exibição T-SQL no Azure SQL Data Warehouse para desenvolver soluções. 

## <a name="why-use-views"></a>Por que usar exibições?
As exibições podem ser usadas de várias maneiras diferentes para melhorar a qualidade de sua solução.  Este artigo destaca alguns exemplos de como enriquecer sua solução com exibições, bem como as limitações que precisam ser consideradas.


> [!IMPORTANT]
> Consulte a nova sintaxe de exibição materializada em [criar exibição materializada como SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  Para obter mais informações, consulte as [notas de versão](/azure/sql-data-warehouse/release-notes-10-0-10106-0).
>


> [!NOTE]
> A sintaxe para CREATE VIEW não é discutida neste artigo. Para obter mais informações, consulte a documentação [criar exibição](/sql/t-sql/statements/create-view-transact-sql) .
> 

## <a name="architectural-abstraction"></a>Abstração da arquitetura

Um padrão de aplicativo comum é recriar tabelas usando CREATE TABLE como SELECT (CTAS) seguido por um padrão de renomeação de objeto durante o carregamento de dados.

O exemplo a seguir adiciona novos registros de data a uma dimensão de data. Observe como uma nova tabela, DimDate_New, é criada primeiro e, em seguida, renomeada para substituir a versão original da tabela.

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

No entanto, essa abordagem pode resultar em tabelas aparecendo e desaparecendo da exibição de um usuário, bem como das mensagens de erro "a tabela não existe". As exibições podem ser usadas para fornecer aos usuários uma camada de apresentação consistente, enquanto os objetos subjacentes são renomeados. Ao fornecer acesso aos dados por meio de exibições, os usuários não precisam de visibilidade para as tabelas subjacentes. Essa camada fornece uma experiência de usuário consistente, garantindo que os designers de data warehouse possam desenvolver o modelo de dados. Ser capaz de desenvolver as tabelas subjacentes significa que os designers podem usar o CTAS para maximizar o desempenho durante o processo de carregamento de dados.   

## <a name="performance-optimization"></a>Otimização de desempenho
As exibições também podem ser utilizadas para impor junções otimizadas de desempenho entre tabelas. Por exemplo, um modo de exibição pode incorporar uma chave de distribuição redundante como parte dos critérios de junção para minimizar a movimentação de dados. Outro benefício de uma exibição pode ser forçar uma consulta específica ou uma dica de junção. Usar modos de exibição dessa maneira garante que as junções sejam sempre executadas de maneira ideal, evitando a necessidade de os usuários se lembrarem da construção correta para suas junções.

## <a name="limitations"></a>Limitações
Os modos de exibição no SQL Data Warehouse são armazenados somente como metadados. Consequentemente, as seguintes opções não estão disponíveis:

* Não há opção de associação de esquema
* As tabelas base não podem ser atualizadas por meio da exibição
* As exibições não podem ser criadas em tabelas temporárias
* Não há suporte para as dicas expandir/desexpandir
* Não há exibições indexadas no SQL Data Warehouse

## <a name="next-steps"></a>Passos Seguintes
Para mais sugestões de desenvolvimento, consulte [Descrição geral do desenvolvimento no SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


