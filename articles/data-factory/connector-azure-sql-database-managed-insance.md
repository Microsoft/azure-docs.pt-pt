---
title: Copiar dados de e para instância gerida da base de dados SQL do Azure com o Azure Data Factory | Documentos da Microsoft
description: Aprenda a mover dados de e para instância gerida da base de dados SQL do Azure com o Azure Data Factory.
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
ms.openlocfilehash: 3e1978c761c365125ac94a1ecbef5f9ac7375eba
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338606"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Copiar dados de e para instância gerida da base de dados SQL do Azure com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para instância gerida da base de dados SQL do Azure. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de instância gerida da base de dados SQL do Azure para qualquer arquivo de dados de sink suportados. Também pode copiar dados de qualquer arquivo de dados de origem suportada para a instância gerida. Para obter uma lista dos arquivos de dados que são suportados como origens e sinks, a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de instância gerida da base de dados SQL do Azure suporta:

- Copiar dados utilizando a autenticação de SQL.
- Como uma origem, obter dados utilizando uma consulta SQL ou um procedimento armazenado.
- Como um sink, acrescentando dados a uma tabela de destino ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

>[!NOTE]
>O Azure SQL Database Managed Instance [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) não é suportada por este conector agora. Para contornar, pode utilizar um [conector do ODBC genérico](connector-odbc.md) e um driver ODBC do SQL Server por meio de um runtime de integração autoalojado. Siga [esta orientação](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) com ODBC download e a ligação de cadeia de caracteres as configurações de driver.

>[!NOTE]
>Autenticações de identidade gerida e principal de serviço não são atualmente suportadas por este conector. Para contornar, escolha um conector da base de dados do Azure SQL e especificar manualmente o servidor da sua instância gerida.

## <a name="prerequisites"></a>Pré-requisitos

Para acessar o Azure SQL Database Managed Instance [ponto final público](../sql-database/sql-database-managed-instance-public-endpoint-securely.md), pode usar um runtime de integração do Azure geridos do Azure Data Factory. Certifique-se de que habilite o ponto final público e também permite o tráfego de ponto final público no grupo de segurança de rede para que o Azure Data Factory consegue ligar à base de dados. Para obter mais informações, consulte [esta orientação](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Para acessar o ponto final privado de instância gerida da base de dados SQL do Azure, configure uma [integration runtime autoalojado](create-self-hosted-integration-runtime.md) que pode aceder à base de dados. Se aprovisionar o runtime de integração autoalojado na mesma rede virtual que sua instância gerida, certifique-se de que sua máquina de runtime de integração não está numa sub-rede diferente, a sua instância gerida. Se aprovisionar o runtime de integração autoalojado numa rede virtual diferente do que a sua instância gerida, pode utilizar um peering de rede virtual ou uma rede virtual para a ligação de rede virtual. Para obter mais informações, consulte [ligar a sua aplicação para a instância gerida da base de dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Azure Data Factory específicas para o conector de instância gerida da base de dados SQL do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço do Azure SQL Database Managed Instance ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **SqlServer**. | Sim |
| connectionString |Esta propriedade especifica o **connectionString** informações necessárias para ligar à instância gerida utilizando a autenticação de SQL. Para obter mais informações, consulte os exemplos seguintes. <br/>Marcar esse campo como **SecureString** armazena de forma segura no Azure Data Factory. Também pode colocar uma palavra-passe no Azure Key Vault. Se é autenticação de SQL, extrair o `password` configuração fora de cadeia de ligação. Para obter mais informações, consulte o exemplo JSON a tabela a seguir e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | Isso [runtime de integração](concepts-integration-runtime.md) é utilizada para ligar ao arquivo de dados. Pode usar um runtime de integração autoalojado ou um runtime de integração do Azure, se a sua instância gerida tem um ponto final público e permite que o Azure Data Factory para aceder ao mesmo. Se não for especificado, é utilizado o runtime de integração do Azure padrão. |Sim |

**Exemplo 1: Utilizar a autenticação SQL**

A porta predefinida é 1433. Se estiver a utilizar instância gerida da base de dados SQL do Azure com um ponto final público, especifica explicitamente porta 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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

A porta predefinida é 1433. Se estiver a utilizar instância gerida da base de dados SQL do Azure com um ponto final público, especifica explicitamente porta 3342.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para serem utilizados definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados de instância gerida da base de dados SQL do Azure.

Para copiar dados de e para instância gerida da base de dados SQL do Azure, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados tem de ser definida **SqlServerTable**. | Sim |
| tableName |Esta propriedade é o nome da tabela ou vista na instância da base de dados que o serviço ligado refere-se a. | Não para a origem, Sim para o sink |

**Exemplo**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

Para obter uma lista completa de seções e propriedades disponíveis para serem utilizados definir as atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com a origem de instância gerida da base de dados SQL do Azure e de sink.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>O Azure SQL Database Managed Instance como uma origem

Para copiar dados de instância gerida da base de dados SQL do Azure, definir o tipo de origem na atividade de cópia para **SqlSource**. As seguintes propriedades são suportadas na secção de origem de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida **SqlSource**. | Sim |
| sqlReaderQuery |Esta propriedade usa a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Esta propriedade é o nome do procedimento armazenado que lê dados da tabela de origem. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Esses parâmetros são para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e as letras maiúsculas e minúsculas os parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |

**Tenha em atenção os seguintes pontos:**

- Se **sqlReaderQuery** especificado para **SqlSource**, a atividade de cópia executa esta consulta em relação à origem de instância gerida para obter os dados. Também pode especificar um procedimento armazenado, especificando **sqlReaderStoredProcedureName** e **storedProcedureParameters** se o procedimento armazenado utiliza parâmetros.
- Se não especificar qualquer um de **sqlReaderQuery** ou **sqlReaderStoredProcedureName** propriedade, as colunas definidas na secção "estrutura" do conjunto de dados JSON são utilizados para construir uma consulta. A consulta `select column1, column2 from mytable` for executado relativamente a instância gerida. Se a definição do conjunto de dados não tiver "estrutura", todas as colunas são selecionadas da tabela.

**Exemplo: Utilizar uma consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>O Azure SQL Database Managed Instance como sink

> [!TIP]
> Saiba mais sobre os comportamentos de escrita suportados, configurações e melhores práticas da [melhor prática para carregar dados para a instância gerida da base de dados SQL do Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Para copiar dados para a instância gerida da base de dados SQL do Azure, defina o tipo de sink na atividade de cópia para **SqlSink**. As seguintes propriedades são suportadas na secção de sink de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de sink de atividade de cópia tem de ser definida **SqlSink**. | Sim |
| writeBatchSize |Número de linhas a inserir na tabela SQL *por lote*.<br/>Valores permitidos são números inteiros para o número de linhas. Por predefinição, o Azure Data Factory dinamicamente determina o tamanho do lote apropriado com base no tamanho de linha.  |Não |
| writeBatchTimeout |Esta propriedade especifica o tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite.<br/>Valores permitidos são para o período de tempo. Um exemplo é "00: 30:00," que é 30 minutos. |Não |
| preCopyScript |Esta propriedade especifica uma consulta SQL para a atividade de cópia seja executada antes de escrever dados para a instância gerida. Ele é invocado apenas uma vez por cópia executar. Pode utilizar esta propriedade para limpar os dados pré-carregado. |Não |
| sqlWriterStoredProcedureName |Este nome é o procedimento armazenado que define como aplicar dados de origem para a tabela de destino. <br/>Este procedimento armazenado está *invocado por lote*. Para fazer uma operação que é executado apenas uma vez e não tem nada a com dados de origem, por exemplo, delete ou truncate, use o `preCopyScript` propriedade. |Não |
| storedProcedureParameters |Esses parâmetros são utilizados para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e as letras maiúsculas e minúsculas os parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |
| sqlWriterTableType |Esta propriedade especifica um nome de tipo de tabela a ser utilizado no procedimento armazenado. A atividade de cópia torna os dados que está a ser movidos disponíveis numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados que estão a ser copiados com os dados existentes. |Não |

**Exemplo 1: Anexar dados**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Prática recomendada para carregar dados para a instância gerida da base de dados SQL do Azure

Quando copia dados para a instância gerida da base de dados SQL do Azure, poderá necessitar de comportamento de escrita diferentes:

- [Acrescentar](#append-data): Meus dados de origem tem apenas de novos registos.
- [Upsert](#upsert-data): Meus dados de origem têm inserções e atualizações.
- [Substituir](#overwrite-the-entire-table): Quero recarregar a tabela de toda a dimensão de cada vez.
- [Escrever com lógica personalizada](#write-data-with-custom-logic): Preciso de um processamento extra antes da última inserção na tabela de destino. 

Veja as secções correspondentes para saber como configurar no Azure Data Factory e melhores práticas.

### <a name="append-data"></a>Anexar dados

Acrescentar dados é o comportamento predefinido deste conector de sink de instância gerida da base de dados SQL do Azure. O Azure Data Factory faz uma inserção em massa para gravação eficiente à sua tabela. Pode configurar a origem e sink em conformidade na atividade de cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção 1:** Se tiver uma grande quantidade de dados para copiar, utilize a seguinte abordagem para fazer um upsert: 

- Em primeiro lugar, utilize um [tabela temporária](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) para carregamento em massa todos os registos através da atividade de cópia. Uma vez que operações em tabelas temporárias não tiver sessão iniciadas, é possível carregar milhões de registos em segundos.
- Executar uma atividade de procedimento armazenado no Azure Data Factory para aplicar uma [intercalar](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou instrução de inserção/ATUALIZAÇÃO. Utilize a tabela temporária, como a origem de executar todas as atualizações ou insere como uma única transação. Dessa forma, o número de ida e volta e operações de registo é reduzido. No fim da atividade de procedimento armazenado, a tabela temporária pode ser truncada para estar preparado para o próximo ciclo de upsert.

Por exemplo, no Azure Data Factory, pode criar um pipeline com uma **atividade de cópia** em cadeia com um **atividade Stored Procedure**. O primeiro copia dados de seu arquivo de origem numa tabela temporária, por exemplo, **##UpsertTempTable**, como o nome da tabela no conjunto de dados. Em seguida, o último invoca um procedimento armazenado para intercalar dados de origem de tabela temporária para a tabela de destino e limpar a tabela temporária.

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

**Opção 2:** Também pode optar por [invocar um procedimento armazenado dentro de uma atividade de cópia](#invoke-a-stored-procedure-from-a-sql-sink). Essa abordagem é executado cada linha na tabela de origem em vez de utilizar a inserção em massa como a abordagem de predefinição na atividade de cópia, que não é adequada para upsert em grande escala.

### <a name="overwrite-the-entire-table"></a>Substituir a tabela inteira

Pode configurar o **preCopyScript** propriedade num sink de atividade de cópia. Neste caso, para cada atividade de cópia que é executado, Azure Data Factory é executado o script primeiro. Em seguida, ele executa a cópia para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, especifica um script para eliminar primeiro todos os registos antes de em massa carregar os novos dados a partir da origem.

### <a name="write-data-with-custom-logic"></a>Escrever dados com lógica personalizada

Os passos para escrever dados com lógica personalizada são semelhantes às descritas a [Upsert dados](#upsert-data) secção. Quando precisar de aplicar extra de processamento antes da inserção de final de origem de dados na tabela de destino, de grande escala, pode efetuar uma das duas coisas: 

- Carregar para uma tabela temporária e, em seguida, chamar um procedimento armazenado.
- Invoca um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado a partir de um sink SQL

Quando copia dados para a instância gerida da base de dados SQL do Azure, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais.

> [!TIP]
> Invocar um procedimento armazenado processa os dados de linha por linha em vez de através da utilização de uma operação em massa, que não é recomendada para cópia em grande escala. Saiba mais a partir da [melhor prática para carregar dados para a instância gerida da base de dados SQL do Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Pode usar um procedimento armazenado quando os mecanismos de cópia interna não atendem a finalidade. Um exemplo é quando deseja aplicar um processamento extra antes da inserção de final de origem de dados na tabela de destino. Alguns exemplos de processamento extra são quando deseja intercalar colunas, procurar valores adicionais e inserir dados em mais de uma tabela.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert numa tabela na base de dados do SQL Server. Partem do princípio de que os dados de entrada e o sink **Marketing** cada tabela têm três colunas: **ProfileID**, **estado**, e **categoria**. Fazer o upsert com base na **ProfileID** coluna e aplicá-la apenas para uma categoria específica.

**Conjunto de dados de saída:** "tableName" é o mesmo nome de parâmetro de tipo de tabela no seu procedimento armazenado, conforme mostrado no seguinte script do procedimento armazenado:

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapeamento de tipo de dados para a instância gerida da base de dados SQL do Azure

Quando os dados são copiados de e para instância gerida da base de dados SQL do Azure, os seguintes mapeamentos são utilizados entre tipos de dados de instância gerida da base de dados SQL do Azure para tipos de dados intermediárias do Azure Data Factory. Para saber como a atividade de cópia é mapeada do tipo de esquema e os dados de origem para o sink, veja [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do Azure SQL Database Managed Instance | Tipo de dados intermediárias do Azure Data Factory |
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

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
