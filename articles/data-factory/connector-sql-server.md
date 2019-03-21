---
title: Copiar dados de/para o SQL Server com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como mover dados de/para base de dados do SQL Server que está no local ou na VM do Azure com o Azure Data Factory.
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
ms.openlocfilehash: 78d82f7604d86b50ee5e05e5c3b5b9802a9559e5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877943"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Copiar dados para e do SQL Server com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-sqlserver-connector.md)
> * [Versão atual](connector-sql-server.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para uma base de dados do SQL Server. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de/para o banco de dados do SQL Server para qualquer arquivo de dados de sink suportados ou copiar dados de qualquer arquivo de dados de origem suportada para a base de dados do SQL Server. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector do SQL Server suporta:

- Versão de 2016, 2014, 2012, 2008 R2, 2008 e 2005 do SQL Server
- Copiar dados utilizando **SQL** ou **Windows** autenticação.
- Como origem, obter dados com a consulta SQL ou procedimento armazenado.
- Como sink, acrescentando dados a tabela de destino ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) não é suportado agora.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar dados de cópia da base de dados do SQL Server que não está acessível ao público, terá de configurar um Runtime de integração autoalojado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes. O Runtime de integração fornece um controlador de base de dados do SQL Server interno, portanto não precisa de instalar manualmente a qualquer driver quando se copiam dados de/para o banco de dados do SQL Server.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector de base de dados do SQL Server.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do SQL Server:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida como: **SqlServer** | Sim |
| connectionString |Especifique as informações de connectionString necessárias para ligar à base de dados do SQL Server com a autenticação SQL ou autenticação do Windows. Consulte os exemplos seguintes.<br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também pode colocar a palavra-passe no Azure Key Vault, e se se trata de solicitação de autenticação de SQL a `password` configuração fora de cadeia de ligação. Veja o exemplo JSON abaixo da tabela e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. |Sim |
| userName |Especifique o nome de utilizador se estiver a utilizar autenticação do Windows. Exemplo: **domainname\\nome de utilizador**. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração autoalojado ou Runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!TIP]
>Se atingir o erro com o código de erro como "UserErrorFailedToConnectToSqlServer" e a mensagem, como "o limite de sessão para a base de dados é XXX e foi atingido.", adicione `Pooling=false` para sua cadeia de ligação e tente novamente.

**Exemplo 1: utilizar a autenticação de SQL**

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

**Exemplo 2: utilizar a autenticação de SQL com a palavra-passe no Azure Key Vault**

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

**Exemplo 3: utilizar a autenticação do Windows**

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do SQL Server.

Para copiar dados de/para a base de dados do SQL Server, defina a propriedade de tipo de conjunto de dados para **SqlServerTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados deve ser definida como: **SqlServerTable** | Sim |
| tableName |Nome da tabela ou vista de instância de base de dados do SQL Server pelo serviço ligado refere-se. | Não para a origem, Sim para o sink |

**Exemplo:**

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pelo SQL Server origem e sink.

### <a name="sql-server-as-source"></a>SQL Server como origem

Para copiar dados do SQL Server, defina o tipo de origem na atividade copy na atividade **SqlSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **SqlSource** | Sim |
| sqlReaderQuery |Utilize a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado.<br/>Valores permitidos são: pares nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |

**Pontos a serem observados:**

- Se o **sqlReaderQuery** é especificado para o SqlSource, a atividade de cópia executa esta consulta em relação à origem de SQL Server para obter os dados. Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).
- Se não especificar "sqlReaderQuery" ou "sqlReaderStoredProcedureName", as colunas definidas na secção "estrutura" do conjunto de dados JSON são utilizadas para construir uma consulta (`select column1, column2 from mytable`) para executar o SQL Server. Se a definição do conjunto de dados não tiver a "estrutura", todas as colunas são selecionadas da tabela.

**Exemplo: com o SQL query**

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

**Exemplo: utilizar o procedimento armazenado**

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

**A definição do procedimento armazenado:**

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

### <a name="sql-server-as-sink"></a>SQL Server como sink

Para copiar dados para o SQL Server, defina o tipo de sink na atividade de cópia para **SqlSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de sink de atividade de cópia tem de ser definida: **SqlSink** | Sim |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize.<br/>Valores permitidos são: número inteiro (número de linhas). |Não (predefinição: 10000) |
| writeBatchTimeout |Tempo para a operação de inserção de lote seja concluída antes de atingir o tempo limite de espera.<br/>Valores permitidos são: intervalo de tempo. Exemplo: "00: 30:00" (30 minutos). |Não |
| preCopyScript |Especifica uma consulta SQL para a atividade de cópia executar antes de escrever dados no SQL Server. Apenas ser invocado uma vez por cópia executar. Pode utilizar esta propriedade para limpar os dados carregados previamente. |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar a origem de dados na tabela de destino, por exemplo, para fazer upserts ou transformação usando sua própria lógica de negócios. <br/><br/>Tenha em atenção de que este procedimento armazenado será **invocado por lote**. Se pretender efetuar a operação que só é executado uma vez e não tem nada a fazer com os dados de origem, por exemplo, eliminar/truncar, utilize `preCopyScript` propriedade. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado.<br/>Valores permitidos são: pares nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |
| sqlWriterTableType |Especifique um nome de tipo de tabela a ser utilizado no procedimento armazenado. Atividade de cópia torna os dados que está a ser movidos disponíveis numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados a ser copiados com os dados existentes. |Não |

> [!TIP]
> Quando se copiam dados para o SQL Server, a atividade de cópia acrescenta dados para a tabela do sink por predefinição. Para efetuar uma UPSERT ou lógica de negócios adicionais, utilize o procedimento armazenado no SqlSink. Saiba mais detalhes a partir [invocar o procedimento armazenado para o Sink do SQL](#invoking-stored-procedure-for-sql-sink).

**Exemplo 1: acrescentar dados**

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

**Exemplo 2: invocar um procedimento armazenado durante a cópia para upsert**

Saiba mais detalhes a partir [invocar o procedimento armazenado para o Sink do SQL](#invoking-stored-procedure-for-sql-sink).

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

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade na base de dados de destino

Esta seção fornece um exemplo que copia dados de uma tabela de origem com não existe nenhuma coluna de identidade para uma tabela de destino com uma coluna de identidade.

**Tabela de origem:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```

**Tabela de destino:**

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

Tenha em atenção que, como sua tabela de origem e destino têm diferente esquema (o destino tem uma coluna adicional com identidade). Neste cenário, tem de especificar **estrutura** propriedade na definição de conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Invocar um procedimento armazenado do sink do SQL

Quando se copiam dados na base de dados do SQL Server, um utilizador especificado procedimento armazenado poderia ser configurado e invocado com parâmetros adicionais.

Um procedimento armazenado pode ser utilizado quando os mecanismos de cópia interna não servem a finalidade. É normalmente utilizado quando upsert (insert + atualização) ou um processamento extra (mesclagem de colunas, procurar valores adicionais, a inserção em várias tabelas, etc.) precisa ser feito antes da última inserção de dados de origem na tabela de destino.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert numa tabela na base de dados do SQL Server. Partindo do princípio de dados de entrada e a tabela de "Marketing" do sink tem três colunas: ProfileID, estado e categoria. Executar com base na coluna "ProfileID" do upsert e aplicam-se apenas por uma categoria específica.

**Conjunto de dados de saída**

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

Defina a seção SqlSink na atividade de cópia da seguinte forma.

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

Na sua base de dados, defina o procedimento armazenado com o mesmo nome como SqlWriterStoredProcedureName. Ele lida com dados de entrada da sua origem especificada e intercalação para a tabela de saída. O nome do parâmetro de tipo de tabela no procedimento armazenado deve ser igual o "tableName" definido no conjunto de dados.

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

Na sua base de dados, defina o tipo de tabela com o mesmo nome como sqlWriterTableType. Tenha em atenção que o esquema do tipo de tabela deve ser a mesmo que o esquema devolvido pelos seus dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

A funcionalidade de procedimento armazenado aproveita [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Tipo de dados de mapeamento para o SQL server

Ao copiar dados de/para o SQL Server, os seguintes mapeamentos são utilizados entre tipos de dados do SQL Server para tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do SQL Server | Tipo de dados intermediárias de fábrica de dados |
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
> Para mapas de tipos de dados para o tipo Decimal de provisório, atualmente ADF suporta precisão até 28. Se tiver dados com precisão maior que 28, considere converter a cadeia de caracteres na consulta SQL.

## <a name="troubleshooting-connection-issues"></a>Resolução de problemas de ligação

1. Configure o seu SQL Server para aceitar ligações remotas. Inicie **SQL Server Management Studio**, clique com botão direito **servidor de**e clique em **propriedades**. Selecione **conexões** na lista e verificação **permitir ligações remotas para o servidor**.

    ![Ativar ligações remotas](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Ver [configurar a opção de configuração do servidor de acesso remoto](https://msdn.microsoft.com/library/ms191464.aspx) para obter passos detalhados.

2. Inicie **Gestor de configuração do SQL Server**. Expanda **configuração de rede do SQL Server** para a instância que pretende e selecione **protocolos para MSSQLSERVER**. Deverá ver protocolos no painel direito. Ative TCP/IP clicando **TCP/IP** e clicando em **ativar**.

    ![Ative TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Ver [ativar ou desativar um protocolo de rede do servidor](https://msdn.microsoft.com/library/ms191294.aspx) para obter detalhes e formas alternativas de ativação de protocolo TCP/IP.

3. Na janela da mesma, faça duplo clique em **TCP/IP** para iniciar **propriedades de TCP/IP** janela.
4. Mude para o **endereços IP** separador. Desloque-se para baixo para ver **IPAll** secção. Tome nota da **porta TCP** (a predefinição é **1433**).
5. Criar uma **regra da Firewall do Windows** na máquina para permitir o tráfego de entrada por essa porta.  
6. **Verificar ligação**: Para ligar ao servidor SQL com o nome totalmente qualificado, utilize o SQL Server Management Studio noutra máquina. Por exemplo: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
