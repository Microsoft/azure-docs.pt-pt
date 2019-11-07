---
title: Exemplo na memória do banco de dados SQL do Azure
description: Experimente as tecnologias na memória do banco de dados SQL do Azure com OLTP e o exemplo columnstore.
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
ms.openlocfilehash: 8526236afdb0a312879cb3c1635a7fd85985278f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689816"
---
# <a name="in-memory-sample"></a>Exemplo na memória

As tecnologias na memória no banco de dados SQL do Azure permitem melhorar o desempenho do seu aplicativo e, potencialmente, reduzir o custo do seu banco de dados. Usando tecnologias na memória no banco de dados SQL do Azure, você pode obter melhorias de desempenho com várias cargas de trabalho.

Neste artigo, você verá dois exemplos que ilustram o uso de OLTP na memória, bem como índices columnstore no banco de dados SQL do Azure.

Para obter mais informações, consulte:
- [Visão geral e cenários de uso do OLTP na memória](https://msdn.microsoft.com/library/mt774593.aspx) (inclui referências a estudos de caso de cliente e informações para começar)
- [Documentação para OLTP na memória](https://msdn.microsoft.com/library/dn133186.aspx)
- [Guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- HTAP (processamento transacional/analítico híbrido), também conhecido como [análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. instalar o exemplo de OLTP na memória

Você pode criar o banco de dados de exemplo AdventureWorksLT com alguns cliques na [portal do Azure](https://portal.azure.com/). Em seguida, as etapas nesta seção explicam como você pode enriquecer seu banco de dados AdventureWorksLT com objetos OLTP na memória e demonstrar os benefícios de desempenho.

Para uma demonstração de desempenho mais simplista, mas mais visualmente atraente para OLTP na memória, consulte:

- Versão: [in-Memory-OLTP-Demo-v 1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Código-fonte: [in-Memory-OLTP-demo-código-fonte](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Etapas de instalação

1. No [portal do Azure](https://portal.azure.com/), crie um banco de dados Premium ou comercialmente crítico em um servidor. Defina a **origem** para o banco de dados de exemplo AdventureWorksLT. Para obter instruções detalhadas, consulte [criar seu primeiro banco de dados SQL do Azure](sql-database-single-database-get-started.md).

2. Conecte-se ao banco de dados com SQL Server Management Studio [(SSMS. exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Copie o [script Transact-SQL OLTP na memória](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) para a área de transferência. O script T-SQL cria os objetos na memória necessários no banco de dados de exemplo AdventureWorksLT que você criou na etapa 1.

4. Cole o script T-SQL no SSMS e, em seguida, execute o script. A cláusula de `MEMORY_OPTIMIZED = ON` CREATE TABLE instruções são cruciais. Por exemplo:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Erro 40536


Se você receber o erro 40536 ao executar o script T-SQL, execute o seguinte script T-SQL para verificar se o banco de dados oferece suporte na memória:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Um resultado de **0** significa que a memória não tem suporte e **1** significa que ela tem suporte. Para diagnosticar o problema, verifique se o banco de dados está na camada de serviço Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Sobre os itens criados com otimização de memória

**Tabelas**: o exemplo contém as seguintes tabelas com otimização de memória:

- Tabela SalesLT. Product_inmem
- Tabela SalesLT. SalesOrderHeader_inmem
- Tabela SalesLT. SalesOrderDetail_inmem
- Demo. DemoSalesOrderHeaderSeed
- Demo. DemoSalesOrderDetailSeed


Você pode inspecionar tabelas com otimização de memória por meio do pesquisador de **objetos** no SSMS. Clique com o botão direito do mouse em **tabelas** > **Filtrar** > **configurações de filtro** > **é otimizado para memória**. O valor é igual a 1.


Ou você pode consultar as exibições do catálogo, como:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procedimento armazenado compilado nativamente**: você pode inspecionar tabela saleslt. usp_InsertSalesOrder_inmem por meio de uma consulta de exibição de catálogo:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Executar a carga de trabalho OLTP de exemplo

A única diferença entre os dois *procedimentos armazenados* a seguir é que o primeiro procedimento usa versões com otimização de memória das tabelas, enquanto o segundo procedimento usa as tabelas regulares em disco:

- Tabela SalesLT **.** usp_InsertSalesOrder **_inmem**
- Tabela SalesLT **.** usp_InsertSalesOrder **_ondisk**


Nesta seção, você verá como usar o prático utilitário **ostress. exe** para executar os dois procedimentos armazenados em níveis de estressação. Você pode comparar quanto tempo leva para que as duas execuções de estresse sejam concluídas.


Quando você executa o ostress. exe, é recomendável passar valores de parâmetro projetados para os dois itens a seguir:

- Execute um grande número de conexões simultâneas usando-N100.
- Ter cada loop de conexão centenas de vezes, usando-R500.


No entanto, talvez você queira começar com valores muito menores como-N10 e-R50 para garantir que tudo esteja funcionando.


### <a name="script-for-ostressexe"></a>Script para ostress. exe


Esta seção exibe o script T-SQL que é inserido em nossa linha de comando ostress. exe. O script usa itens que foram criados pelo script T-SQL que você instalou anteriormente.


O script a seguir insere um exemplo de ordem de venda com cinco itens de linha nas seguintes *tabelas*com otimização de memória:

- Tabela SalesLT. SalesOrderHeader_inmem
- Tabela SalesLT. SalesOrderDetail_inmem


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


Para tornar a versão *_ondisk* do script T-SQL anterior para ostress. exe, você substituiria as duas ocorrências da subcadeia de caracteres *_inmem* por *_ondisk*. Essas substituições afetam os nomes de tabelas e procedimentos armazenados.


### <a name="install-rml-utilities-and-ostress"></a>Instalar utilitários e `ostress` do RML


O ideal é que você planeje executar o ostress. exe em uma VM (máquina virtual) do Azure. Você criaria uma [VM do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) na mesma região geográfica do Azure em que o banco de dados AdventureWorksLT reside. No entanto, você pode executar o ostress. exe em seu laptop.


Na VM, ou em qualquer host que você escolher, instale os utilitários RML (Replay Markup Language). Os utilitários incluem ostress. exe.

Para obter mais informações, consulte:
- A discussão de ostress. exe no [banco de dados de exemplo para OLTP na memória](https://msdn.microsoft.com/library/mt465764.aspx).
- [Banco de dados de exemplo para OLTP na memória](https://msdn.microsoft.com/library/mt465764.aspx).
- O [blog para instalar o ostress. exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-_inmem-stress-workload-first"></a>Executar a carga de trabalho de *_inmem* estresse primeiro


Você pode usar uma janela de *prompt de cmd do RML* para executar nossa linha de comando do ostress. exe. Os parâmetros de linha de comando direcionam `ostress` para:

- Executar conexões de 100 simultaneamente (-N100).
- Faça com que cada conexão execute o script T-SQL 50 vezes (-R50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Para executar a linha de comando ostress. exe anterior:


1. Redefina o conteúdo de dados do banco de dado executando o seguinte comando no SSMS para excluir todos os dados que foram inseridos por todas as execuções anteriores:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Copie o texto da linha de comando ostress. exe anterior para a área de transferência.

3. Substitua o `<placeholders>` para os parâmetros-S-U-P-d pelos valores reais corretos.

4. Execute a linha de comando editada em uma janela cmd do RML.


#### <a name="result-is-a-duration"></a>O resultado é uma duração


Quando `ostress.exe` é concluído, ele grava a duração da execução como sua linha final de saída na janela cmd do RML. Por exemplo, uma execução de teste mais curta duração cerca de 1,5 minutos:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Redefinir, editar para *_ondisk*e executar novamente


Depois de ter o resultado da execução do *_inmem* , execute as seguintes etapas para a *_ondisk* executar:


1. Redefina o banco de dados executando o comando a seguir no SSMS para excluir todos aqueles que foram inseridos pela execução anterior:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Edite a linha de comando ostress. exe para substituir todos os *_inmem* por *_ondisk*.

3. Execute novamente o ostress. exe pela segunda vez e Capture o resultado da duração.

4. Mais uma vez, redefina o banco de dados (para excluir com responsabilidade o que pode ser uma grande quantidade de dado de teste).


#### <a name="expected-comparison-results"></a>Resultados de comparação esperados

Nossos testes na memória mostraram que o desempenho foi melhorado em **nove vezes** para essa carga de trabalho simplista, com `ostress` em execução em uma VM do Azure na mesma região do Azure que o banco de dados.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. instalar o exemplo de análise na memória


Nesta seção, você compara os resultados de e/s e estatísticas quando está usando um índice columnstore versus um índice de árvore b tradicional.


Para análise em tempo real em uma carga de trabalho OLTP, geralmente é melhor usar um índice columnstore não clusterizado. Para obter detalhes, consulte [índices Columnstore descritos](https://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparar o teste de análise columnstore


1. Use o portal do Azure para criar um novo banco de dados AdventureWorksLT a partir do exemplo.
   - Use esse nome exato.
   - Escolha qualquer camada de serviço Premium.

2. Copie o [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) para a área de transferência.
   - O script T-SQL cria os objetos na memória necessários no banco de dados de exemplo AdventureWorksLT que você criou na etapa 1.
   - O script cria a tabela de dimensões e duas tabelas de fatos. As tabelas de fatos são populadas com 3,5 milhões linhas cada.
   - O script pode levar 15 minutos para ser concluído.

3. Cole o script T-SQL no SSMS e, em seguida, execute o script. A palavra-chave **COLUMNSTORE** na instrução **CREATE INDEX** é crucial, como em:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Defina AdventureWorksLT como nível de compatibilidade 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    O nível 130 não está diretamente relacionado aos recursos na memória. Mas o nível 130 geralmente fornece um desempenho de consulta mais rápido do que o 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Tabelas de chaves e índices columnstore


- dbo. FactResellerSalesXL_CCI é uma tabela que tem um índice columnstore clusterizado, que tem compactação avançada no nível de *dados* .

- dbo. FactResellerSalesXL_PageCompressed é uma tabela que tem um índice clusterizado regular equivalente, que é compactado apenas no nível de *página* .


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Consultas de chave para comparar o índice columnstore


Há [vários tipos de consulta T-SQL que você pode executar](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) para ver as melhorias de desempenho. Na etapa 2 do script T-SQL, preste atenção a esse par de consultas. Eles diferem apenas em uma linha:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Um índice columnstore clusterizado está na tabela FactResellerSalesXL\_CCI.

O trecho de script T-SQL a seguir imprime estatísticas para e/s e hora para a consulta de cada tabela.


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

Em um banco de dados com o tipo de preço P2, você pode esperar aproximadamente nove vezes o lucro de desempenho para essa consulta usando o índice columnstore clusterizado comparado com o índice tradicional. Com o P15, você pode esperar cerca de 57 vezes o lucro de desempenho usando o índice columnstore.



## <a name="next-steps"></a>Passos seguintes

- [Início rápido 1: tecnologias OLTP em memória para um desempenho mais rápido do T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)

- [Usar o OLTP na memória em um aplicativo SQL do Azure existente](sql-database-in-memory-oltp-migration.md)

- [Monitorar o armazenamento OLTP na memória](sql-database-in-memory-oltp-monitoring.md) para OLTP na memória


## <a name="additional-resources"></a>Recursos adicionais

#### <a name="deeper-information"></a>Informações mais profundas

- [Saiba como o quorum duplica a carga de trabalho do banco de dados principal ao reduzir a DTU de 70% com OLTP na memória no banco de dados SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP na memória na postagem do blog do banco de dados SQL do Azure](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Saiba mais sobre o OLTP na memória](https://msdn.microsoft.com/library/dn133186.aspx)

- [Saiba mais sobre índices columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Saiba mais sobre a análise operacional em tempo real](https://msdn.microsoft.com/library/dn817827.aspx)

- Consulte [padrões comuns de carga de trabalho e considerações de migração](https://msdn.microsoft.com/library/dn673538.aspx) (que descreve os padrões de carga de trabalho em que o OLTP na memória normalmente fornece ganhos de desempenho significativos)

#### <a name="application-design"></a>Design da aplicação

- [OLTP na memória (otimização na memória)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Usar o OLTP na memória em um aplicativo SQL do Azure existente](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Ferramentas

- [Portal do Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
