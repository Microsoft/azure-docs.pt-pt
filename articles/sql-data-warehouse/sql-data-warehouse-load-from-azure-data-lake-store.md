---
title: Dados de carga tutorial do Armazenamento do Lago De Dados Azure
description: Utilize tabelas externas da PolyBase para carregar dados do Armazenamento do Lago De Dados Azure para análise sql.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 03/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b0b9cffe0b69545a6d0219941b48ac9eb0f399b3
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300592"
---
# <a name="load-data-from-azure-data-lake-storage-for-sql-analytics"></a>Dados de carga do Armazenamento de Lagos De Dados Azure para SQL Analytics
Este guia descreve como usar tabelas externas da PolyBase para carregar dados do Armazenamento do Lago De Dados Azure. Embora possa fazer consultas adhoc em dados armazenados no Data Lake Storage, recomendamos a importação dos dados para um melhor desempenho. 

> [!NOTE]  
> Uma alternativa ao carregamento é a [declaração copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) atualmente em pré-visualização pública.  A declaração copy fornece a maior flexibilidade. Para fornecer feedback sobre a declaração do COPY, envie um e-mail para a seguinte lista de distribuição: sqldwcopypreview@service.microsoft.com.
>
> [!div class="checklist"]

> * Crie objetos de base de dados necessários para carregar a partir do Armazenamento do Lago de Dados.
> * Ligue-se a um diretório de armazenamento de Data Lake.
> * Carregue os dados no armazém de dados.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar
Antes de começar este tutorial, transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Para executar este tutorial, precisa de:

* Uma piscina SQL. Consulte [Criar um pool SQL e dados de consulta.](create-data-warehouse-portal.md)
* Uma conta de armazenamento de data lake. Veja começar com o Armazenamento do [Lago De Dados Azure.](../data-lake-store/data-lake-store-get-started-portal.md) Para esta conta de armazenamento, terá de configurar ou especificar uma das seguintes credenciais para carregar: Uma chave de conta de armazenamento, um utilizador de Aplicação de Diretório Azure ou um utilizador AAD que tenha a função RBAC adequada para a conta de armazenamento. 

##  <a name="create-a-credential"></a>Criar uma credencial
Pode saltar esta secção e proceder a "Criar a fonte de dados externa" ao autenticar utilizando o pass-through AAD. Não é necessária uma credencial de base de dados com âmbito de aplicação para a balança de dados (Storage Blob Data Reader, Contributor ou Owner Role) na conta de armazenamento. Mais detalhes são delineados [aqui.](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260) 

Para aceder à sua conta de Armazenamento de Data Lake, terá de criar uma Chave Master de Base de Dados para encriptar o seu segredo credencial. Em seguida, cria uma Credencial de Base de Dados Scoped para armazenar o seu segredo. Ao autenticar utilizando os principais de serviço (utilizador da Aplicação de Diretório Sonártica, a Base de Dados Scoped Credenciais armazena as credenciais principais do serviço criadas em AAD. Também pode utilizar a Credencial scoped base de dados para armazenar a chave da conta de armazenamento para gen2.

Para se ligar ao Data Lake Storage utilizando os principais de serviço, tem **primeiro** de criar uma Aplicação de Diretório Ativo Azure, criar uma chave de acesso e conceder o acesso à aplicação à conta data Lake Storage. Para obter instruções, consulte [Authenticate to Azure Data Lake Storage utilizando o Diretório Ativo](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

Inicie sessão na sua piscina SQL com um utilizador com permissões de nível CONTROL e execute as seguintes declarações SQL na sua base de dados:

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
Utilize este comando CRIAR FONTE DE [DADOS EXTERNOs](/sql/t-sql/statements/create-external-data-source-transact-sql) para armazenar a localização dos dados. Se estiver a autenticar com passagem aAD, o parâmetro CREDENTIAL não é necessário. 

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
Para importar os dados do Armazenamento do Lago de Dados, é necessário especificar o Formato de Ficheiro Externo. Este objeto define como os ficheiros são escritos no Armazenamento de Data Lake.
Para a lista completa, veja a nossa documentação T-SQL [CRIAR FORMATO DE ARQUIVO EXTERNO](/sql/t-sql/statements/create-external-file-format-transact-sql)

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

## <a name="create-the-external-tables"></a>Criar as Tabelas Externas
Agora que especificou a fonte de dados e o formato de ficheiro, está pronto para criar as tabelas externas. As tabelas externas são como interage com dados externos. O parâmetro de localização pode especificar um ficheiro ou um diretório. Se especificar um diretório, todos os ficheiros dentro do diretório serão carregados.

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

## <a name="external-table-considerations"></a>Considerações externas da tabela
Criar uma mesa externa é fácil, mas há algumas nuances que precisam de ser discutidas.

As tabelas externas são fortemente dactilografadas. Isto significa que cada linha dos dados ingeridos deve satisfazer a definição de esquema de mesa.
Se uma linha não corresponder à definição de esquema, a linha é rejeitada da carga.

As opções REJECT_TYPE e REJECT_VALUE permitem definir quantas linhas ou qual a percentagem dos dados que devem estar presentes na tabela final. Durante a carga, se o valor de rejeição for atingido, a carga falha. A causa mais comum das linhas rejeitadas é uma incompatibilidade de definição de esquemas. Por exemplo, se uma coluna for incorretamente dada ao esquema do int quando os dados do ficheiro forem uma corda, cada linha não carregará.

Data Lake Storage Gen1 usa controlo de acesso baseado em funções (RBAC) para controlar o acesso aos dados. Isto significa que o Diretor de Serviço deve ter lido permissões aos diretórios definidos no parâmetro de localização e às crianças do diretório final e ficheiros. Isto permite à PolyBase autenticar e carregar esses dados. 

## <a name="load-the-data"></a>Carregar os dados
Para carregar dados do Armazenamento de Data Lake, utilize a declaração [CREATE TABLE AS SELECT (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 

O CTAS cria uma nova tabela e povoa-a com os resultados de uma declaração selecionada. O CTAS define a nova tabela para ter as mesmas colunas e tipos de dados que os resultados da declaração selecionada. Se selecionar todas as colunas de uma tabela externa, a nova tabela é uma réplica das colunas e tipos de dados na tabela externa.

Neste exemplo, estamos a criar uma tabela distribuída por hash chamada DimProduct a partir da nossa Tabela Externa DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Otimizar a compressão da loja de colunas
Por predefinição, as tabelas são definidas como um índice de loja de colunas agrupada. Após a conclusão de uma carga, algumas das linhas de dados podem não ser comprimidas na loja de colunas.  Há uma variedade de razões pelas quais isto pode acontecer. Para saber mais, consulte a gestão dos índices da [columnstore.](sql-data-warehouse-tables-index.md)

Para otimizar o desempenho da consulta e a compressão da loja de colunas após uma carga, reconstrua a tabela para forçar o índice da loja de colunas a comprimir todas as linhas.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Otimizar as estatísticas
É melhor criar estatísticas de coluna única imediatamente após uma carga. Há algumas opções para as estatísticas. Por exemplo, se criarmos estatísticas de uma coluna única em todas as colunas, pode levar muito tempo a reconstruir todas as estatísticas. Se sabe que certas colunas não vão estar em predicados de consulta, pode ignorar a criação de estatísticas nessas colunas.

Se decidir criar estatísticas de coluna única em cada coluna de cada tabela, pode utilizar a amostra de código de procedimento armazenada `prc_sqldw_create_stats` no artigo [de estatística.](sql-data-warehouse-tables-statistics.md)

O exemplo que se segue é um bom ponto de partida para a criação de estatísticas. Cria estatísticas de uma coluna única sobre cada coluna na tabela de dimensões e em cada coluna de junção nas tabelas de factos. Pode sempre adicionar estatísticas únicas ou multi-colunas a outras colunas de tabelas de factos mais tarde.

## <a name="achievement-unlocked"></a>Realização desbloqueada!
Carregou com sucesso dados no seu armazém de dados. Grande trabalho!

## <a name="next-steps"></a>Passos seguintes 
Neste tutorial, criou tabelas externas para definir a estrutura para dados armazenados em Data Lake Storage Gen1, e depois utilizou a declaração PolyBase CREATE TABLE AS SELECT para carregar dados no seu armazém de dados. 

Fez tudo isto:
> [!div class="checklist"]
>
> * Criou objetos de base de dados necessários para carregar a partir do Armazenamento do Lago de Dados.
> * Ligado a um diretório de Armazenamento de Data Lake.
> * Carregado dados para o armazém de dados.
>

Os dados de carregamento são o primeiro passo para o desenvolvimento de uma solução de armazém de dados utilizando a Azure Synapse Analytics. Confira os nossos recursos de desenvolvimento.

> [!div class="nextstepaction"]
> [Saiba como desenvolver tabelas para armazenamento de dados](sql-data-warehouse-tables-overview.md)
