---
title: 'Tutorial: Carregamento de geração 1 Lake armazenamento de dados do Azure para o armazém de dados SQL do Azure | Documentos da Microsoft'
description: Utilize tabelas externas do PolyBase para carregar dados de geração 1 de armazenamento do Azure Data Lake para o Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 32ac5b0841365acfc0a52e343eafc4f3760dffaa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476141"
---
# <a name="load-data-from-azure-data-lake-storage-gen1-to-sql-data-warehouse"></a>Carregar dados de geração 1 de armazenamento do Azure Data Lake para o SQL Data Warehouse
Utilize tabelas externas do PolyBase para carregar dados de geração 1 de armazenamento do Azure Data Lake para o Azure SQL Data Warehouse. Embora seja possível executar consultas ad hoc em dados armazenados no Data Lake Storage Gen1, recomendamos que importar os dados para o SQL Data Warehouse para um melhor desempenho.

> [!div class="checklist"]
> * Crie objetos de base de dados necessários para carregar a partir de geração 1 de armazenamento do Data Lake.
> * Ligar a um diretório de geração 1 de armazenamento do Data Lake.
> * Carregar dados para o Azure SQL Data Warehouse.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar
Antes de começar este tutorial, transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Para executar este tutorial, terá de:

* Aplicação do Active Directory do Azure a utilizar para autenticação de serviço para serviço. Para criar, siga [autenticação do Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Precisa do ID de cliente, a chave e o OAuth2.0 valor do ponto final do Token da sua aplicação do Active Directory para ligar à sua conta de geração 1 de armazenamento do Data Lake do SQL Data Warehouse. Detalhes de como obter estes valores são no link acima. Para o registo de aplicação do Azure Active Directory, utilize o ID da aplicação como o ID de cliente.
> 

* Um armazém de dados SQL do Azure. Ver [criar e a consulta e Azure SQL Data Warehouse](create-data-warehouse-portal.md).

* Uma conta de geração 1 de armazenamento do Data Lake. Ver [introdução ao Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Criar uma credencial
Para aceder à sua conta do Data Lake Storage Gen1, terá de criar uma chave mestra de base de dados para encriptar o segredo da credencial utilizado no próximo passo. Em seguida, cria uma credencial de um âmbito de base de dados, que armazena as credenciais de principal de serviço definidas no AAD. Para aqueles de vocês que usaram o PolyBase para ligar ao Windows Azure Storage Blobs, tenha em atenção que a sintaxe de credencial é diferente.

Para ligar ao Data Lake Storage Gen1, deve **primeiro** criar uma aplicação do Active Directory do Azure, crie uma chave de acesso e conceder o acesso de aplicação para o recurso de geração 1 de armazenamento do Data Lake. Para obter instruções, consulte [autenticar ao Azure Data Lake armazenamento Gen1 usando o Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSG1Credential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLSG1Credential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Criar origem de dados externa
Utilize esta opção [criar origem de dados externa](/sql/t-sql/statements/create-external-data-source-transact-sql) comando para a localização dos dados do arquivo. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage Gen1.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorageGen1
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSG1Credential
);
```

## <a name="configure-data-format"></a>Configurar o formato de dados
Para importar os dados de geração 1 de armazenamento do Data Lake, tem de especificar o formato de ficheiro externo. Este objeto define como os ficheiros são escritos na geração 1 de armazenamento do Data Lake.
Para obter a lista completa, examinar a nossa documentação de T-SQL [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

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
Agora que especificar o formato de origem e de arquivo de dados, está pronto para criar as tabelas externas. Tabelas externas são como interagir com dados externos. O parâmetro de localização pode especificar um ficheiro ou diretório. Se de que especifica um diretório, serão carregados todos os ficheiros dentro do diretório.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage Gen1 root folder.
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
,   DATA_SOURCE = AzureDataLakeStorageGen1
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Considerações de tabela externa
É fácil criar uma tabela externa, mas existem algumas nuances que têm de ser discutido.

Tabelas externas tem rigidez de tipos. Isso significa que cada linha dos dados a ser ingeridos deve satisfazer a definição de esquema de tabela.
Se uma linha não coincide com a definição de esquema, a linha é rejeitada da carga.

As opções de REJECT_TYPE e REJECT_VALUE permitem-lhe definir o número de linhas ou porcentagem dos dados tem de estar presente na tabela final. Durante o carregamento, se o valor de rejeitar for atingido, a carga falha. A causa mais comum de linhas rejeitadas é um erro de correspondência de definição de esquema. Por exemplo, se uma coluna incorretamente é dado o esquema de int quando os dados no arquivo são uma cadeia de caracteres, cada linha irá falhar ao carregar.

Geração 1 de armazenamento do Data Lake utiliza o controlo de acesso baseado em ' (RBAC) da função para controlar o acesso aos dados. Isso significa que o Principal de serviço deve ter permissões de leitura para os diretórios definidos no parâmetro de localização e os filhos do diretório final e ficheiros. Isto permite que o PolyBase para autenticar e carregar dados. 

## <a name="load-the-data"></a>Carregar os dados
Para carregar dados de utilização de geração 1 de armazenamento do Data Lake a [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instrução. 

CTAS cria uma nova tabela e a preenche com os resultados de uma instrução select. CTAS define a nova tabela para ter as mesmas colunas e tipos de dados como os resultados da instrução select. Se selecionar todas as colunas de uma tabela externa, a nova tabela é uma réplica das colunas e tipos de dados em tabela externa.

Neste exemplo, estamos a criar uma tabela distribuída por hash chamada DimProduct a partir do nosso DimProduct_external de tabela externa.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Otimizar a compressão columnstore
Por predefinição, o SQL Data Warehouse armazena a tabela como um índice columnstore em cluster. Após a conclusão de uma carga, algumas das linhas de dados não podem ser comprimidas para o columnstore.  Há uma série de motivos por que isso pode acontecer. Para obter mais informações, consulte [Gerir índices columnstore](sql-data-warehouse-tables-index.md).

Para otimizar o desempenho de consulta e compressão de columnstore depois de uma carga, reconstrua a tabela para forçar o índice columnstore para comprimir todas as linhas.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Otimizar as estatísticas
É melhor criar estatísticas de coluna única imediatamente após uma carga. Existem algumas opções para estatísticas. Por exemplo, se criar estatísticas de coluna única em cada coluna poderá demorar muito tempo a recriar todas as estatísticas. Se souber que determinados colunas não vão ser em predicados de consulta, pode ignorar criar estatísticas nessas colunas.

Se optar por criar estatísticas de coluna única em cada coluna de cada tabela, pode utilizar o exemplo de código do procedimento armazenado `prc_sqldw_create_stats` no [estatísticas](sql-data-warehouse-tables-statistics.md) artigo.

O exemplo seguinte é um bom ponto de partida para a criação de estatísticas. Ele cria estatísticas de coluna única em cada coluna na tabela de dimensões e em cada coluna junção das tabelas de fatos. Pode sempre adicionar estatísticas única ou várias colunas de outras colunas da tabela de fatos mais tarde.

## <a name="achievement-unlocked"></a>Medalha desbloqueada!
Carregou com êxito dados para o Azure SQL Data Warehouse. Parabéns!

## <a name="next-steps"></a>Passos Seguintes 
Neste tutorial, criou tabelas externas para definir a estrutura dos dados armazenados no Data Lake Storage Gen1 e, em seguida, utilizou a instrução PolyBase CREATE TABLE AS SELECT para carregar dados para o armazém de dados. 

Fez tudo isto:
> [!div class="checklist"]
> * Objetos de base de dados criada necessários para carregar a partir de geração 1 de armazenamento do Data Lake.
> * Ligado a um diretório de geração 1 de armazenamento do Data Lake.
> * Dados carregados para o Azure SQL Data Warehouse.
> 

O carregamento de dados é a primeira etapa para desenvolver uma solução de armazém de dados com o SQL Data Warehouse. Confira nossos recursos de desenvolvimento.

> [!div class="nextstepaction"]
>[Saiba como desenvolver tabelas no armazém de dados SQL](sql-data-warehouse-tables-overview.md)




