---
title: Carregue os dados de retalho da Contoso para piscinas SQL dedicadas
description: Utilize comandos PolyBase e T-SQL para carregar duas tabelas dos dados de retalho Contoso em piscinas SQL dedicadas.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b1afcdfa74245eb566663d5dec6ce2e2276fbdc8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568238"
---
# <a name="load-contoso-retail-data-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Carregue os dados de retalho da Contoso em piscinas SQL dedicadas em Azure Synapse Analytics

Neste tutorial, aprende-se a usar comandos PolyBase e T-SQL para carregar duas tabelas a partir dos dados de retalho da Contoso em piscinas SQL dedicadas.

Neste tutorial você:

1. Configure a PolyBase para carregar a partir do armazenamento de bolhas Azure
2. Carregue os dados públicos na sua base de dados
3. Execute otimizações após o fim da carga.

## <a name="before-you-begin"></a>Antes de começar

Para executar este tutorial, você precisa de uma conta Azure que já tem uma piscina SQL dedicada. Se não tiver um armazém de dados a forrado, consulte [criar um armazém de dados e definir a regra de firewall ao nível do servidor](create-data-warehouse-portal.md).

## <a name="configure-the-data-source"></a>Configure a fonte de dados

A PolyBase utiliza objetos externos T-SQL para definir a localização e atributos dos dados externos. As definições de objetos externos são armazenadas em piscinas SQL dedicadas. Os dados são armazenados externamente.

## <a name="create-a-credential"></a>Criar uma credencial

**Salta este passo** se estiveres a carregar os dados públicos do Contoso. Não precisa de acesso seguro aos dados públicos, uma vez que já está acessível a ninguém.

**Não salte este passo** se estiver a usar este tutorial como modelo para carregar os seus próprios dados. Para aceder aos dados através de uma credencial, utilize o seguinte script para criar uma credencial de âmbito de base de dados. Em seguida, utilize-o ao definir a localização da fonte de dados.

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

## <a name="create-the-external-data-source"></a>Criar a fonte de dados externa

Utilize este comando [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para armazenar a localização dos dados e o tipo de dados.

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH
(  
    TYPE = Hadoop
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
);
```

> [!IMPORTANT]
> Se optar por tornar públicos os seus recipientes de armazenamento de bolhas de azure, lembre-se que, como titular dos dados, será cobrado por encargos de saída de dados quando os dados saírem do centro de dados.

## <a name="configure-the-data-format"></a>Configurar o formato de dados

Os dados são armazenados em ficheiros de texto no armazenamento de blob Azure, e cada campo é separado com um delimiter. No SSMS, executar o seguinte comando CREATE EXTERNAL FILE FORMAT para especificar o formato dos dados nos ficheiros de texto. Os dados do Contoso não estão recomprimidos e os tubos delimitados.

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

## <a name="create-the-schema-for-the-external-tables"></a>Criar o esquema para as tabelas externas

Agora que especificou a fonte de dados e o formato de ficheiro, está pronto para criar o esquema para as tabelas externas.

Para criar um local para armazenar os dados do Contoso na sua base de dados, crie um esquema.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>Criar as tabelas externas

Execute o seguinte script para criar as tabelas externas DimProduct e FactOnlineSales. Tudo o que está a fazer aqui é definir nomes de colunas e tipos de dados, e encaderna-los à localização e formato dos ficheiros de armazenamento de bolhas Azure. A definição é armazenada no armazém de dados e os dados ainda estão na Bolha de Armazenamento Azure.

O parâmetro  **LOCALIZAÇÃO** é a pasta sob a pasta raiz na Bolha de Armazenamento Azure. Cada mesa está numa pasta diferente.

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

## <a name="load-the-data"></a>Carregar os dados

Existem diferentes formas de aceder a dados externos.  Pode consultar os dados diretamente das tabelas externas, carregar os dados em novas tabelas no armazém de dados ou adicionar dados externos às tabelas de armazém de dados existentes.  

### <a name="create-a-new-schema"></a>Criar um novo esquema

O CTAS cria uma nova tabela que contém dados.  Primeiro, crie um esquema para os dados do contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Carregue os dados em novas tabelas

Para carregar os dados do armazenamento de bolhas Azure na tabela do armazém de dados, utilize a declaração [CREATE TABLE AS SELECT (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Carregar com [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) aproveita as tabelas externas fortemente dactilografadas que criou. Para carregar os dados em novas tabelas, utilize uma declaração CTAS por tabela.

O CTAS cria uma nova tabela e povoa-a com os resultados de uma declaração selecionada. O CTAS define a nova tabela para ter as mesmas colunas e tipos de dados que os resultados da declaração selecionada. Se selecionar todas as colunas de uma tabela externa, a nova tabela será uma réplica das colunas e tipos de dados na tabela externa.

Neste exemplo, criamos tanto a dimensão como a tabela de factos como tabelas de haxixe distribuídas.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>Acompanhe o progresso da carga

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

## <a name="optimize-columnstore-compression"></a>Otimizar a compressão da loja de colunas

Por padrão, as piscinas SQL dedicadas armazenam a tabela como um índice de loja de colunas agrupado. Depois de concluída uma carga, algumas das linhas de dados podem não ser comprimidas na loja de colunas.  Há diferentes razões para isto acontecer. Para saber mais, consulte [gerir os índices de loja de colunas.](sql-data-warehouse-tables-index.md)

Para otimizar o desempenho da consulta e a compressão da loja de colunas após uma carga, reconstrua a tabela para forçar o índice da loja de colunas a comprimir todas as linhas.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Para obter mais informações sobre a manutenção dos índices de loja de colunas, consulte o artigo [índices de loja de colunas.](sql-data-warehouse-tables-index.md)

## <a name="optimize-statistics"></a>Otimizar estatísticas

É melhor criar estatísticas de coluna única imediatamente após uma carga. Se sabes que certas colunas não vão estar em pré-doutoramentos, podes saltar a criação de estatísticas nessas colunas. Se criar estatísticas de coluna única em cada coluna, pode levar muito tempo para reconstruir todas as estatísticas.

Se decidir criar estatísticas de coluna única em todas as colunas de cada tabela, pode utilizar a amostra de código de procedimento armazenada `prc_sqldw_create_stats` no artigo [de estatística.](sql-data-warehouse-tables-statistics.md)

O exemplo a seguir é um bom ponto de partida para a criação de estatísticas. Cria estatísticas de coluna única em cada coluna na tabela de dimensões, e em cada coluna de junção nas tabelas de factos. Pode sempre adicionar estatísticas únicas ou multi-colunas a outras colunas de tabelas de factos mais tarde.

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

Você carregou com sucesso dados públicos no seu armazém de dados. Parabéns!

Pode agora começar a consultar as tabelas para explorar os seus dados. Executar a seguinte consulta para descobrir o total de vendas por marca:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Passos seguintes

Para carregar o conjunto completo de dados, executar o exemplo [carregue o armazém completo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) de dados de retalho Contoso do repositório de amostras do Microsoft SQL Server.
Para obter mais dicas de desenvolvimento, consulte [decisões de design e técnicas de codificação para armazéns de dados.](sql-data-warehouse-overview-develop.md)
