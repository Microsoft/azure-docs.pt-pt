---
title: Dados de retalho Load Contoso
description: Utilize comandos PolyBase e T-SQL para carregar duas tabelas dos dados do Retalhista Contoso para a Azure SQL Analytics.
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
ms.openlocfilehash: af505d7614b527d6dc7e1ce54136578d67824cf9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721171"
---
# <a name="load-contoso-retail-data-to-a-sql-analytics-data-warehouse"></a>Carregar dados de retalho de Contoso para um armazém de dados SQL Analytics

Neste tutorial, aprende-se a usar comandos PolyBase e T-SQL para carregar duas tabelas dos dados do Retalhista Contoso num armazém de dados da SQL Analytics. 

Neste tutorial, você vai:

1. Configure PolyBase para carregar a partir do armazenamento de blob Azure
2. Carregue dados públicos na sua base de dados
3. Execute otimizações após a carga estar terminada.

## <a name="before-you-begin"></a>Antes de começar
Para executar este tutorial, precisa de uma conta Azure que já tenha um armazém de dados SQL Analytics. Se não tiver um armazém de dados aprovisionado, consulte Criar um armazém de [dados e definir a regra de firewall ao nível do servidor](create-data-warehouse-portal.md).

## <a name="1-configure-the-data-source"></a>1. Configure a fonte de dados
A PolyBase utiliza objetos externos T-SQL para definir a localização e os atributos dos dados externos. As definições externas de objetos são armazenadas no seu armazém de dados SQL Analytics. Os dados são armazenados externamente.

### <a name="11-create-a-credential"></a>1.1. Criar uma credencial
**Ignore este passo** se estiver a carregar os dados públicos do Contoso. Não precisa de acesso seguro aos dados públicos, uma vez que já é acessível a ninguém.

**Não ignore este passo** se estiver a usar este tutorial como modelo para carregar os seus próprios dados. Para aceder aos dados através de uma credencial, utilize o seguinte script para criar uma credencial com um espaço de dados. Em seguida, use-o ao definir a localização da fonte de dados.

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
Utilize este comando CRIAR FONTE DE [DADOS EXTERNOs](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-ver15) para armazenar a localização dos dados e o tipo de dados. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Se optar por tornar públicos os seus contentores de armazenamento de blob azul, lembre-se que, como proprietário de dados, será cobrado por taxas de egress de dados quando os dados saem do centro de dados. 
> 
> 

## <a name="2-configure-data-format"></a>2. Configurar o formato de dados
Os dados são armazenados em ficheiros de texto no armazenamento de blob Azure, e cada campo é separado com um delimitador. No SSMS, execute o seguinte comando CREATE EXTERNAL FILE FORMAT para especificar o formato dos dados nos ficheiros de texto. Os dados contoso são descomprimidos e o tubo deslimitado.

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

## <a name="3-create-the-external-tables"></a>3. Criar as tabelas externas
Agora que especificou a fonte de dados e o formato de ficheiros, está pronto para criar as tabelas externas. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Crie um esquema para os dados.
Para criar um local para armazenar os dados contoso na sua base de dados, crie um esquema.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Crie as tabelas externas.
Executar o seguinte script para criar as tabelas externas DimProduct e FactOnlineSales. Tudo o que está a fazer aqui é definir nomes de colunas e tipos de dados, e encadi-los à localização e formato dos ficheiros de armazenamento de blob Azure. A definição é armazenada no armazém de dados SQL Analytics e os dados ainda estão no Blob de Armazenamento Azure.

O parâmetro **LOCATION** é a pasta sob a pasta raiz no Blob de Armazenamento Azure. Cada mesa está numa pasta diferente.

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

## <a name="4-load-the-data"></a>4. Carregar os dados
Existem diferentes formas de aceder a dados externos.  Pode consultar dados diretamente das tabelas externas, carregar os dados em novas tabelas no armazém de dados ou adicionar dados externos às tabelas de depósitos de dados existentes.  

### <a name="41-create-a-new-schema"></a>4.1. Criar um novo esquema
O CTAS cria uma nova tabela que contém dados.  Primeiro, crie um esquema para os dados contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Carregue os dados em novas tabelas
Para carregar dados do armazenamento de blob Azure na tabela do armazém de dados, utilize a declaração [CREATE TABLE AS SELECT (Transact-SQL).](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) Carregar com [CTAS](sql-data-warehouse-develop-ctas.md) aproveita as tabelas externas fortemente digitadas que criou. Para carregar os dados em novas tabelas, utilize uma declaração CTAS por tabela. 
 
O CTAS cria uma nova tabela e povoa-a com os resultados de uma declaração selecionada. O CTAS define a nova tabela para ter as mesmas colunas e tipos de dados que os resultados da declaração selecionada. Se selecionar todas as colunas de uma tabela externa, a nova tabela será uma réplica das colunas e tipos de dados na tabela externa.

Neste exemplo, criamos tanto a dimensão como a tabela de factos como mesas distribuídas pelo hash. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 Acompanhe o progresso da carga
Pode acompanhar o progresso da sua carga utilizando pontos de vista dinâmicos de gestão (DMVs). 

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

## <a name="5-optimize-columnstore-compression"></a>5. Otimizar a compressão da loja de colunas
Por padrão, o armazém de dados SQL Analytics armazena a tabela como um índice de lojas de colunas agrupadas. Após a conclusão de uma carga, algumas das linhas de dados podem não ser comprimidas na loja de colunas.  Há razões diferentes para isto acontecer. Para saber mais, consulte a gestão dos índices da [columnstore.](sql-data-warehouse-tables-index.md)

Para otimizar o desempenho da consulta e a compressão da loja de colunas após uma carga, reconstrua a tabela para forçar o índice da loja de colunas a comprimir todas as linhas. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Para obter mais informações sobre a manutenção dos índices da columnstore, consulte o artigo de índices de lojas de [gestão.](sql-data-warehouse-tables-index.md)

## <a name="6-optimize-statistics"></a>6. Otimizar as estatísticas
É melhor criar estatísticas de uma coluna única imediatamente após uma carga. Se souberes que certas colunas não vão estar em predicados de consulta, podes ignorar a criação de estatísticas nessas colunas. Se criarestatísticas de coluna única em cada coluna, pode levar muito tempo para reconstruir todas as estatísticas. 

Se decidir criar estatísticas de coluna única em cada coluna de cada tabela, pode utilizar a amostra de código de procedimento armazenada `prc_sqldw_create_stats` no artigo [de estatística.](sql-data-warehouse-tables-statistics.md)

O exemplo que se segue é um bom ponto de partida para a criação de estatísticas. Cria estatísticas de uma coluna única sobre cada coluna na tabela de dimensões e em cada coluna de junção nas tabelas de factos. Pode sempre adicionar estatísticas únicas ou multi-colunas a outras colunas de tabelas de factos mais tarde.

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
Carregou com sucesso dados públicos num armazém de dados da SQL Analytics. Grande trabalho!

Agora pode começar a consultar as tabelas para explorar os seus dados. Execute a seguinte consulta para descobrir o total de vendas por marca:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Passos Seguintes
Para carregar o conjunto completo de dados, execute o exemplo carregue o armazém completo de dados de retalho de [Contoso](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) do repositório de amostras do Servidor Microsoft SQL.
Para obter mais dicas de desenvolvimento, consulte decisões de [design e técnicas de codificação para armazéns](sql-data-warehouse-overview-develop.md)de dados.
