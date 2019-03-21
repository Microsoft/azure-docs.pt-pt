---
title: Exemplo do SQL da base de dados dentro da memória do Azure | Documentos da Microsoft
description: Experimente o Azure SQL Database tecnologias dentro da memória com o exemplo OLTP e columnstore.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 2aa98c3958f1dffeb8adbad5e91a11f397d4a9fd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005733"
---
# <a name="in-memory-sample"></a>Exemplo de dentro da memória

Tecnologias dentro da memória na SQL Database do Azure permitem-lhe melhorar o desempenho da sua aplicação e potencialmente reduzem os custos da base de dados. Ao utilizar tecnologias dentro da memória na base de dados do Azure SQL, pode conseguir melhorias de desempenho com várias cargas de trabalho.

Neste artigo, verá dois exemplos que ilustram o uso de OLTP na memória, bem como os índices columnstore na base de dados do Azure SQL.

Para obter mais informações, consulte:
- [Descrição geral de OLTP na memória e cenários de uso](https://msdn.microsoft.com/library/mt774593.aspx) (inclui referências a casos práticos de clientes e informações para começar a trabalhar)
- [Documentação para OLTP dentro da memória](https://msdn.microsoft.com/library/dn133186.aspx)
- [Guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Híbrido transacional/analítico de processamento (HTAP), também conhecido como [análises operacionais em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Instalar o exemplo de OLTP na memória

É possível criar a base de dados de exemplo do AdventureWorksLT com alguns cliques na [portal do Azure](https://portal.azure.com/). Em seguida, os passos nesta secção explicam como pode enriquecer a sua base de dados AdventureWorksLT com objetos de OLTP na memória e demonstrar os benefícios de desempenho.

Para uma mais simples, mas visualmente mais atraente desempenho demonstração para OLTP dentro da memória, consulte:

- Release: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- O código-fonte: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Passos de instalação

1. Na [portal do Azure](https://portal.azure.com/), criar uma base de dados Premium ou críticas para a empresa num servidor. Definir o **origem** na base de dados de exemplo do AdventureWorksLT. Para obter instruções detalhadas, consulte [criar a sua primeira base de dados SQL do Azure](sql-database-single-database-get-started.md).

2. Ligar à base de dados com o SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Copiar o [script dentro da memória OLTP Transact-SQL](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) para sua área de transferência. O script T-SQL cria os objetos necessários dentro da memória da base de dados de exemplo AdventureWorksLT que criou no passo 1.

4. Cole o script T-SQL no SSMS e, em seguida, execute o script. O `MEMORY_OPTIMIZED = ON` instruções CREATE TABLE de cláusula são cruciais. Por exemplo:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Erro 40536


Se obtiver o erro 40536 ao executar o script T-SQL, execute o seguinte script de T-SQL para verificar se a base de dados suporta dentro da memória:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


O resultado de **0** significa que essa memória não é suportada, e **1** significa que é suportado. Para diagnosticar o problema, certifique-se de que a base de dados está na camada de serviço Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Sobre os itens criados com otimização de memória

**Tabelas**: O exemplo contém as seguintes tabelas com otimização de memória:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Pode inspecionar a tabelas com otimização de memória através do **Object Explorer** no SSMS. Com o botão direito **tabelas** > **filtro** > **definições do filtro** > **tem otimizada de memória**. O valor é igual a 1.


Ou pode consultar as exibições de catálogo, tais como:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procedimento armazenado compilado nativamente**: Pode inspecionar SalesLT.usp_InsertSalesOrder_inmem através de uma consulta de vista de catálogo:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Executar a carga de trabalho do OLTP de exemplo

A única diferença entre os dois *procedimentos armazenados* é que o primeiro procedimento utiliza versões com otimização de memória das tabelas, enquanto o segundo procedimento utiliza as tabelas no disco regulares:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


Nesta secção, verá como utilizar o útil **ostress.exe** utilitário para executar os dois procedimentos armazenados em níveis estressante. Pode comparar o tempo que demora para execuções de duas estresse concluir.


Quando executa ostress.exe, recomendamos que passe os valores de parâmetro concebidos para os seguintes elementos:

- Executar um grande número de ligações em simultâneo, através da utilização - n100.
- Ter cada loop de ligação de centenas de vezes, ao utilizar - r500.


No entanto, convém começar com valores muito menores, como - n10 e - r50 para se certificar de que tudo está funcionando.


### <a name="script-for-ostressexe"></a>Script para ostress.exe


Esta secção apresenta o script de T-SQL que é incorporado na nossa linha de comando ostress.exe. O script utiliza os itens que foram criados pelo script T-SQL que instalou anteriormente.


O script a seguir insere uma ordem de venda de exemplo com cinco itens de linha para o seguinte com otimização de memória *tabelas*:

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


Para tornar o *_ondisk* versão do script T-SQL anterior para ostress.exe, poderia substituir ambas as ocorrências da *_inmem* subcadeia com *_ondisk*. Estas substituições afetam os nomes das tabelas e procedimentos armazenados.


### <a name="install-rml-utilities-and-ostress"></a>Instale os utilitários RML e `ostress`


Idealmente, que teria que pretende executar ostress.exe numa máquina virtual do Azure (VM). Criaria uma [VM do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) na mesma região geográfica do Azure onde reside a sua base de dados do AdventureWorksLT. Mas pode executar em vez disso ostress.exe no seu computador portátil.


Na VM, ou em qualquer anfitrião escolher, instale os utilitários de linguagem de marcação de repetição (RML). Os utilitários incluem ostress.exe.

Para obter mais informações, consulte:
- A discussão ostress.exe [base de dados de exemplo para OLTP dentro da memória](https://msdn.microsoft.com/library/mt465764.aspx).
- [Base de dados de exemplo para OLTP dentro da memória](https://msdn.microsoft.com/library/mt465764.aspx).
- O [blogue para a instalação ostress.exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Executar o *_inmem* enfatizar primeiro a carga de trabalho


Pode utilizar um *linha de comandos do RML Cmd* janela para executar nossa linha de comandos ostress.exe. Parâmetros da linha de comandos direto `ostress` para:

- Executar 100 ligações em simultâneo (-n100).
- Tem de cada ligação, execute o script T-SQL 50 vezes (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Para executar a linha de comando ostress.exe anterior:


1. Repor o conteúdo de dados de base de dados ao executar o seguinte comando no SSMS, para eliminar todos os dados que foi inseridos por quaisquer execuções anteriores:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Copie o texto da linha de comandos de ostress.exe anterior para a área de transferência.

3. Substitua o `<placeholders>` para os parâmetros -S - U+0b95 -P -d com os valores reais corretos.

4. Execute a linha de comandos editada numa janela RML Cmd.


#### <a name="result-is-a-duration"></a>Resultado é um período de tempo


Quando `ostress.exe` estiver concluído, ele grava a duração da execução como a linha final de saída na janela RML Cmd. Por exemplo, uma execução de teste mais curta duração de aproximadamente 1,5 minutos:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Repor, editar para *_ondisk*, em seguida, volte a executar


Depois de ter o resultado a partir do *_inmem* executar, execute os seguintes passos para o *_ondisk* executar:


1. Repor a base de dados ao executar o seguinte comando no SSMS para eliminar todos os dados que é inserido através da execução anterior:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Edite a linha de comando ostress.exe para substituir todas *_inmem* com *_ondisk*.

3. Volte a executar ostress.exe pela segunda vez e capturar o resultado de duração.

4. Novamente, repor a base de dados (com responsabilidade a eliminar o que pode ser uma grande quantidade de dados de teste).


#### <a name="expected-comparison-results"></a>Resultados da comparação esperado

Nossos testes dentro da memória têm mostrado que desempenho melhorado **nove vezes** para esta carga de trabalho simplista, com `ostress` em execução numa VM do Azure na mesma região do Azure como a base de dados.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Instalar o exemplo de análise dentro da memória


Nesta secção, compare os resultados de e/s e estatísticas de quando estiver usando um índice columnstore em comparação com um índice de árvore b tradicional.


Para efetuar análises em tempo real numa carga de trabalho OLTP, muitas vezes é melhor usar um índice columnstore não em cluster. Para obter detalhes, consulte [descrito de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparar o teste de análise columnstore


1. Utilize o portal do Azure para criar uma nova base de dados do AdventureWorksLT do exemplo.
   - Utilize esse nome exato.
   - Escolha qualquer escalão de serviço Premium.

2. Copiar o [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) para sua área de transferência.
   - O script T-SQL cria os objetos necessários dentro da memória da base de dados de exemplo AdventureWorksLT que criou no passo 1.
   - O script cria duas tabelas de fatos e de tabela de dimensão. As tabelas de fatos são preenchidas com linhas de 3,5 milhões cada.
   - O script poderá demorar 15 minutos a concluir.

3. Cole o script T-SQL no SSMS e, em seguida, execute o script. O **COLUMNSTORE** palavra-chave no **CREATE INDEX** instrução é crucial, como em:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Definir o AdventureWorksLT para o nível de compatibilidade 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Nível 130 não está diretamente relacionado a funcionalidades de dentro da memória. Mas o nível 130 geralmente fornece desempenho de consulta mais rápido que 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Principais tabelas e índices columnstore


- dbo. FactResellerSalesXL_CCI é uma tabela que tem um índice columnstore em cluster, o que aumentou a compactação na *dados* nível.

- dbo. FactResellerSalesXL_PageCompressed é uma tabela que tem um equivalente índice em cluster regular, que é comprimido apenas nos *página* nível.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Consultas principais para comparar o índice columnstore


Existem [vários tipos de consultas de T-SQL que pode executar](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) para ver as melhorias de desempenho. Passo 2 no script T-SQL, preste atenção para esse par de consultas. Eles diferem apenas numa linha:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Um índice columnstore em cluster está no FactResellerSalesXL\_tabela CCI.

O seguinte trecho de script de T-SQL imprime as estatísticas de e/s e tempo para a consulta de cada tabela.


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

Numa base de dados com o escalão de preço P2, pode esperar cerca de nove vezes o ganho de desempenho para esta consulta ao utilizar o índice columnstore em cluster em comparação com o índice tradicional. Com P15, pode esperar cerca de 57 vezes o ganho de desempenho ao utilizar o índice columnstore.



## <a name="next-steps"></a>Passos Seguintes

- [Início rápido 1: Tecnologias de OLTP dentro da memória para acelerar o desempenho de T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)

- [Utilize o OLTP na memória num aplicativo existente do SQL do Azure](sql-database-in-memory-oltp-migration.md)

- [Armazenamento OLTP dentro da memória de monitor](sql-database-in-memory-oltp-monitoring.md) para OLTP dentro da memória


## <a name="additional-resources"></a>Recursos adicionais

#### <a name="deeper-information"></a>Informações mais detalhadas

- [Saiba como o quórum duplica a carga de trabalho de chave da base de dados, reduzindo assim a DTU por 70% com o OLTP dentro da memória na base de dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP dentro da memória na mensagem de blogue de base de dados SQL do Azure](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Saiba mais sobre o OLTP dentro da memória](https://msdn.microsoft.com/library/dn133186.aspx)

- [Saiba mais sobre os índices columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Saiba mais sobre análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)

- Ver [padrões de carga de trabalho comuns e considerações sobre a migração](https://msdn.microsoft.com/library/dn673538.aspx) (que descreve padrões de carga de trabalho onde o OLTP dentro da memória fornece mais ganhos significativos de desempenho)

#### <a name="application-design"></a>Design do aplicativo

- [(Otimização de memória) de OLTP na memória](https://msdn.microsoft.com/library/dn133186.aspx)

- [Utilize o OLTP na memória num aplicativo existente do SQL do Azure](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Ferramentas

- [Portal do Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
