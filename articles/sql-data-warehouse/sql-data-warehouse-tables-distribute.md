---
title: Tabelas distribuídas documentação de orientação '-' Azure SQL Data Warehouse | Documentos da Microsoft
description: Recomendações para a criação de tabelas distribuídas distribuída por hash e round-robin no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: c3de7b46449b8075d17a19733eda88d692b1d876
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935821"
---
# <a name="guidance-for-designing-distributed-tables-in-azure-sql-data-warehouse"></a>Orientações para a criação de tabelas distribuídas no Azure SQL Data Warehouse
Recomendações para a criação de tabelas distribuídas distribuída por hash e round-robin no Azure SQL Data Warehouse.

Este artigo pressupõe que esteja familiarizado com a distribuição de dados e conceitos de movimento de dados no SQL Data Warehouse.  Para obter mais informações, consulte [do Azure SQL Data Warehouse - arquitetura Massivamente paralela de processamento (MPP)](massively-parallel-processing-mpp-architecture.md). 

## <a name="what-is-a-distributed-table"></a>O que é uma tabela distribuída?
Uma tabela distribuída aparece como uma única tabela, mas as linhas, na verdade, são armazenadas em 60 distribuições. As linhas são distribuídas com um hash ou o algoritmo de round robin.  

**Tabelas distribuídas de hash** melhorar o desempenho de consulta em grandes tabelas de fatos e são o foco deste artigo. **As tabelas round-robin** são úteis para melhorar a velocidade de carregamento. Estas opções de estrutura tem um impacto significativo em melhorar a consulta e desempenho de carregamento.

Outra opção de armazenamento de tabela é replicar uma pequena tabela em todos os nós de computação. Para obter mais informações, consulte [documentação de orientação para as tabelas replicadas](design-guidance-for-replicated-tables.md). Para rapidamente escolher entre três opções, consulte tabelas distribuídas a [descrição geral de tabelas](sql-data-warehouse-tables-overview.md). 

Como parte do design da tabela, compreenda o máximo possível sobre os seus dados e como os dados são consultados.  Por exemplo, considere estas perguntas:

- Como grande, é a tabela?   
- Com que frequência é atualizada a tabela?   
- Tenho de tabelas de fatos e dimensão num data warehouse?   


### <a name="hash-distributed"></a>Distribuídas com hash
Uma tabela distribuída por hash distribui as linhas de tabela em todos os nós de computação usando uma função de hash determinista para atribuir a cada linha para um [distribuição](massively-parallel-processing-mpp-architecture.md#distributions). 

![Tabela de Distributed](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "tabela distribuídas")  

Uma vez que os valores idênticas sempre de hash para a distribuição da mesma, o armazém de dados tem conhecimento interno das localizações de linha. SQL Data Warehouse utiliza esse conhecimento para minimizar o movimento de dados durante consultas, que melhora o desempenho de consulta. 

Tabelas distribuídas de hash funcionam bem para tabelas de factos grandes num esquema em estrela. Podem ter um grande número de linhas e ainda obter um elevado desempenho. Certamente, existem algumas considerações de design que ajudam a obter o desempenho que do sistema distribuído foi concebido para fornecer. A escolha de uma coluna de distribuição de bom é essa consideração descrita neste artigo. 

Considere a utilização de um hash e distribuída tabela quando:

- O tamanho da tabela no disco é mais do que 2 GB.
- A tabela tem insert frequente, atualizar e eliminar operações. 

### <a name="round-robin-distributed"></a>Distribuídas por round robin
Uma tabela distribuída round robin distribui as linhas de tabela uniformemente por todas as distribuições. A atribuição de linhas para as distribuições é aleatória. Ao contrário das tabelas de hash e distribuída, as linhas com valores de iguais não são garantidas a ser atribuída a distribuição da mesma. 

Como resultado, o sistema precisa, às vezes invocar uma operação de movimento de dados para organizar melhor os seus dados antes que possa resolver uma consulta.  Essa etapa adicional pode abrandar as suas consultas. Por exemplo, associar uma tabela round robin, normalmente, requer reshuffling as linhas, que é um impacto no desempenho.

Considere utilizar a distribuição round robin para a sua tabela nos seguintes cenários:

- Quando começar a utilizar como ponto de partida simple uma vez que é a predefinição
- Se não houver nenhuma chave de junção óbvio
- Se não houver coluna boa candidata para distribuir a tabela de hash
- Se a tabela não partilha uma chave de associação comum com outras tabelas
- Se a associação será menos perceptível do que as outras associações na consulta
- Quando a tabela é uma tabela de testes temporária

O tutorial [dados de táxis de Nova Iorque de carga para o Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) fornece um exemplo de carregamento de dados para uma tabela de teste round robin.


## <a name="choosing-a-distribution-column"></a>Escolher uma coluna de distribuição
Uma tabela distribuída por hash tem uma coluna de distribuição que é a chave de hash. Por exemplo, o código a seguir cria uma tabela distribuída por hash com ProductKey como a coluna de distribuição.

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
``` 

A escolha de uma coluna de distribuição é uma decisão de design importante uma vez que os valores nesta coluna, determinar como as linhas são distribuídas. A melhor escolha depende de vários fatores e geralmente envolve compromissos. No entanto, se não escolher a melhor coluna na primeira vez, pode utilizar [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) voltar a criar a tabela com uma coluna de distribuição diferente. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Escolha uma coluna de distribuição que não necessita de atualizações
Não é possível atualizar uma coluna de distribuição, a menos que a eliminar a linha e inserir uma nova linha com valores atualizados. Por conseguinte, selecione uma coluna com valores estáticos. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Escolha uma coluna de distribuição com dados que distribui uniformemente

Para obter melhor desempenho, todas as distribuições devem ter aproximadamente o mesmo número de linhas. Quando um ou mais distribuições tem um número desproporcional de linhas, algumas distribuições concluir a parte de uma consulta paralela antes de outras pessoas. Uma vez que a consulta não é possível concluir até que todas as distribuições de terminar de processamento, cada consulta só é o mais rápida a distribuição mais lenta.

- Distorção de dados significa que os dados não são distribuídos uniformemente entre as distribuições
- Distorção de processamento significa que algumas distribuições demorar mais do que outras pessoas, quando executar consultas paralelas. Isto pode acontecer quando os dados são desviados.
  
Equilibrar o processamento paralelo, selecione uma coluna de distribuição que:

- **Tem muitos valores exclusivos.** A coluna pode ter alguns valores duplicados. No entanto, todas as linhas com o mesmo valor são atribuídas a distribuição da mesma. Uma vez que existem 60 distribuições, a coluna deve ter, pelo menos, 60 valores exclusivos.  Normalmente, o número de valores exclusivos é muito maior.
- **Não tem valores nulos ou tem apenas de alguns valores nulos.** Para obter um exemplo extremo, se todos os valores na coluna são NULL, todas as linhas são atribuídas a distribuição da mesma. Como resultado, o processamento de consultas é desviado para uma distribuição e não beneficiam de processamento paralelo. 
- **Não é uma coluna de data**. Todos os dados para a mesma data seja colocado no sítio a distribuição da mesma. Se vários usuários estiver filtrando todas a mesma data, em seguida, apenas 1 a 60 distribuições fazer todo o trabalho de processamento. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Escolha uma coluna de distribuição que minimiza o movimento de dados

Para obter consultas de resultado de consulta correta pode mover dados de um nó de computação para outro. Movimento de dados acontece frequentemente quando as consultas ter junções e agregações em tabelas distribuídas. Escolha uma coluna de distribuição que ajuda a minimizar o movimento de dados é uma das estratégias mais importantes para otimizar o desempenho do seu armazém de dados SQL.

Para minimizar o movimento de dados, selecione uma coluna de distribuição que:

- É utilizada na `JOIN`, `GROUP BY`, `DISTINCT`, `OVER`, e `HAVING` cláusulas. Quando duas grandes tabelas de fatos têm associações frequentes, o desempenho das consultas melhora quando distribuir ambas as tabelas em um das colunas de associação.  Quando não é possível utilizar uma tabela em associações, considere distribuir a tabela numa coluna que esteja com frequência no `GROUP BY` cláusula.
- É *não* utilizados na `WHERE` cláusulas. Isso pode limitar a consulta para não executar em todas as distribuições. 
- É *não* uma coluna de data. Cláusulas WHERE, muitas vezes, filtrar por data.  Quando isto acontecer, pode executar todo o processamento em apenas algumas distribuições.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>O que fazer quando nenhuma das colunas são uma coluna de distribuição boa

Se nenhuma das suas colunas tem suficiente valores distintos para uma coluna de distribuição, pode criar uma nova coluna como uma composição de um ou mais valores. Para evitar o movimento de dados durante a execução da consulta, utilize a coluna de distribuição composto como uma coluna de associação em consultas.

Depois de criar uma tabela de hash e distribuída, a próxima etapa é carregar dados para a tabela.  Para carregar a documentação de orientação, veja [descrição geral do carregamento](sql-data-warehouse-overview-load.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Como descobrir se a coluna de distribuição é uma boa escolha
Depois de dados são carregados numa tabela distribuída por hash, verifique como as linhas são distribuídas por 60 distribuições. As linhas por distribuição podem variar até 10% sem um impacto considerável no desempenho. 

### <a name="determine-if-the-table-has-data-skew"></a>Determinar se a tabela contém dados inclinar
Uma forma rápida de verificar para distorção de dados é usar [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). O seguinte código SQL devolve o número de linhas de tabela são armazenadas em cada um dos 60 distribuições. Para um desempenho equilibrado, as linhas na tabela distribuída devem ser distribuídas uniformemente por todas as distribuições.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Para identificar quais tabelas tem mais do que distorção de dados de 10%:

1. Criar a vista dbo.vTableSizes que é mostrado na [descrição geral de tabelas](sql-data-warehouse-tables-overview.md#table-size-queries) artigo.  
2. Execute a seguinte consulta:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Planos de consulta de verificação para movimento de dados
Uma coluna de distribuição boa permite junções e agregações para que o movimento de dados mínimo. Isso afeta a forma como as junções devem ser escritas. Para obter o movimento de dados mínimo para uma associação em duas tabelas de hash e distribuída, uma das colunas de associação tem de ser a coluna de distribuição.  Quando duas tabelas de hash e distribuída ingressam numa coluna de distribuição do mesmo tipo de dados, a associação não necessita de movimento de dados. Associações podem utilizar colunas adicionais sem incorrer em movimento de dados.

Para evitar o movimento de dados durante uma associação:

- As tabelas envolvidas na associação tem de ser distribuídas em com hash **um** das colunas a participar na União.
- Os tipos de dados das colunas de associação tem de corresponder entre ambas as tabelas.
- As colunas têm de ser associadas com um operador equals.
- O tipo de associação não pode ser um `CROSS JOIN`.

Para ver se consultas ocorrerem movimento de dados, pode ver o plano de consulta.  


## <a name="resolve-a-distribution-column-problem"></a>Resolver um problema de coluna de distribuição
Não é necessário resolver que inclinar de todos os casos de dados. Distribuição de dados é uma questão de encontrar o equilíbrio certo entre minimizar distorção de dados e o movimento de dados. Nem sempre é possível minimizar a distorção de dados e o movimento de dados. Por vezes, as vantagens de ter o movimento de dados mínimo podem superar o impacto da eliminação de dados skew.

Para decidir se deve resolver dados distorção numa tabela, deve compreender tanto quanto possível sobre os volumes de dados e as consultas na carga de trabalho. Pode utilizar os passos a [consulta monitorização](sql-data-warehouse-manage-monitor.md) artigo para monitorizar o impacto de distorção sobre o desempenho de consulta. Mais concretamente, procure o tempo que demora a consultas grandes em distribuições individuais.

Uma vez que não é possível alterar a coluna de distribuição numa tabela existente, é a forma típica de resolver distorção de dados voltar a criar a tabela com uma coluna de distribuição diferente.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Voltar a criar a tabela com uma nova coluna de distribuição
Este exemplo utiliza [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) voltar a criar uma tabela com uma coluna de distribuição hash diferente.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Passos Seguintes

Para criar uma tabela distribuída, utilize uma das afirmações a seguir:

- [Criar tabela (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Criar TABLE AS SELECT (Azure SQL Data Warehouse](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


