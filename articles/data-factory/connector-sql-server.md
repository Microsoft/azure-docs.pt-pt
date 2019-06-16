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
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: 230fe94820a00c276238a7f5ff189ecc817f3f96
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074049"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Copiar dados para e do SQL Server com o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
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
| type | A propriedade de tipo tem de ser definida como: **SqlServer** | Sim |
| connectionString |Especifique as informações de connectionString necessárias para ligar à base de dados do SQL Server com a autenticação SQL ou autenticação do Windows. Consulte os exemplos seguintes.<br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também pode colocar a palavra-passe no Azure Key Vault, e se se trata de solicitação de autenticação de SQL a `password` configuração fora de cadeia de ligação. Veja o exemplo JSON abaixo da tabela e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. |Sim |
| userName |Especifique o nome de utilizador se estiver a utilizar autenticação do Windows. Exemplo: **domainname\\nome de utilizador**. |Não |
| password |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Não |
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

Para copiar dados de/para o banco de dados do SQL Server, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **SqlServerTable** | Sim |
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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
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
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **SqlSource** | Sim |
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

> [!TIP]
> Saiba mais sobre os comportamentos de escrita suportados, configurações e melhor prática de [melhor prática para carregar dados para o SQL Server](#best-practice-for-loading-data-into-sql-server).

Para copiar dados para o SQL Server, defina o tipo de sink na atividade de cópia para **SqlSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de sink de atividade de cópia tem de ser definida: **SqlSink** | Sim |
| writeBatchSize |Número de linhas para inserções na tabela de SQL **por lote**.<br/>Valores permitidos são: número inteiro (número de linhas). Por predefinição, o Data Factory determinar dinamicamente o tamanho do lote apropriado com base no tamanho de linha. |Não |
| writeBatchTimeout |Tempo para a operação de inserção de lote seja concluída antes de atingir o tempo limite de espera.<br/>Valores permitidos são: intervalo de tempo. Exemplo: "00: 30:00" (30 minutos). |Não |
| preCopyScript |Especifica uma consulta SQL para a atividade de cópia executar antes de escrever dados no SQL Server. Apenas ser invocado uma vez por cópia executar. Pode utilizar esta propriedade para limpar os dados carregados previamente. |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que define como aplicar a origem de dados na tabela de destino.<br/>Tenha em atenção de que este procedimento armazenado será **invocado por lote**. Se pretender efetuar a operação que só é executado uma vez e não tem nada a fazer com os dados de origem, por exemplo, eliminar/truncar, utilize `preCopyScript` propriedade. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado.<br/>Valores permitidos são: pares nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |
| sqlWriterTableType |Especifique um nome de tipo de tabela a ser utilizado no procedimento armazenado. Atividade de cópia torna os dados que está a ser movidos disponíveis numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados a ser copiados com os dados existentes. |Não |

**Exemplo 1: anexar dados**

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

**Exemplo 2: invocar um procedimento armazenado durante a cópia**

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

## <a name="best-practice-for-loading-data-into-sql-server"></a>Prática recomendada para carregar dados para o SQL Server

Quando copiar dados no SQL Server, pode necessitar de comportamento de escrita diferentes:

- **[Acrescentar](#append-data)** : meus dados de origem tem apenas novos registros;
- **[Upsert](#upsert-data)** : meus dados de origem têm inserções e atualizações,
- **[Substituir](#overwrite-entire-table)** : Quero recarregar a tabela de toda a dimensão sempre;
- **[Escrever com lógica personalizada](#write-data-with-custom-logic)** : Preciso de um processamento extra antes da última inserção na tabela de destino.

Consulte as secções respectivamente sobre como configurar no ADF e as práticas recomendadas.

### <a name="append-data"></a>Anexar dados

Este é o comportamento predefinido deste conector de sink do SQL Server e fazer do ADF **inserção em massa** para gravação eficiente à sua tabela. Além disso, pode simplesmente configurar a origem e sink em conformidade na atividade de cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção eu** (sugeridas, especialmente quando tem dados de grande dimensão para copiar): a **abordagem de alto desempenho a maioria dos** fazer upsert é o seguinte: 

- Em primeiro lugar, tirar partido de uma [tabela temporária](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) para carregamento em massa com a atividade de cópia de todos os registos. Que as operações em tabelas temporárias não são registadas, pode carregar milhões de registos em segundos.
- Executar uma atividade de procedimento armazenado no ADF para aplicar uma [intercalar](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (ou de inserção/ATUALIZAÇÃO) declaração e uso o temp tabela como origem para executar todas as atualizações ou insere como uma única transação, reduzindo a quantidade de idas e voltas e operações de registo. No fim da atividade de procedimento armazenado, possível truncar a tabela temporária para estar preparado para o próximo ciclo de upsert. 

Por exemplo, no Azure Data Factory, pode criar um pipeline com uma **atividade de cópia** em cadeia com um **atividade Stored Procedure** com êxito. O primeiro copia dados de seu arquivo de origem para uma tabela temporária da base de dados, digamos " **##UpsertTempTable**" como nome de tabela no conjunto de dados, em seguida, o último invoca um procedimento armazenado para intercalar dados de origem de tabela temporária na tabela de destino, e limpar a tabela temporária.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

A base de dados, defina um procedimento armazenado com a lógica de intercalação, semelhante ao seguinte, que é apontada da atividade Stored Procedure acima. Partindo do princípio de destino **Marketing** tabela com três colunas: **ProfileID**, **estado**, e **categoria**, e fazer o upsert com base no **ProfileID** coluna.

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

**Opção II:** em alternativa, pode optar por [invocar um procedimento armazenado numa atividade de cópia](#invoking-stored-procedure-for-sql-sink), ao mesmo tempo nota essa abordagem é executada para cada linha na tabela de origem em vez de tirar partido em massa Inserir como a abordagem de predefinição na atividade de cópia, portanto, não se encaixa para upsert de grande escala.

### <a name="overwrite-entire-table"></a>Substituir tabela inteira

Pode configurar **preCopyScript** sink de propriedade na atividade de cópia, caso em que para cada atividade de cópia executar, ADF executa o script em primeiro lugar, em seguida, execute a cópia para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, pode especificar um script para eliminar primeiro todos os registos antes dos novos dados da origem de carregamento em massa.

### <a name="write-data-with-custom-logic"></a>Escrever dados com lógica personalizada

Semelhante, conforme descrito em [Upsert dados](#upsert-data) secção, quando precisar de aplicar um processamento extra antes da inserção de final de origem de dados na tabela de destino, pode uma) para um dimensionamento de grande escala, carregar para uma tabela temporária, em seguida, invocar um armazenado procedimento ou b) invocar um procedimento armazenado durante a cópia.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Invocar um procedimento armazenado do sink do SQL

Quando se copiam dados na base de dados do SQL Server, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais.

> [!TIP]
> Invocação de procedimento armazenado processa a dados linha por linha em vez de operação em massa, que não é sugerida para cópia de grande escala. Saiba mais a partir da [melhor prática para carregar dados para o SQL Server](#best-practice-for-loading-data-into-sql-server).

Pode usar um procedimento armazenado quando os mecanismos de cópia interna não atendem a finalidade, por exemplo, aplicar um processamento extra antes da inserção de final de origem de dados na tabela de destino. Alguns exemplos de processamento extra são colunas de intercalação, procura de valores adicionais e a inserção em mais de uma tabela.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert numa tabela na base de dados do SQL Server. Partem do princípio de que dados de entrada e o sink **Marketing** cada tabela têm três colunas: **ProfileID**, **estado**, e **categoria**. Fazer o upsert com base na **ProfileID** coluna e aplicá-la apenas para uma categoria específica.

**Conjunto de dados de saída:** "tableName" deve ser o mesmo nome de parâmetro de tipo de tabela no seu procedimento armazenado (veja abaixo o script do procedimento armazenado).

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

Definir o **sink do SQL** secção na atividade de cópia da seguinte forma.

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

Na sua base de dados, definir o procedimento armazenado com o mesmo nome que o **SqlWriterStoredProcedureName**. Ele lida com dados de entrada da sua origem especificado e intercala a tabela de saída. O nome do parâmetro de tipo de tabela no procedimento armazenado deve ser igual a **tableName** definido no conjunto de dados.

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
