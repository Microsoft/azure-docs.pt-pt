---
title: Copiar dados de e para SQL Server com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como mover dados para e da base de dados do SQL Server que está no local ou numa VM do Azure com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: a6767c7c8931898c44fd748dbe4299b8ed23eb9c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443277"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Copiar dados de e para SQL Server com o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory, que está a utilizar:"]
> * [Versão 1](v1/data-factory-sqlserver-connector.md)
> * [Versão atual](connector-sql-server.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para uma base de dados do SQL Server. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de e para uma base de dados do SQL Server para qualquer arquivo de dados de sink suportados. Em alternativa, pode copiar dados de qualquer arquivo de dados de origem suportada para uma base de dados do SQL Server. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector do SQL Server suporta:

- Versões do SQL Server 2016, 2014, 2012, 2008 R2, 2008 e 2005.
- Copiar dados utilizando a autenticação de SQL ou o Windows.
- Como uma origem, obter dados utilizando uma consulta SQL ou um procedimento armazenado.
- Como um sink, acrescentando dados a uma tabela de destino ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) não é suportada por este conector agora. Para contornar, pode utilizar um [conector do ODBC genérico](connector-odbc.md) e um driver ODBC do SQL Server. Siga [esta orientação](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) com ODBC download e a ligação de cadeia de caracteres as configurações de driver.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar dados de cópia da base de dados do SQL Server que não esteja acessível ao público, terá de configurar um runtime de integração autoalojado. Para obter mais informações, consulte [integration runtime autoalojado](create-self-hosted-integration-runtime.md). O runtime de integração fornece um controlador de base de dados do SQL Server interno. Não precisa de instalar manualmente a qualquer driver quando copia dados de ou para a base de dados do SQL Server.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector de base de dados do SQL Server.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do SQL Server:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **SqlServer**. | Sim |
| connectionString |Especifique **connectionString** informações necessárias para ligar à base de dados do SQL Server com a autenticação SQL ou autenticação do Windows. Consulte os exemplos seguintes.<br/>Marcar esse campo como **SecureString** armazena de forma segura no Azure Data Factory. Também pode colocar uma palavra-passe no Azure Key Vault. Se é autenticação de SQL, extrair o `password` configuração fora de cadeia de ligação. Para obter mais informações, consulte o exemplo JSON a tabela a seguir e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| userName |Especifique um nome de utilizador se utilizar a autenticação do Windows. Um exemplo é **domainname\\nome de utilizador**. |Não |
| password |Especifique uma palavra-passe da conta de utilizador que especificou para o nome de utilizador. Marcar esse campo como **SecureString** armazena de forma segura no Azure Data Factory. Também pode [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Não |
| connectVia | Isso [runtime de integração](concepts-integration-runtime.md) é utilizada para ligar ao arquivo de dados. Pode usar um runtime de integração autoalojado ou o runtime de integração do Azure, se seu arquivo de dados está acessível ao público. Se não for especificado, é utilizado o runtime de integração do Azure padrão. |Não |

>[!TIP]
>Se atingir um erro com o código de erro "UserErrorFailedToConnectToSqlServer" e uma mensagem como "o limite de sessão para a base de dados é XXX e foi atingido", adicione `Pooling=false` para sua cadeia de ligação e tente novamente.

**Exemplo 1: Utilizar a autenticação SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: Utilizar a autenticação de SQL com uma palavra-passe no Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 3: Utilizar a autenticação do Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados do SQL Server.

Para copiar dados de e para uma base de dados do SQL Server, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados tem de ser definida **SqlServerTable**. | Sim |
| tableName |Esta propriedade é o nome da tabela ou vista de instância de base de dados do SQL Server que o serviço ligado refere-se a. | Não para a origem, Sim para o sink |

**Exemplo**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para serem utilizados definir as atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com a origem do SQL Server e de sink.

### <a name="sql-server-as-a-source"></a>SQL Server como uma origem

Para copiar dados do SQL Server, defina o tipo de origem na atividade copy na atividade **SqlSource**. As seguintes propriedades são suportadas na secção de origem de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida **SqlSource**. | Sim |
| sqlReaderQuery |Utilize a consulta SQL personalizada para ler os dados. Um exemplo é `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Esta propriedade é o nome do procedimento armazenado que lê dados da tabela de origem. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Esses parâmetros são para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |

**Pontos a serem observados:**

- Se **sqlReaderQuery** especificado para **SqlSource**, a atividade de cópia executa esta consulta em relação à origem de SQL Server para obter os dados. Também pode especificar um procedimento armazenado, especificando **sqlReaderStoredProcedureName** e **storedProcedureParameters** se o procedimento armazenado utiliza parâmetros.
- Se não especificar qualquer um **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, as colunas definidas na secção "estrutura" do conjunto de dados JSON são utilizadas para construir uma consulta. A consulta `select column1, column2 from mytable` executa contra o SQL Server. Se a definição do conjunto de dados não tiver "estrutura", todas as colunas são selecionadas da tabela.

**Exemplo: Utilize o SQL query**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemplo: Usar um procedimento armazenado**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**A definição do procedimento armazenado**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server como um sink

> [!TIP]
> Saiba mais sobre os comportamentos de escrita suportados, configurações e melhores práticas da [melhor prática para carregar dados para o SQL Server](#best-practice-for-loading-data-into-sql-server).

Para copiar dados para o SQL Server, defina o tipo de sink na atividade de cópia para **SqlSink**. As seguintes propriedades são suportadas na secção de sink de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de sink de atividade de cópia tem de ser definida **SqlSink**. | Sim |
| writeBatchSize |Número de linhas a inserir na tabela SQL *por lote*.<br/>Valores permitidos são números inteiros para o número de linhas. Por predefinição, o Azure Data Factory dinamicamente determina o tamanho do lote apropriado com base no tamanho de linha. |Não |
| writeBatchTimeout |Esta propriedade especifica o tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite.<br/>Valores permitidos são para o período de tempo. Um exemplo é "00: 30:00" durante 30 minutos. |Não |
| preCopyScript |Esta propriedade especifica uma consulta SQL para a atividade de cópia seja executada antes de escrever dados no SQL Server. Ele é invocado apenas uma vez por cópia executar. Pode utilizar esta propriedade para limpar os dados pré-carregado. |Não |
| sqlWriterStoredProcedureName |Este nome é o procedimento armazenado que define como aplicar dados de origem para a tabela de destino.<br/>Este procedimento armazenado está *invocado por lote*. Para fazer uma operação que é executado apenas uma vez e não tem nada a com dados de origem, por exemplo, delete ou truncate, use o `preCopyScript` propriedade. |Não |
| storedProcedureParameters |Esses parâmetros são utilizados para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e as letras maiúsculas e minúsculas os parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |
| sqlWriterTableType |Esta propriedade especifica um nome de tipo de tabela a ser utilizado no procedimento armazenado. A atividade de cópia torna os dados que está a ser movidos disponíveis numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados que estão a ser copiados com os dados existentes. |Não |

**Exemplo 1: Anexar dados**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemplo 2: Invocar um procedimento armazenado durante a cópia**

Saiba mais detalhes a partir [invocar um procedimento armazenado a partir de um sink SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>Prática recomendada para carregar dados para o SQL Server

Quando copiar dados no SQL Server, poderá necessitar de comportamento de escrita diferentes:

- [Acrescentar](#append-data): Meus dados de origem tem apenas de novos registos.
- [Upsert](#upsert-data): Meus dados de origem têm inserções e atualizações.
- [Substituir](#overwrite-the-entire-table): Quero recarregar a tabela de toda a dimensão de cada vez.
- [Escrever com lógica personalizada](#write-data-with-custom-logic): Preciso de um processamento extra antes da última inserção na tabela de destino.

Veja as secções correspondentes para saber como configurar no Azure Data Factory e melhores práticas.

### <a name="append-data"></a>Anexar dados

Acrescentar dados é o comportamento predefinido deste conector de sink do SQL Server. O Azure Data Factory faz uma inserção em massa para gravação eficiente à sua tabela. Pode configurar a origem e sink em conformidade na atividade de cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção 1:** Se tiver uma grande quantidade de dados para copiar, utilize a seguinte abordagem para fazer um upsert: 

- Em primeiro lugar, utilize um [tabela temporária](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) para carregamento em massa todos os registos através da atividade de cópia. Uma vez que operações em tabelas temporárias não tiver sessão iniciadas, é possível carregar milhões de registos em segundos.
- Executar uma atividade de procedimento armazenado no Azure Data Factory para aplicar uma [intercalar](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou instrução de inserção/ATUALIZAÇÃO. Utilize a tabela temporária como uma origem para executar todas as atualizações ou insere como uma única transação. Dessa forma, o número de ida e volta e operações de registo é reduzido. No fim da atividade de procedimento armazenado, a tabela temporária pode ser truncada para estar preparado para o próximo ciclo de upsert.

Por exemplo, no Azure Data Factory, pode criar um pipeline com uma **atividade de cópia** em cadeia com um **atividade Stored Procedure**. O primeiro copia dados de seu arquivo de origem numa tabela temporária da base de dados, por exemplo, **##UpsertTempTable**, como o nome da tabela no conjunto de dados. Em seguida, o último invoca um procedimento armazenado para intercalar dados de origem de tabela temporária para a tabela de destino e limpar a tabela temporária.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

A base de dados, defina um procedimento armazenado com a lógica de intercalação, semelhante ao seguinte exemplo, o que é apontada da atividade de procedimento armazenado anterior. Vamos supor que o destino é o **Marketing** tabela com três colunas: **ProfileID**, **estado**, e **categoria**. Fazer o upsert com base na **ProfileID** coluna.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Opção 2:** Também pode optar por [invocar um procedimento armazenado dentro da atividade de cópia](#invoke-a-stored-procedure-from-a-sql-sink). Essa abordagem é executado cada linha na tabela de origem em vez de utilizar a inserção em massa como a abordagem de predefinição na atividade de cópia, que não é adequada para upsert em grande escala.

### <a name="overwrite-the-entire-table"></a>Substituir a tabela inteira

Pode configurar o **preCopyScript** propriedade num sink de atividade de cópia. Neste caso, para cada atividade de cópia que é executado, Azure Data Factory é executado o script primeiro. Em seguida, ele executa a cópia para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, especifica um script para eliminar primeiro todos os registos antes de em massa carregar os novos dados a partir da origem.

### <a name="write-data-with-custom-logic"></a>Escrever dados com lógica personalizada

Os passos para escrever dados com lógica personalizada são semelhantes às descritas a [Upsert dados](#upsert-data) secção. Quando precisar de aplicar extra de processamento antes da inserção de final de origem de dados na tabela de destino, de grande escala, pode efetuar uma das duas coisas: 

- Carregar para uma tabela temporária e, em seguida, chamar um procedimento armazenado. 
- Invoca um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado a partir de um sink SQL

Quando copiar dados num banco de dados do SQL Server, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais.

> [!TIP]
> Invocar um procedimento armazenado processa os dados de linha por linha em vez de através da utilização de uma operação em massa, que não é recomendada para cópia em grande escala. Saiba mais a partir da [melhor prática para carregar dados para o SQL Server](#best-practice-for-loading-data-into-sql-server).

Pode usar um procedimento armazenado quando os mecanismos de cópia interna não atendem a finalidade. Um exemplo é quando deseja aplicar um processamento extra antes da inserção de final de origem de dados na tabela de destino. Alguns exemplos de processamento extra são quando deseja intercalar colunas, procurar valores adicionais e inserir dados em mais de uma tabela.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert numa tabela na base de dados do SQL Server. Partem do princípio de que os dados de entrada e o sink **Marketing** cada tabela têm três colunas: **ProfileID**, **estado**, e **categoria**. Fazer o upsert com base na **ProfileID** coluna e aplicá-la apenas para uma categoria específica.

**Conjunto de dados de saída:** "tableName" é o mesmo nome de parâmetro de tipo de tabela no seu procedimento armazenado, conforme mostrado no seguinte script do procedimento armazenado:

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Definir o **sink do SQL** secção a atividade de cópia da seguinte forma:

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

Na sua base de dados, definir o procedimento armazenado com o mesmo nome que **SqlWriterStoredProcedureName**. Ele lida com dados de entrada da sua origem especificado e intercala a tabela de saída. O nome do parâmetro de tipo de tabela no procedimento armazenado é o mesmo que **tableName** definido no conjunto de dados.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category);
END
```

Na sua base de dados, definir o tipo de tabela com o mesmo nome que **sqlWriterTableType**. O esquema do tipo de tabela é o mesmo que o esquema devolvido pelos seus dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

A funcionalidade de procedimento armazenado aproveita [parâmetros de valor de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Mapeamento de tipo de dados para o SQL Server

Quando copia dados de e para o SQL Server, os seguintes mapeamentos são utilizados entre tipos de dados do SQL Server para tipos de dados intermediárias do Azure Data Factory. Para saber como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink, veja [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do SQL Server | Tipo de dados intermediárias do Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Para tipos de dados que são mapeados para o tipo Decimal provisório, Azure Data Factory suporta atualmente precisão até 28. Se tiver dados que necessitam de precisão maior que 28, considere a conversão para uma cadeia de caracteres numa consulta SQL.

## <a name="troubleshoot-connection-issues"></a>Resolver problemas de ligação

1. Configure a sua instância de SQL Server para aceitar ligações remotas. Inicie **SQL Server Management Studio**, clique com botão direito **servidor de**e selecione **propriedades**. Selecione **conexões** na lista e selecione o **permitir ligações remotas a este servidor** caixa de verificação.

    ![Ativar ligações remotas](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Para obter passos detalhados, consulte [configurar a opção de configuração do servidor de acesso remoto](https://msdn.microsoft.com/library/ms191464.aspx).

2. Inicie **Gestor de configuração do SQL Server**. Expanda **configuração de rede do SQL Server** para a instância que pretende e selecione **protocolos para MSSQLSERVER**. Protocolos são apresentados no painel da direita. Ative TCP/IP clicando **TCP/IP** e selecionando **ativar**.

    ![Ative TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Para obter mais informações e alternativas formas de ativar o protocolo TCP/IP, consulte [ativar ou desativar um protocolo de rede do servidor](https://msdn.microsoft.com/library/ms191294.aspx).

3. Na janela da mesma, faça duplo clique em **TCP/IP** para iniciar o **propriedades de TCP/IP** janela.
4. Mude para o **endereços IP** separador. Desloque-se para baixo para ver os **IPAll** secção. Anote o **porta TCP**. A predefinição é **1433**.
5. Criar uma **regra da Firewall do Windows** na máquina para permitir o tráfego de entrada por essa porta. 
6. **Verificar ligação**: Para ligar ao SQL Server com um nome totalmente qualificado, utilize o SQL Server Management Studio noutra máquina. Um exemplo é `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
