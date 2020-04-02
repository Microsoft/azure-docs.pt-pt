---
title: Amostra de memória
description: Experimente tecnologias de base de dados Azure SQL Na memória com o OLTP e a amostra de colunas.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b707d67c88eb550d397134b2294c1c5b0e1f7f7d
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528220"
---
# <a name="in-memory-sample"></a>Amostra de memória

As tecnologias In-Memory na Base de Dados Azure SQL permitem-lhe melhorar o desempenho da sua aplicação e potencialmente reduzir o custo da sua base de dados. Ao utilizar tecnologias In-Memory na Base de Dados Azure SQL, pode obter melhorias de desempenho com várias cargas de trabalho.

Neste artigo você verá duas amostras que ilustram o uso de In-Memory OLTP, bem como índices de lojas de colunas na Base de Dados Azure SQL.

Para obter mais informações, consulte:
- [Cenários de visão geral e utilização do OLTP em memória](https://msdn.microsoft.com/library/mt774593.aspx) (inclui referências a estudos de caso seletivas do cliente e informações para começar)
- [Documentação para OLTP em Memória](https://msdn.microsoft.com/library/dn133186.aspx)
- [Guia de Índices de Lojas de Colunas](https://msdn.microsoft.com/library/gg492088.aspx)
- Processamento transalítico/analítico híbrido (HTAP), também conhecido como [análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Instale a amostra OLTP em Memória

Pode criar a base de dados da amostra AdventureWorksLT com alguns cliques no [portal Azure](https://portal.azure.com/). Em seguida, os passos nesta secção explicam como pode enriquecer a sua base de dados AdventureWorksLT com objetos OLTP in-memory e demonstrar benefícios de desempenho.

Para uma demonstração de desempenho mais simplista, mas visualmente apelativa para In-Memory OLTP, consulte:

- Lançamento: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Código fonte: [in-memory-oltp-demo-source-code](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

#### <a name="installation-steps"></a>Passos de instalação

1. No [portal Azure, crie](https://portal.azure.com/)uma base de dados Premium ou Business Critical num servidor. Delineie a **Fonte** para a base de dados da amostra AdventureWorksLT. Para obter instruções detalhadas, consulte Criar a sua primeira base de [dados Azure SQL](sql-database-single-database-get-started.md).

2. Ligue-se à base de dados com o SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Copie o [script OLTP-SQL em memória](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) para a sua área de pranto. O script T-SQL cria os objetos in-memory necessários na base de dados da amostra AdventureWorksLT que criou no passo 1.

4. Colhe o script T-SQL em SSMS e, em seguida, execute o script. As `MEMORY_OPTIMIZED = ON` declarações da cláusula CREATE TABLE são cruciais. Por exemplo:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Erro 40536


Se tiver um erro 40536 quando executar o script T-SQL, execute o seguinte script T-SQL para verificar se a base de dados suporta In-Memory:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Um resultado de **0** significa que a In-Memory não é suportada, e **1** significa que é suportada. Para diagnosticar o problema, certifique-se de que a base de dados está no nível de serviço Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Sobre os itens criados otimizados pela memória

**Tabelas**: A amostra contém as seguintes tabelas otimizadas pela memória:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderHeadered
- Demo.DemoSalesOrderDetailSeed


Pode inspecionar tabelas otimizadas pela memória através do **Object Explorer** no SSMS. Clique certo **tabelas** > **As definições** > do filtro do**filtro** > **são otimizadas**pela memória . O valor é igual a 1.


Ou pode consultar as vistas do catálogo, tais como:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procedimento armazenado com compilado de forma nativa:** Pode inspecionar SalesLT.usp_InsertSalesOrder_inmem através de uma consulta de visualização de catálogo:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Executar a amostra de carga de trabalho OLTP

A única diferença entre os dois *procedimentos armazenados seguintes* é que o primeiro procedimento utiliza versões otimizadas pela memória das tabelas, enquanto o segundo procedimento utiliza as tabelas regulares em disco:

- **SalesLT.** usp_InsertSalesOrder **_inmem**
- **SalesLT.** _ondisk **_ondisk** usp_InsertSalesOrder


Nesta secção, você vê como usar o utilitário **ostress.exe** útil para executar os dois procedimentos armazenados em níveis stressantes. Pode comparar o tempo que leva para as duas corridas de stress terminarem.


Quando executar ostress.exe, recomendamos que passe os valores dos parâmetros concebidos para ambos:

- Executar um grande número de ligações simultâneas, utilizando -n100.
- Tenha cada laço de ligação centenas de vezes, usando -r500.


No entanto, é melhor começar com valores muito mais pequenos como -n10 e -r50 para garantir que tudo está a funcionar.


### <a name="script-for-ostressexe"></a>Roteiro para ostress.exe


Esta secção exibe o script T-SQL que está incorporado na nossa linha de comando ostress.exe. O script utiliza itens que foram criados pelo script T-SQL que instalou anteriormente.


O seguinte script insere uma ordem de venda de amostras com cinco itens de linha nas seguintes *tabelas*otimizadas pela memória:

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


Para fazer a *versão _ondisk* do script T-SQL anterior para ostress.exe, substituiria ambas as ocorrências do substring *_inmem* por *_ondisk*. Estas substituições afetam os nomes das tabelas e dos procedimentos armazenados.


### <a name="install-rml-utilities-and-ostress"></a>Instalar utilitários RML e`ostress`


Idealmente, você planeja vadiar ostress.exe em uma máquina virtual Azure (VM). Criaria um [VM Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) na mesma região geográfica azure onde reside a sua base de dados AdventureWorksLT. Mas podes publicar o stress.exe no teu portátil.


No VM, ou em qualquer anfitrião que escolher, instale os utilitários Replay Markup Language (RML). Os serviços públicos incluem ostress.exe.

Para obter mais informações, consulte:
- A discussão ostress.exe na Base de [Dados de Amostras para OLTP em Memória](https://msdn.microsoft.com/library/mt465764.aspx).
- [Base de Dados de Amostras para OLTP em Memória](https://msdn.microsoft.com/library/mt465764.aspx).
- O [blog para instalar ostress.exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-_inmem-stress-workload-first"></a>Executar a *carga de trabalho de stress _inmem* primeiro


Você pode usar uma janela *RML Cmd Prompt* para executar a nossa linha de comando ostress.exe. Os parâmetros da `ostress` linha de comando diretamente para:

- Executar 100 ligações simultaneamente (-n100).
- Faça com que cada ligação execute o script T-SQL 50 vezes (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Para executar a linha de comando ostress.exe anterior:


1. Redefinir o conteúdo da base de dados executando o seguinte comando no SSMS, para eliminar todos os dados que foram inseridos por quaisquer execuções anteriores:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Copie o texto da linha de comando ostress.exe anterior para a sua prancheta.

3. Substitua `<placeholders>` os parâmetros -S-U-P-d pelos valores reais corretos.

4. Coloque a linha de comando editada numa janela De Cmd RML.


#### <a name="result-is-a-duration"></a>Resultado é uma duração


Quando `ostress.exe` termina, escreve a duração da execução como a sua linha final de saída na janela RML Cmd. Por exemplo, um teste mais curto durou cerca de 1,5 minutos:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Reset, editar para *_ondisk,* em seguida, reexecutar


Depois de ter o resultado da corrida *_inmem,* execute os seguintes passos para a *corrida _ondisk:*


1. Redefinir a base de dados executando o seguinte comando em SSMS para eliminar todos os dados que foram inseridos pela execução anterior:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Editar a linha de comando ostress.exe para substituir todos os *_inmem* por *_ondisk*.

3. Recorra ostress.exe pela segunda vez, e capture o resultado da duração.

4. Mais uma vez, redefinir a base de dados (para apagar responsavelmente o que pode ser uma grande quantidade de dados de teste).


#### <a name="expected-comparison-results"></a>Resultados de comparação esperados

Os nossos testes In-Memory mostraram que o desempenho melhorou `ostress` **nove vezes** para esta carga de trabalho simplista, com uma execução num VM Azure na mesma região do Azure que a base de dados.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Instale a amostra de Análise em Memória


Nesta secção, compara-se os resultados da IO e das estatísticas quando se está a usar um índice de colunas contra um índice tradicional de árvore B.


Para análises em tempo real sobre uma carga de trabalho OLTP, muitas vezes é melhor usar um índice de colunas não agrupado. Para mais detalhes, consulte os Índices da [Columnstore Descritos](https://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparar o teste de análise da loja de colunas


1. Utilize o portal Azure para criar uma nova base de dados AdventureWorksLT a partir da amostra.
   - Usa o nome exato.
   - Escolha qualquer nível de serviço Premium.

2. Copie o [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) para a sua prancheta.
   - O script T-SQL cria os objetos in-memory necessários na base de dados da amostra AdventureWorksLT que criou no passo 1.
   - O guião cria a tabela Dimension e duas tabelas de factos. As tabelas de factos são povoadas com 3,5 milhões de filas cada.
   - O guião pode levar 15 minutos para ser concluído.

3. Colhe o script T-SQL em SSMS e, em seguida, execute o script. A palavra-chave **COLUMNSTORE** na declaração do **ÍNDICE CREATE** é crucial, como em:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Definir adventureWorksLT para o nível de compatibilidade 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    O nível 130 não está diretamente relacionado com as funcionalidades in-Memory. Mas o nível 130 geralmente fornece um desempenho de consulta mais rápido que 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Tabelas-chave e índices de lojas de colunas


- dbo. FactResellerSalesXL_CCI é uma tabela que tem um índice de lojas de colunas agrupadas, que tem compressão avançada ao nível dos *dados.*

- dbo. FactResellerSalesXL_PageCompressed é uma tabela que tem um índice regular equivalente agrupado, que é comprimido apenas ao nível da *página.*


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Principais consultas para comparar o índice de lojas de colunas


Existem [vários tipos de consultas T-SQL que pode executar](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) para ver melhorias de desempenho. No passo 2 do script T-SQL, preste atenção a este par de consultas. Diferem apenas numa linha:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Um índice de loja de colunas agrupado está na tabela FACTResellerSalesXL\_CCI.

O seguinte excerto de script T-SQL imprime estatísticas para IO e TIME para a consulta de cada tabela.


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

Numa base de dados com o nível de preços P2, pode esperar cerca de nove vezes o ganho de desempenho desta consulta utilizando o índice de colunas agrupadas em comparação com o índice tradicional. Com o P15, pode esperar cerca de 57 vezes o ganho de desempenho utilizando o índice de colunas.



## <a name="next-steps"></a>Passos seguintes

- [Quickstart 1: Tecnologias OLTP em Memória para um desempenho T-SQL mais rápido](https://msdn.microsoft.com/library/mt694156.aspx)

- [Utilizar o OLTP em Memória numa aplicação Azure SQL existente](sql-database-in-memory-oltp-migration.md)

- [Monitor armazenamento OLTP em memória](sql-database-in-memory-oltp-monitoring.md) para OLTP em memória


## <a name="additional-resources"></a>Recursos adicionais

#### <a name="deeper-information"></a>Informação mais profunda

- [Saiba como o Quorum duplica a carga de trabalho da base de dados chave enquanto baixa o DTU em 70% com o OLTP in-memory na Base de Dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP in-Memory em Post de blog de base de dados Azure SQL](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Saiba mais sobre OLTP in-memory](https://msdn.microsoft.com/library/dn133186.aspx)

- [Conheça os índices de lojas de colunas](https://msdn.microsoft.com/library/gg492088.aspx)

- [Conheça a análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)

- Consulte padrões comuns de carga de [trabalho e considerações de migração](https://msdn.microsoft.com/library/dn673538.aspx) (que descreve padrões de carga de trabalho onde o OLTP em memória geralmente proporciona ganhos significativos de desempenho)

#### <a name="application-design"></a>Design da aplicação

- [OLTP in-memory (Otimização em Memória)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Utilizar o OLTP em Memória numa aplicação Azure SQL existente](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Ferramentas

- [Portal do Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
