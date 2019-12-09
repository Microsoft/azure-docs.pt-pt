---
title: Tutorial de carregamento de dados de Azure Data Lake Storage
description: Use tabelas externas do polybase para carregar dados de Azure Data Lake Storage para o SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 12/06/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fdbf0eb849549071b4cbbb961c9e9f71fce1faf8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923630"
---
# <a name="load-data-from-azure-data-lake-storage-to-sql-data-warehouse"></a>Carregar dados de Azure Data Lake Storage para SQL Data Warehouse
Este guia descreve como usar tabelas externas do polybase para carregar dados de Azure Data Lake Storage para o SQL Data Warehouse do Azure. Embora seja possível executar consultas ad hoc em dados armazenados no Data Lake Storage, é recomendável importar os dados para o SQL Data Warehouse para obter o melhor desempenho. 

> [!NOTE]  
> Uma alternativa ao carregamento é a [instrução de cópia](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) atualmente em visualização pública. Para fornecer comentários sobre a instrução de cópia, envie um email para a seguinte lista de distribuição: sqldwcopypreview@service.microsoft.com.
>
> [!div class="checklist"]

> * Crie objetos de banco de dados necessários para carregar de Data Lake Storage.
> * Conecte-se a um diretório Data Lake Storage.
> * Carregar dados no Azure SQL Data Warehouse.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar
Antes de começar este tutorial, transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Para executar este tutorial, você precisa de:

* Um SQL Data Warehouse do Azure. Consulte [criar e consultar e SQL data warehouse do Azure](create-data-warehouse-portal.md).
* Uma conta de Data Lake Storage. Consulte [introdução ao Azure data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md). Para essa conta de armazenamento, você precisará configurar ou especificar uma das seguintes credenciais a serem carregadas: uma chave de conta de armazenamento, um usuário de aplicativo do Azure Directory ou um usuário do AAD que tem a função RBAC apropriada para a conta de armazenamento. 

##  <a name="create-a-credential"></a>Criar uma credencial
Você pode ignorar esta seção e continuar a "criar a fonte de dados externa" ao autenticar usando passagem do AAD. Uma credencial no escopo do banco de dados não precisa ser criada ou especificada ao usar passagem do AAD, mas verifique se o usuário do AAD tem a função RBAC apropriada (leitor de dados de blob de armazenamento, colaborador ou função de proprietário) para a conta de armazenamento. Mais detalhes são descritos [aqui](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260). 

Para acessar sua conta de Data Lake Storage, você precisará criar uma chave mestra de banco de dados para criptografar seu segredo de credencial. Em seguida, você cria uma credencial no escopo do banco de dados para armazenar seu segredo. Ao autenticar usando entidades de serviço (usuário do aplicativo do Azure Directory), a credencial no escopo do banco de dados armazena as credenciais da entidade de serviço configuradas no AAD. Você também pode usar a credencial no escopo do banco de dados para armazenar a chave da conta de armazenamento para Gen2.

Para se conectar ao Data Lake Storage usando entidades de serviço, **primeiro** você deve criar um aplicativo Azure Active Directory, criar uma chave de acesso e conceder ao aplicativo acesso à conta de data Lake Storage. Para obter instruções, consulte [autenticar para Azure data Lake Storage usando Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principal:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Criar a fonte de dados externa
Use este comando [criar fonte de dados externa](/sql/t-sql/statements/create-external-data-source-transact-sql) para armazenar o local dos dados. Se você estiver autenticando com passagem do AAD, o parâmetro CREDENTIAL não será necessário. 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Configurar formato de dados
Para importar os dados de Data Lake Storage, você precisa especificar o formato de arquivo externo. Esse objeto define como os arquivos são gravados em Data Lake Storage.
Para obter a lista completa, consulte nossa documentação do T-SQL [criar formato de arquivo externo](/sql/t-sql/statements/create-external-file-format-transact-sql)

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

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

## <a name="create-the-external-tables"></a>Criar as tabelas externas
Agora que você especificou a fonte de dados e o formato de arquivo, você está pronto para criar as tabelas externas. As tabelas externas são como você interage com os dados externos. O parâmetro Location pode especificar um arquivo ou um diretório. Se ele especificar um diretório, todos os arquivos dentro do diretório serão carregados.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Considerações sobre a tabela externa
A criação de uma tabela externa é fácil, mas há algumas nuances que precisam ser discutidas.

As tabelas externas são fortemente tipadas. Isso significa que cada linha dos dados que estão sendo ingeridos deve satisfazer a definição de esquema de tabela.
Se uma linha não corresponder à definição de esquema, a linha será rejeitada da carga.

As opções REJECT_TYPE e REJECT_VALUE permitem que você defina quantas linhas ou qual porcentagem dos dados devem estar presentes na tabela final. Durante o carregamento, se o valor de rejeição for atingido, o carregamento falhará. A causa mais comum de linhas rejeitadas é uma incompatibilidade de definição de esquema. Por exemplo, se uma coluna tiver dado incorretamente o esquema de int quando os dados no arquivo forem uma cadeia de caracteres, todas as linhas não serão carregadas.

O Data Lake Storage Gen1 usa o RBAC (controle de acesso baseado em função) para controlar o acesso aos dados. Isso significa que a entidade de serviço deve ter permissões de leitura para os diretórios definidos no parâmetro Location e para os filhos do diretório final e dos arquivos. Isso permite que o polybase autentique e carregue esses dados. 

## <a name="load-the-data"></a>Carregar os dados
Para carregar dados de Data Lake Storage use a instrução [CREATE TABLE as Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) . 

CTAS cria uma nova tabela e a popula com os resultados de uma instrução SELECT. CTAS define a nova tabela para ter as mesmas colunas e tipos de dados que os resultados da instrução SELECT. Se você selecionar todas as colunas de uma tabela externa, a nova tabela será uma réplica das colunas e dos tipos de dados na tabela externa.

Neste exemplo, estamos criando uma tabela distribuída de hash chamada DimProduct da nossa tabela externa DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Otimizar a compactação columnstore
Por padrão, o SQL Data Warehouse armazena a tabela como um índice columnstore clusterizado. Após a conclusão de uma carga, algumas das linhas de dados podem não ser compactadas no columnstore.  Há uma variedade de motivos pelos quais isso pode acontecer. Para saber mais, consulte [gerenciar índices columnstore](sql-data-warehouse-tables-index.md).

Para otimizar o desempenho da consulta e a compactação columnstore após uma carga, recompile a tabela para forçar o índice columnstore a compactar todas as linhas.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Otimizar estatísticas
É melhor criar estatísticas de coluna única imediatamente após um carregamento. Há algumas opções para estatísticas. Por exemplo, se você criar estatísticas de coluna única em cada coluna, pode levar muito tempo para recompilar todas as estatísticas. Se você souber que determinadas colunas não serão em predicados de consulta, você poderá ignorar a criação de estatísticas nessas colunas.

Se você decidir criar estatísticas de coluna única em cada coluna de cada tabela, poderá usar o exemplo de código de procedimento armazenado `prc_sqldw_create_stats` no artigo [estatísticas](sql-data-warehouse-tables-statistics.md) .

O exemplo a seguir é um bom ponto de partida para a criação de estatísticas. Ele cria estatísticas de coluna única em cada coluna na tabela de dimensões e em cada coluna de junção nas tabelas de fatos. Você sempre pode adicionar estatísticas de coluna única ou de várias colunas a outras colunas da tabela de fatos posteriormente.

## <a name="achievement-unlocked"></a>Realização desbloqueada!
Você carregou com êxito os dados no Azure SQL Data Warehouse. Ótimo trabalho!

## <a name="next-steps"></a>Passos seguintes 
Neste tutorial, você criou tabelas externas para definir a estrutura de dados armazenados em Data Lake Storage Gen1 e, em seguida, usou a instrução CREATE TABLE do polybase como SELECT para carregar dados em seu data warehouse. 

Fez tudo isto:
> [!div class="checklist"]
> * Foram criados objetos de banco de dados necessários para carregar de Data Lake Storage.
> * Conectado a um Data Lake Storage Directory.
> * Dados carregados no Azure SQL Data Warehouse.
>

Carregar dados é a primeira etapa para desenvolver uma solução de data warehouse usando SQL Data Warehouse. Confira nossos recursos de desenvolvimento.

> [!div class="nextstepaction"]
> [Saiba como desenvolver tabelas no SQL Data Warehouse](sql-data-warehouse-tables-overview.md)
