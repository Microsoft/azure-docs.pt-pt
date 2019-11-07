---
title: Carregar dados de varejo da contoso
description: Use os comandos polybase e T-SQL para carregar duas tabelas dos dados de varejo da Contoso em Análise de SQL do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d928f2392f204baae6cfdbe864938ef0dee1d6ca
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692651"
---
# <a name="load-contoso-retail-data-to-a-sql-analytics-data-warehouse"></a>Carregar dados de varejo da Contoso para uma análise de SQL data warehouse

Neste tutorial, você aprenderá a usar os comandos do polybase e do T-SQL para carregar duas tabelas dos dados de varejo da Contoso em uma análise de SQL data warehouse. 

Neste tutorial, você vai:

1. Configurar o polybase para carregar do armazenamento de BLOBs do Azure
2. Carregar dados públicos no banco de dado
3. Execute otimizações após a conclusão do carregamento.

## <a name="before-you-begin"></a>Antes de começar
Para executar este tutorial, você precisa de uma conta do Azure que já tenha um data warehouse de análise SQ. Se você não tiver um data warehouse provisionado, consulte [criar uma data warehouse SQL e definir regra de firewall no nível de servidor] [criar um data warehouse SQL].

## <a name="1-configure-the-data-source"></a>1. configurar a fonte de dados
O polybase usa objetos externos do T-SQL para definir o local e os atributos dos dados externos. As definições de objeto externo são armazenadas em seu data warehouse de análise do SQL. Os dados são armazenados externamente.

### <a name="11-create-a-credential"></a>1,1. Criar uma credencial
**Ignore esta etapa** se você estiver carregando os dados públicos da contoso. Você não precisa de acesso seguro aos dados públicos, pois eles já estão acessíveis a qualquer pessoa.

**Não ignore esta etapa** se você estiver usando este tutorial como um modelo para carregar seus próprios dados. Para acessar dados através de uma credencial, use o seguinte script para criar uma credencial no escopo do banco de dados e, em seguida, use-a ao definir o local da fonte.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

### <a name="12-create-the-external-data-source"></a>1.2. Criar a fonte de dados externa
Use este comando [criar fonte de dados externa][CREATE EXTERNAL DATA SOURCE] para armazenar o local dos dados e o tipo de dados. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Se você optar por tornar os seus contêineres de armazenamento de BLOBs do Azure públicos, lembre-se de que, como o proprietário dos dados, você será cobrado pelos encargos de egresso de dados quando data center os dados saem 
> 
> 

## <a name="2-configure-data-format"></a>2. configurar o formato de dados
Os dados são armazenados em arquivos de texto no armazenamento de BLOBs do Azure, e cada campo é separado com um delimitador. No SSMS, execute o comando [criar formato de arquivo externo][CREATE EXTERNAL FILE FORMAT] a seguir para especificar o formato dos dados nos arquivos de texto. Os dados da Contoso são descompactados e delimitados por pipe.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. criar as tabelas externas
Agora que você especificou a fonte de dados e o formato de arquivo, você está pronto para criar as tabelas externas. 

### <a name="31-create-a-schema-for-the-data"></a>3,1. Crie um esquema para os dados.
Para criar um local para armazenar os dados da Contoso em seu banco de dado, crie um esquema.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3,2. Crie as tabelas externas.
Execute o script a seguir para criar as tabelas externas DimProduct e FactOnlineSales. Tudo o que você está fazendo aqui é definir nomes de coluna e tipos de dados e associá-los ao local e ao formato dos arquivos de armazenamento de BLOBs do Azure. A definição é armazenada no data warehouse de análise do SQL e os dados ainda estão no Azure Storage Blob.

O parâmetro **Location** é a pasta sob a pasta raiz no Azure Storage BLOB. Cada tabela está em uma pasta diferente.

```sql
--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. carregar os dados
Há diferentes maneiras de acessar dados externos.  Você pode consultar dados diretamente das tabelas externas, carregar os dados em novas tabelas no data warehouse ou adicionar dados externos a tabelas data warehouse existentes.  

### <a name="41-create-a-new-schema"></a>4,1. Criar um novo esquema
CTAS cria uma nova tabela que contém dados.  Primeiro, crie um esquema para os dados da contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4,2. Carregar os dados em novas tabelas
Para carregar dados do armazenamento de BLOBs do Azure na tabela data warehouse, use a instrução [CREATE TABLE as Select (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] . O carregamento com CTAS aproveita as tabelas externas com rigidez de tipos que você criou. Para carregar os dados em novas tabelas, use uma instrução [CTAS][CTAS] por tabela. 
 
CTAS cria uma nova tabela e a popula com os resultados de uma instrução SELECT. CTAS define a nova tabela para ter as mesmas colunas e tipos de dados que os resultados da instrução SELECT. Se você selecionar todas as colunas de uma tabela externa, a nova tabela será uma réplica das colunas e dos tipos de dados na tabela externa.

Neste exemplo, criamos a dimensão e a tabela de fatos como tabelas distribuídas de hash. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4,3 acompanhar o progresso do carregamento
Você pode acompanhar o progresso de sua carga usando DMVs (exibições de gerenciamento dinâmico). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. otimizar a compactação columnstore
Por padrão, a análise de SQL data warehouse armazena a tabela como um índice columnstore clusterizado. Após a conclusão de uma carga, algumas das linhas de dados podem não ser compactadas no columnstore.  Há diferentes motivos pelos quais isso pode acontecer. Para saber mais, consulte [gerenciar índices columnstore][manage columnstore indexes].

Para otimizar o desempenho da consulta e a compactação columnstore após uma carga, recompile a tabela para forçar o índice columnstore a compactar todas as linhas. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Para obter mais informações sobre como manter índices columnstore, consulte o artigo [gerenciar índices columnstore][manage columnstore indexes] .

## <a name="6-optimize-statistics"></a>6. otimizar estatísticas
É melhor criar estatísticas de coluna única imediatamente após um carregamento. Se você souber que determinadas colunas não serão em predicados de consulta, você poderá ignorar a criação de estatísticas nessas colunas. Se você criar estatísticas de coluna única em cada coluna, poderá levar muito tempo para recompilar todas as estatísticas. 

Se você decidir criar estatísticas de coluna única em cada coluna de cada tabela, poderá usar o exemplo de código de procedimento armazenado `prc_sqldw_create_stats` no artigo [estatísticas][statistics] .

O exemplo a seguir é um bom ponto de partida para a criação de estatísticas. Ele cria estatísticas de coluna única em cada coluna na tabela de dimensões e em cada coluna de junção nas tabelas de fatos. Você sempre pode adicionar estatísticas de coluna única ou de várias colunas a outras colunas da tabela de fatos posteriormente.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Realização desbloqueada!
Você carregou com êxito dados públicos em um data warehouse de análise de SQL. Ótimo trabalho!

Agora você pode começar a consultar as tabelas para explorar seus dados. Execute a consulta a seguir para descobrir o total de vendas por marca:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Passos seguintes
Para carregar o conjunto de dados completo, execute o exemplo [carregar o data warehouse de varejo completo da Contoso](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) no repositório de exemplos Microsoft SQL Server.

Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento do SQL data warehouse] [Visão geral do desenvolvimento do SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Create a SQL Analytics data warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Analytics data warehouse]: sql-data-warehouse-overview-load.md
[SQL Analytics data warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
