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
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: 782027f19d4e82f26fc1265f25b86223386d7182
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903390"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Copiar dados de e para instância gerida da base de dados SQL do Azure com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para instância gerida da base de dados SQL do Azure. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de instância gerida da base de dados SQL do Azure para qualquer arquivo de dados de sink suportados. Também pode copiar dados de qualquer arquivo de dados de origem suportada para a instância gerida. Para obter uma lista dos arquivos de dados que são suportados como origens e sinks, a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de instância gerida da base de dados SQL do Azure suporta:

- Copiar dados utilizando a autenticação de SQL ou o Windows.
- Como uma origem, obter dados utilizando uma consulta SQL ou o procedimento armazenado.
- Como um sink, acrescentando dados a uma tabela de destino ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) não é suportado agora. 

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar dados de cópia de um Azure SQL Database Managed Instance que esteja localizado numa rede virtual, configure um runtime de integração autoalojado que pode acessar o banco de dados. Para obter mais informações, consulte [integration runtime autoalojado](create-self-hosted-integration-runtime.md).

Se aprovisionar o runtime de integração autoalojado na mesma rede virtual que sua instância gerida, certifique-se de que sua máquina de runtime de integração não está numa sub-rede diferente, a sua instância gerida. Se aprovisionar o runtime de integração autoalojado numa rede virtual diferente do que a sua instância gerida, pode utilizar um peering de rede virtual ou uma rede virtual para a ligação de rede virtual. Para obter mais informações, consulte [ligar a sua aplicação para a instância gerida da base de dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector de instância gerida da base de dados SQL do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço do Azure SQL Database Managed Instance ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo deve ser definida como **SqlServer**. | Sim. |
| connectionString |Esta propriedade especifica as informações de connectionString, que é necessário para ligar à instância gerida utilizando a autenticação SQL ou autenticação do Windows. Para obter mais informações, consulte os exemplos seguintes. <br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também pode colocar a palavra-passe no Azure Key Vault, e se se trata de solicitação de autenticação de SQL a `password` configuração fora de cadeia de ligação. Veja o exemplo JSON abaixo da tabela e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. |Sim. |
| userName |Esta propriedade especifica um nome de utilizador, se utilizar a autenticação do Windows. Um exemplo é **domainname\\nome de utilizador**. |Não. |
| palavra-passe |Esta propriedade especifica uma palavra-passe da conta de utilizador que especificou para o nome de utilizador. Selecione **SecureString** armazenar as informações de connectionString em segurança na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Não. |
| connectVia | Isso [runtime de integração](concepts-integration-runtime.md) é utilizada para ligar ao arquivo de dados. Aprovisione o runtime de integração autoalojado na mesma rede virtual que sua instância gerida. |Sim. |

>[!TIP]
>Poderá ver o código de erro, como "UserErrorFailedToConnectToSqlServer" com uma mensagem "o limite de sessão para a base de dados é XXX e foi atingido." Se este erro ocorrer, adicionar `Pooling=false` para sua cadeia de ligação e tente novamente.

**Exemplo 1: Utilizar a autenticação SQL**

```json
{
    "name": "AzureSqlMILinkedService",
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

**Exemplo 2: Utilizar a autenticação de SQL com a palavra-passe no Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
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
    "name": "AzureSqlMILinkedService",
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

Para obter uma lista completa de seções e propriedades disponíveis para serem utilizados definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados de instância gerida da base de dados SQL do Azure.

Para copiar dados de e para instância gerida da base de dados SQL do Azure, defina a propriedade de tipo de conjunto de dados para **SqlServerTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida **SqlServerTable**. | Sim. |
| tableName |Esta propriedade é o nome da tabela ou vista na instância da base de dados que o serviço ligado refere-se a. | Não para a origem. Sim para o sink. |

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
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida **SqlSource**. | Sim. |
| sqlReaderQuery |Esta propriedade usa a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. |Não. |
| sqlReaderStoredProcedureName |Esta propriedade é o nome do procedimento armazenado que lê dados da tabela de origem. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não. |
| storedProcedureParameters |Esses parâmetros são para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e as letras maiúsculas e minúsculas os parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não. |

Tenha em atenção os seguintes pontos:

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

Para copiar dados para a instância gerida da base de dados SQL do Azure, defina o tipo de sink na atividade de cópia para **SqlSink**. As seguintes propriedades são suportadas na secção de sink de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de sink de atividade de cópia tem de ser definida **SqlSink**. | Sim. |
| writeBatchSize |Esta propriedade insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize.<br/>Valores permitidos são números inteiros para o número de linhas. |Não (predefinição: 10,000). |
| writeBatchTimeout |Esta propriedade especifica o tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite.<br/>Valores permitidos são para o intervalo de tempo. Um exemplo é "00: 30:00," que é 30 minutos. |Não. |
| preCopyScript |Esta propriedade especifica uma consulta SQL para a atividade de cópia executar antes de escrever dados para a instância gerida. Ele é invocado apenas uma vez por cópia executar. Pode utilizar esta propriedade para limpar os dados pré-carregado. |Não. |
| sqlWriterStoredProcedureName |Este nome é o procedimento armazenado que define como aplicar dados de origem para a tabela de destino. São exemplos de procedimentos para fazer a upserts ou transformações com sua própria lógica de negócios. <br/><br/>Este procedimento armazenado está *invocado por lote*. Para fazer uma operação que é executado apenas uma vez e não tem nada a com dados de origem, por exemplo, delete ou truncate, use o `preCopyScript` propriedade. |Não. |
| storedProcedureParameters |Esses parâmetros são utilizados para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e as letras maiúsculas e minúsculas os parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não. |
| sqlWriterTableType |Esta propriedade especifica um nome de tipo de tabela a ser utilizado no procedimento armazenado. A atividade de cópia torna os dados que está a ser movidos disponíveis numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados que estão a ser copiados com os dados existentes. |Não. |

> [!TIP]
> Quando os dados são copiados para a instância gerida da base de dados SQL do Azure, a atividade de cópia acrescenta dados para a tabela do sink por predefinição. Para efetuar uma upsert ou lógica de negócios adicionais, utilize o procedimento armazenado no SqlSink. Para obter mais informações, consulte [invocar um procedimento armazenado a partir de um sink SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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

**Exemplo 2: Invocar um procedimento armazenado durante a cópia para upsert**

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

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade na base de dados de destino

O exemplo seguinte copia dados de uma tabela de origem com não existe nenhuma coluna de identidade para uma tabela de destino com uma coluna de identidade.

**Tabela de origem**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```

**Tabela de destino**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Tenha em atenção que a tabela de destino tem uma coluna de identidade.

**Definição de JSON do conjunto de dados de origem**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Definição de JSON do conjunto de dados de destino**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Observe que a sua tabela de origem e destino têm esquema diferente. A tabela de destino tem uma coluna de identidade. Neste cenário, especifique a propriedade de "estrutura" na definição de conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado a partir de um sink SQL

Quando os dados são copiados para a instância gerida da base de dados SQL do Azure, um procedimento armazenado pode ser configurado e invocado com parâmetros adicionais que especificar.

Pode usar um procedimento armazenado quando os mecanismos de cópia interna não atendem a finalidade. É normalmente utilizado quando um upsert (atualização + insert) ou um processamento extra deve ser feito antes da última inserção de dados de origem na tabela de destino. Processamento extra pode incluir tarefas como a intercalação de colunas, procurar valores adicionais e a inserção em várias tabelas.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert numa tabela na instância gerida. O exemplo parte do princípio de que os dados de entrada e a tabela de "Marketing" do sink tem três colunas: ProfileID, estado e categoria. Execute o upsert com base na coluna ProfileID e inscreva-se apenas uma categoria específica.

**Conjunto de dados de saída**

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

Defina a seção SqlSink numa atividade de cópia da seguinte forma:

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

Na sua base de dados, defina o procedimento armazenado com o mesmo nome como SqlWriterStoredProcedureName. Processa dados de entrada da sua origem especificado e intercala-lo a tabela de saída. O nome do parâmetro de tipo de tabela no procedimento armazenado é igual a "tableName", que é definida no conjunto de dados.

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

Na sua base de dados, defina o tipo de tabela com o mesmo nome como sqlWriterTableType. O esquema do tipo de tabela é o mesmo que o esquema devolvido pelos seus dados de entrada.

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
| binário |Byte[] |
| bit |Booleano |
| char |Cadeia de caracteres, Char [] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| decimal |decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Flutuante |Valor de duplo |
| image |Byte[] |
| int |Int32 |
| dinheiro |decimal |
| nchar |Cadeia de caracteres, Char [] |
| ntext |Cadeia de caracteres, Char [] |
| numérico |decimal |
| nvarchar |Cadeia de caracteres, Char [] |
| real |Único |
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |decimal |
| sql_variant |Object |
| texto |Cadeia de caracteres, Char [] |
| hora |Período de tempo |
| carimbo de data/hora |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |Cadeia de caracteres, Char [] |
| xml |Xml |

>[!NOTE]
> Para tipos de dados que são mapeados para o tipo Decimal provisório, Azure Data Factory suporta atualmente precisão até 28. Se tiver dados que necessitam de precisão maior que 28, considere a conversão para uma cadeia de caracteres numa consulta SQL.

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
