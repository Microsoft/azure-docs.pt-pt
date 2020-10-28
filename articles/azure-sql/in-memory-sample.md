---
title: In-Memory amostra
description: Experimente a Azure SQL Database In-Memory tecnologias com amostra de OLTP e columnstore.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b5a1035f8a213a6ce02dd3252ff7d3ddea46faf7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786586"
---
# <a name="in-memory-sample"></a>In-Memory amostra
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

In-Memory tecnologias na Base de Dados Azure SQL permitem-lhe melhorar o desempenho da sua aplicação e potencialmente reduzir o custo da sua base de dados. Ao utilizar tecnologias In-Memory na Base de Dados Azure SQL, pode obter melhorias de desempenho com várias cargas de trabalho.

Neste artigo, você verá duas amostras que ilustram o uso de In-Memory OLTP, bem como índices de loja de colunas na Base de Dados Azure SQL.

Para obter mais informações, consulte:

- [Cenários gerais e de utilização da Memória OLTP](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (inclui referências a estudos de caso de clientes e informações para começar)
- [Documentação para In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Guia de Índices de Colunas](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Processamento híbrido transacional/analítico (HTAP), também conhecido como [análise operacional em tempo real](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Instale a amostra OLTP In-Memory

Pode criar a base de dados de amostras AdventureWorksLT com alguns cliques no [portal Azure.](https://portal.azure.com/) Em seguida, os passos nesta secção explicam como pode enriquecer a sua base de dados AdventureWorksLT com In-Memory objetos OLTP e demonstrar benefícios de desempenho.

Para uma demonstração de desempenho mais simplista, mas visualmente mais apelativa para In-Memory OLTP, consulte:

- Lançamento: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Código [fonte: in-memory-oltp-demo-source-code](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

### <a name="installation-steps"></a>Passos de instalação

1. No [portal Azure,](https://portal.azure.com/)crie uma base de dados Premium ou Business Critical num servidor. Desa estada a **Fonte** para a base de dados de amostras AdventureWorksLT. Para obter instruções detalhadas, consulte [Criar a sua primeira base de dados na Base de Dados Azure SQL](database/single-database-create-quickstart.md).

2. Ligue-se à base de dados com o SQL Server Management Studio [(SSMS.exe)](/sql/ssms/download-sql-server-management-studio-ssms).

3. Copie o [script OLTP Transact-SQL na](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) sua área de transferência. O script T-SQL cria os objetos In-Memory necessários na base de dados de amostras AdventureWorksLT que criou no passo 1.

4. Cole o script T-SQL em SSMS e, em seguida, execute o script. A `MEMORY_OPTIMIZED = ON` cláusula CRIAR Declarações DE TABELA são cruciais. Por exemplo:

```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```

### <a name="error-40536"></a>Erro 40536

Se tiver erro 40536 quando executar o script T-SQL, execute o seguinte script T-SQL para verificar se a base de dados suporta In-Memory:

```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```

Um resultado de **0** significa que In-Memory não é apoiado, e **1** significa que é apoiado. Para diagnosticar o problema, certifique-se de que a base de dados está no nível de serviço Premium.

### <a name="about-the-created-memory-optimized-items"></a>Sobre os itens criados otimizados pela memória

**Tabelas** : A amostra contém as seguintes tabelas otimizadas pela memória:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

Pode inspecionar as tabelas otimizadas pela memória através do **Explorador de Objetos** em SSMS. As **definições** do filtro de filtro de tabelas de clique à direita  >  **Filter**  >  **Filter Settings**  >  **estão otimizadas na memória** . O valor é igual a 1.

Ou pode consultar as vistas do catálogo, tais como:

```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```

**Procedimento armazenado nativamente compilado** : Pode inspecionar SalesLT.usp_InsertSalesOrder_inmem através de uma consulta de visualização de catálogo:

```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```

&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Executar a amostra de carga de trabalho OLTP

A única diferença entre os dois *procedimentos armazenados seguintes* é que o primeiro procedimento utiliza versões otimizadas pela memória das tabelas, enquanto o segundo procedimento utiliza as tabelas regulares no disco:

- SalesLT **.** _inmem **usp_InsertSalesOrder**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**

Nesta secção, você vê como usar a utilidade **ostress.exe** útil para executar os dois procedimentos armazenados em níveis stressantes. Pode comparar o tempo que as duas corridas de stress demoram a terminar.

Quando correr ostress.exe, recomendamos que passe valores de parâmetros concebidos para ambos os seguintes:

- Executar um grande número de ligações simultâneas, utilizando -n100.
- Tenha cada ciclo de ligação centenas de vezes, utilizando -r500.

No entanto, é melhor começar com valores muito menores como -n10 e -r50 para garantir que tudo está a funcionar.

### <a name="script-for-ostressexe"></a>Roteiro para ostress.exe

Esta secção exibe o script T-SQL que está incorporado na nossa linha de comando ostress.exe. O script utiliza itens que foram criados pelo script T-SQL que instalou anteriormente.

O seguinte script insere uma ordem de venda de amostra com cinco itens de linha nas seguintes *tabelas* otimizadas pela memória:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem

```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```

Para fazer a versão *_ondisk* do script T-SQL anterior para ostress.exe, substituiria ambas as ocorrências do *_inmem* substring por *_ondisk* . Estas substituições afetam os nomes das tabelas e dos procedimentos armazenados.

#### <a name="install-rml-utilities-and-ostress"></a>Instalar utilitários RML e `ostress`

Idealmente, você planeja executar ostress.exe numa máquina virtual Azure (VM). Criaria um [Azure VM](https://azure.microsoft.com/documentation/services/virtual-machines/) na mesma região geográfica de Azure onde reside a sua base de dados AdventureWorksLT. Mas podes correr ostress.exe no teu portátil.

No VM, ou em qualquer anfitrião que escolher, instale os utilitários Replay Markup Language (RML). Os serviços públicos incluem ostress.exe.

Para obter mais informações, consulte:

- A ostress.exe discussão na [Base de Dados de Amostras para In-Memory OLTP.](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp)
- [Base de dados de amostras para In-Memory OLTP](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp).
- O [blog para instalar ostress.exe. ](https://techcommunity.microsoft.com/t5/sql-server-support/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql/ba-p/317910)

<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->

#### <a name="run-the-_inmem-stress-workload-first"></a>Executar a *_inmem* carga de trabalho de stress primeiro

Pode utilizar uma janela *de solicitação RML Cmd* para executar a nossa linha de comando ostress.exe. Os parâmetros da linha de comando direto `ostress` para:

- Executar 100 ligações simultaneamente (-n100).
- Ter cada ligação executada o script T-SQL 50 vezes (-r50).

```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```

Para executar a linha de comando ostress.exe anterior:

1. Redefinir o conteúdo dos dados da base de dados executando o seguinte comando em SSMS, para eliminar todos os dados que foram inseridos por quaisquer execuções anteriores:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Copie o texto da linha de comando ostress.exe anterior para a sua área de transferência.

3. Substitua `<placeholders>` o parâmetro -S-U-P -d pelos valores reais corretos.

4. Executar a sua linha de comando editada numa janela RML Cmd.

#### <a name="result-is-a-duration"></a>O resultado é uma duração

Quando `ostress.exe` termina, escreve a duração do percurso como a sua linha final de saída na janela RML Cmd. Por exemplo, um teste mais curto durou cerca de 1,5 minutos:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`

#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Reinicie, edite para *_ondisk,* em seguida, reexamque

Depois de ter o resultado da *_inmem* correr, execute os seguintes passos para a *corrida _ondisk:*

1. Redefinir a base de dados executando o seguinte comando em SSMS para eliminar todos os dados que foram inseridos na execução anterior:

   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Edite a linha de comando ostress.exe para substituir todas as *_inmem* por *_ondisk* .

3. Re-run ostress.exe pela segunda vez, e capture o resultado da duração.

4. Mais uma vez, reinicie a base de dados (para eliminar de forma responsável o que pode ser uma grande quantidade de dados de teste).

#### <a name="expected-comparison-results"></a>Resultados de comparação esperados

Os nossos testes In-Memory mostraram que o desempenho melhorou **nove vezes** para esta carga de trabalho simplista, com `ostress` um VM Azure na mesma região de Azure que a base de dados.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Instale a amostra In-Memory Analytics

Nesta secção, compara-se os resultados do IO e das estatísticas quando se está a usar um índice de loja de colunas contra um índice tradicional de árvore b.

Para análises em tempo real numa carga de trabalho OLTP, é geralmente melhor usar um índice de colunas não aglomerado. Para mais informações, consulte [índices de loja de colunas descritos](/sql/relational-databases/indexes/columnstore-indexes-overview).

### <a name="prepare-the-columnstore-analytics-test"></a>Preparar o teste de análise de loja de colunas

1. Utilize o portal Azure para criar uma nova base de dados AdventureWorksLT a partir da amostra.
   - Usa esse nome exato.
   - Escolha qualquer nível de serviço Premium.

2. Copie o [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) para a sua área de transferência.
   - O script T-SQL cria os objetos In-Memory necessários na base de dados de amostras AdventureWorksLT que criou no passo 1.
   - O script cria a tabela Dimension e duas tabelas de factos. As tabelas de factos são povoadas com 3,5 milhões de linhas cada.
   - O guião pode levar 15 minutos para ser concluído.

3. Cole o script T-SQL em SSMS e, em seguida, execute o script. A palavra-chave **COLUMNSTORE** na declaração **CREATE INDEX** é crucial, uma vez que em:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Desapasse adventureWorksLT para o nível de compatibilidade 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    O nível 130 não está diretamente relacionado com In-Memory funcionalidades. Mas o nível 130 geralmente proporciona um desempenho de consulta mais rápido do que 120.

#### <a name="key-tables-and-columnstore-indexes"></a>Tabelas-chave e índices de lojas de colunas

- dbo. FactResellerSalesXL_CCI é uma tabela que tem um índice de loja de colunas agrupado, que tem compressão avançada ao nível dos *dados.*

- dbo. FactResellerSalesXL_PageCompressed é uma tabela que tem um índice regular equivalente agrupado, que é comprimido apenas ao nível da *página.*

#### <a name="key-queries-to-compare-the-columnstore-index"></a>Principais consultas para comparar o índice de loja de colunas

Existem [vários tipos de consulta T-SQL que você pode executar](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) para ver melhorias de desempenho. No passo 2 do script T-SQL, preste atenção a este par de consultas. Diferem apenas numa linha:

- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`

Um índice de loja de colunas agrupado está na tabela FactResellerSalesXL \_ CCI.

O seguinte excerto de script T-SQL imprime estatísticas de IO e TIME para a consulta de cada tabela.

```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

Numa base de dados com o nível de preços P2, pode esperar cerca de nove vezes o ganho de desempenho desta consulta utilizando o índice de colunas agrupadas em comparação com o índice tradicional. Com o P15, pode esperar cerca de 57 vezes o ganho de desempenho utilizando o índice de loja de colunas.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart 1: In-Memory Tecnologias OLTP para um desempenho T-SQL mais rápido](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)

- [Utilize In-Memory OLTP numa aplicação Azure SQL existente](in-memory-oltp-configure.md)

- [Monitor In-Memory armazenamento OLTP](in-memory-oltp-monitor-space.md) para In-Memory OLTP

## <a name="additional-resources"></a>Recursos adicionais

### <a name="deeper-information"></a>Informação mais profunda

- [Saiba como o Qurum duplica a carga de trabalho da base de dados chave enquanto baixa a DTU em 70% com In-Memory OLTP na Base de Dados Azure SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Saiba mais sobre In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [Saiba mais sobre índices de lojas de colunas](/sql/relational-databases/indexes/columnstore-indexes-overview)

- [Conheça a análise operacional em tempo real](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

- Ver [Padrões de Carga de Trabalho Comuns e Considerações de Migração](/previous-versions/dn673538(v=msdn.10)) (que descreve padrões de carga de trabalho em que In-Memory OLTP geralmente proporciona ganhos significativos de desempenho)

#### <a name="application-design"></a>Design da aplicação

- [OLTP in-memory (otimização na memória)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [Utilize In-Memory OLTP numa aplicação Azure SQL existente](in-memory-oltp-configure.md)

#### <a name="tools"></a>Ferramentas

- [Portal do Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)

- [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)