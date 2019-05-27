---
title: Utilizar vistas de T-SQL no Azure SQL Data Warehouse | Documentos da Microsoft
description: Sugestões para utilizar vistas de T-SQL no Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: d4321f8aef6e754d8a1c5b16ac82b4fa62c40949
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873617"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Modos de exibição no armazém de dados SQL do Azure
Sugestões para utilizar vistas de T-SQL no Azure SQL Data Warehouse para o desenvolvimento de soluções. 

## <a name="why-use-views"></a>Por que usar modos de exibição?
Modos de exibição podem ser usados num número de diferentes formas de melhorar a qualidade da sua solução.  Este artigo destaca alguns exemplos de como enriquecer a sua solução com vistas, bem como as limitações de que precisam ser consideradas.

> [!NOTE]
> Sintaxe para criar vista não é abordada neste artigo. Para obter mais informações, consulte a [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql) documentação.
> 
> 

## <a name="architectural-abstraction"></a>Abstração de arquitetura
Um padrão de aplicação comum é voltar a criar tabelas usando CREATE TABLE AS SELECT (CTAS) seguido de um objeto mudar o nome padrão ao carregamento de dados.

O exemplo seguinte adiciona registos nova data a uma dimensão de data. Tenha em atenção a como uma nova tabela, DimDate_New, é criada pela primeira vez e, em seguida, o nome mudada para substituir a versão original da tabela.

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

No entanto, essa abordagem pode resultar em tabelas aparecendo e desaparecendo na vista de um utilizador, bem como as mensagens de erro "a tabela não existe". Vistas podem ser utilizadas para fornecer aos utilizadores com uma camada de apresentação consistente, lidando simultaneamente e os objetos subjacentes são renomeados. Ao fornecer acesso aos dados através de vistas, os utilizadores não têm visibilidade para as tabelas subjacentes. Esta camada oferece uma experiência de usuário consistente enquanto garantir que o armazém de dados designers pode evoluir o modelo de dados. A capacidade de evoluir as tabelas subjacentes significa que os designers podem usar CTAS para maximizar o desempenho durante o processo de carregamento de dados.   

## <a name="performance-optimization"></a>Otimização do desempenho
Modos de exibição também podem ser utilizados para impor as associações entre as tabelas com otimização de desempenho. Por exemplo, uma exibição pode incorporar uma chave de distribuição redundantes como parte dos critérios de junção para minimizar o movimento de dados. Outra vantagem de um modo de exibição poderia ser forçar uma consulta específica ou uma sugestão de junção. Utilizar vistas desta forma garante que as associações são sempre executadas de forma ideal, evitando a necessidade dos utilizadores memorizem a construção correta para suas associações.

## <a name="limitations"></a>Limitações
Modos de exibição no SQL Data Warehouse são armazenados como apenas metadados. Consequentemente, não estão disponíveis as seguintes opções:

* Não existe nenhuma opção de vinculação de esquema
* Tabelas-base não podem ser atualizadas através da vista
* Modos de exibição não não possível criar sobre as tabelas temporárias
* Não há suporte para o expandir / sugestões NOEXPAND
* Não há nenhum modos de exibição indexados no SQL Data Warehouse

## <a name="next-steps"></a>Passos Seguintes
Para mais sugestões de desenvolvimento, consulte [Descrição geral do desenvolvimento no SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


