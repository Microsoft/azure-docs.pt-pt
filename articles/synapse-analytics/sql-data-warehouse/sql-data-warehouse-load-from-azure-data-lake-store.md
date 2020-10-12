---
title: Dados de carga tutorial do Azure Data Lake Storage
description: Utilize a declaração COPY para carregar os dados do Azure Data Lake Storage para Synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/07/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c634b4f7ac3aa1fe83e6ab3f863e998b8d25232c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533942"
---
# <a name="load-data-from-azure-data-lake-storage-for-synapse-sql"></a>Carregar dados do Azure Data Lake Storage para Synapse SQL

Este guia descreve como utilizar a [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) para carregar dados do Azure Data Lake Storage. Para exemplos rápidos sobre a utilização da declaração COPY em todos os métodos de autenticação, visite a seguinte documentação: [Carregue de forma segura os dados utilizando o SQL da Sinapse](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples).

> [!NOTE]  
> Para fornecer problemas de feedback ou relatório na declaração COPY, envie um e-mail para a seguinte lista de distribuição: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Crie a tabela-alvo para carregar dados a partir do Armazenamento do Lago de Dados Azure.
> * Crie a declaração COPY para carregar dados no armazém de dados.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar este tutorial, transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

Para executar este tutorial, você precisa:

* Uma piscina SQL. Consulte [criar uma piscina SQL e consultar dados](create-data-warehouse-portal.md)de consulta.
* Uma conta de armazenamento do Data Lake. Ver [Começar com o Azure Data Lake Storage.](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Para esta conta de armazenamento, terá de configurar ou especificar uma das seguintes credenciais para carregar: Uma chave de conta de armazenamento, chave de assinatura de acesso partilhado (SAS), um utilizador de Aplicação de Diretório Azure ou um utilizador AAD que tenha o papel Azure adequado na conta de armazenamento.

## <a name="create-the-target-table"></a>Criar a tabela-alvo

Ligue-se à sua piscina SQL e crie a tabela-alvo para a que irá carregar. Neste exemplo, estamos a criar uma tabela de dimensão do produto.

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>Criar a declaração COPY

Ligue-se à sua piscina SQL e execute a declaração COPY. Para obter uma lista completa de exemplos, visite a seguinte documentação: [Carregue os dados de carga segura usando o Sinaapse SQL](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples).

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL (IDENTITY = '', SECRET = '')
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>Otimizar a compressão da loja de colunas

Por predefinição, as tabelas são definidas como um índice de loja de colunas agrupado. Depois de concluída uma carga, algumas das linhas de dados podem não ser comprimidas na loja de colunas.  Há uma variedade de razões para isto acontecer. Para saber mais, consulte [gerir os índices de loja de colunas.](sql-data-warehouse-tables-index.md)

Para otimizar o desempenho da consulta e a compressão da loja de colunas após uma carga, reconstrua a tabela para forçar o índice da loja de colunas a comprimir todas as linhas.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Otimizar estatísticas

É melhor criar estatísticas de coluna única imediatamente após uma carga. Há algumas escolhas para as estatísticas. Por exemplo, se criar estatísticas de coluna única em cada coluna, pode levar muito tempo a reconstruir todas as estatísticas. Se sabe que certas colunas não vão estar em pré-consultas, pode saltar a criação de estatísticas sobre essas colunas.

Se decidir criar estatísticas de coluna única em todas as colunas de cada tabela, pode utilizar a amostra de código de procedimento armazenada `prc_sqldw_create_stats` no artigo [de estatística.](sql-data-warehouse-tables-statistics.md)

O exemplo a seguir é um bom ponto de partida para a criação de estatísticas. Cria estatísticas de coluna única em cada coluna na tabela de dimensões, e em cada coluna de junção nas tabelas de factos. Pode sempre adicionar estatísticas únicas ou multi-colunas a outras colunas de tabelas de factos mais tarde.

## <a name="achievement-unlocked"></a>Realização desbloqueada!

Você carregou dados com sucesso no seu armazém de dados. Parabéns!

## <a name="next-steps"></a>Passos seguintes
Os dados de carregamento são o primeiro passo para desenvolver uma solução de armazém de dados utilizando o Azure Synapse Analytics. Confira os nossos recursos de desenvolvimento.

> [!div class="nextstepaction"]
> [Saiba como desenvolver tabelas para armazenamento de dados](sql-data-warehouse-tables-overview.md)

Para mais exemplos de carregamento e referências, consulte a seguinte documentação:
- [Documentação de referência de declaração de CÓPIA](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [Exemplos de COPY para cada método de autenticação](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [INÍCIO DE CÓPIA para uma única tabela](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
