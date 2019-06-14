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
ms.openlocfilehash: e68b522d5a0fe7c359d83fc436aa7a1fd2159198
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048586"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Copiar dados de e para instância gerida da base de dados SQL do Azure com o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de e para instância gerida da base de dados SQL do Azure. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de instância gerida da base de dados SQL do Azure para qualquer arquivo de dados de sink suportados. Também pode copiar dados de qualquer arquivo de dados de origem suportada para a instância gerida. Para obter uma lista dos arquivos de dados que são suportados como origens e sinks, a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de instância gerida da base de dados SQL do Azure suporta:

- Copiar dados utilizando a autenticação de SQL.
- Como uma origem, obter dados utilizando uma consulta SQL ou o procedimento armazenado.
- Como um sink, acrescentando dados a uma tabela de destino ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) não é suportado agora. 

## <a name="prerequisites"></a>Pré-requisitos

Para acessar a instância gerida da base de dados SQL do Azure  **[ponto final público](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)** , pode utilizar o Azure de ADF gerido IR. Certifique-se de que não apenas ativar o ponto final público, mas também permite o tráfego de ponto final público no grupo de segurança de rede para tornar o ADF conseguir estabelecer ligação à base de dados, ao seguir [esta orientação](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Para acessar a instância gerida da base de dados SQL do Azure **ponto final privado**, configure a um [integration runtime autoalojado](create-self-hosted-integration-runtime.md) que pode aceder à base de dados. Se aprovisionar o runtime de integração autoalojado na mesma rede virtual que sua instância gerida, certifique-se de que sua máquina de runtime de integração não está numa sub-rede diferente, a sua instância gerida. Se aprovisionar o runtime de integração autoalojado numa rede virtual diferente do que a sua instância gerida, pode utilizar um peering de rede virtual ou uma rede virtual para a ligação de rede virtual. Para obter mais informações, consulte [ligar a sua aplicação para a instância gerida da base de dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector de instância gerida da base de dados SQL do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço do Azure SQL Database Managed Instance ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **SqlServer**. | Sim. |
| connectionString |Esta propriedade especifica as informações de connectionString, que é necessário para ligar à instância gerida utilizando a autenticação de SQL. Para obter mais informações, consulte os exemplos seguintes. <br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também pode colocar a palavra-passe no Azure Key Vault, e se se trata de solicitação de autenticação de SQL a `password` configuração fora de cadeia de ligação. Veja o exemplo JSON abaixo da tabela e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. |Sim. |
| connectVia | Isso [runtime de integração](concepts-integration-runtime.md) é utilizada para ligar ao arquivo de dados. Pode utilizar o Runtime de integração autoalojado ou Runtime de integração do Azure (se a sua instância gerida tem o ponto final público e permitir que o ADF aceder a). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Sim. |

**Exemplo 1: Utilizar a autenticação SQL**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername:port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para serem utilizados definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados de instância gerida da base de dados SQL do Azure.

Para copiar dados de e para instância gerida da base de dados SQL do Azure, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados tem de ser definida **SqlServerTable**. | Sim. |
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
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida **SqlSource**. | Sim. |
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

> [!TIP]
> Saiba mais sobre os comportamentos de escrita suportados, configurações e melhor prática de [melhor prática para carregar dados para a instância gerida da base de dados SQL do Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Para copiar dados para a instância gerida da base de dados SQL do Azure, defina o tipo de sink na atividade de cópia para **SqlSink**. As seguintes propriedades são suportadas na secção de sink de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de sink de atividade de cópia tem de ser definida **SqlSink**. | Sim. |
| writeBatchSize |Número de linhas para inserções na tabela de SQL **por lote**.<br/>Valores permitidos são números inteiros para o número de linhas. Por predefinição, o Data Factory determinar dinamicamente o tamanho do lote apropriado com base no tamanho de linha.  |Não |
| writeBatchTimeout |Esta propriedade especifica o tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite.<br/>Valores permitidos são para o intervalo de tempo. Um exemplo é "00: 30:00," que é 30 minutos. |Não. |
| preCopyScript |Esta propriedade especifica uma consulta SQL para a atividade de cópia executar antes de escrever dados para a instância gerida. Ele é invocado apenas uma vez por cópia executar. Pode utilizar esta propriedade para limpar os dados pré-carregado. |Não. |
| sqlWriterStoredProcedureName |Este nome é o procedimento armazenado que define como aplicar dados de origem para a tabela de destino. <br/>Este procedimento armazenado está *invocado por lote*. Para fazer uma operação que é executado apenas uma vez e não tem nada a com dados de origem, por exemplo, delete ou truncate, use o `preCopyScript` propriedade. |Não. |
| storedProcedureParameters |Esses parâmetros são utilizados para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e as letras maiúsculas e minúsculas os parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não. |
| sqlWriterTableType |Esta propriedade especifica um nome de tipo de tabela a ser utilizado no procedimento armazenado. A atividade de cópia torna os dados que está a ser movidos disponíveis numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados que estão a ser copiados com os dados existentes. |Não. |

**Exemplo 1: anexar dados**

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

**Exemplo 2: invocar um procedimento armazenado durante a cópia**

Saiba mais detalhes a partir [invocar um procedimento armazenado a partir de um sink SQL](#invoking-stored-procedure-for-sql-sink).

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

Quando copia dados para a instância gerida da base de dados SQL do Azure, pode necessitar de comportamento de escrita diferentes:

- **[Acrescentar](#append-data)** : meus dados de origem tem apenas novos registros;
- **[Upsert](#upsert-data)** : meus dados de origem têm inserções e atualizações,
- **[Substituir](#overwrite-entire-table)** : Quero recarregar a tabela de toda a dimensão sempre;
- **[Escrever com lógica personalizada](#write-data-with-custom-logic)** : Preciso de um processamento extra antes da última inserção na tabela de destino.

Consulte as secções respectivamente sobre como configurar no ADF e as práticas recomendadas.

### <a name="append-data"></a>Anexar dados

Este é o comportamento predefinido deste conector de sink de instância gerida da base de dados SQL do Azure e fazer do ADF **inserção em massa** para gravação eficiente à sua tabela. Além disso, pode simplesmente configurar a origem e sink em conformidade na atividade de cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção eu** (sugeridas, especialmente quando tem dados de grande dimensão para copiar): a **abordagem de alto desempenho a maioria dos** fazer upsert é o seguinte: 

- Em primeiro lugar, tirar partido de uma [tabela temporária](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) para carregamento em massa com a atividade de cópia de todos os registos. Que as operações em tabelas temporárias não são registadas, pode carregar milhões de registos em segundos.
- Executar uma atividade de procedimento armazenado no ADF para aplicar uma [intercalar](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (ou de inserção/ATUALIZAÇÃO) declaração e uso o temp tabela como origem para executar todas as atualizações ou insere como uma única transação, reduzindo a quantidade de idas e voltas e operações de registo. No fim da atividade de procedimento armazenado, possível truncar a tabela temporária para estar preparado para o próximo ciclo de upsert. 

Por exemplo, no Azure Data Factory, pode criar um pipeline com uma **atividade de cópia** em cadeia com um **atividade Stored Procedure** com êxito. O primeiro copia dados de seu arquivo de origem para uma tabela temporária, digamos " **##UpsertTempTable**" como nome de tabela no conjunto de dados, em seguida, o último invoca um procedimento armazenado para intercalar dados de origem da tabela temporária numa tabela de destino e limpar tabela temporária.

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

## <a name="invoking-stored-procedure-for-sql-sink"></a> Invocar um procedimento armazenado a partir de um sink SQL

Quando copia dados para a instância gerida da base de dados SQL do Azure, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais.

> [!TIP]
> Invocação de procedimento armazenado processa a dados linha por linha em vez de operação em massa, que não é sugerida para cópia de grande escala. Saiba mais a partir da [melhor prática para carregar dados para a instância gerida da base de dados SQL do Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Pode usar um procedimento armazenado quando os mecanismos de cópia interna não atendem a finalidade, por exemplo, aplicar um processamento extra antes da inserção de final de origem de dados na tabela de destino. Alguns exemplos de processamento extra são colunas de intercalação, procura de valores adicionais e a inserção em mais de uma tabela.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert numa tabela na base de dados do SQL Server. Partem do princípio de que dados de entrada e o sink **Marketing** cada tabela têm três colunas: **ProfileID**, **estado**, e **categoria**. Fazer o upsert com base na **ProfileID** coluna e aplicá-la apenas para uma categoria específica.

**Conjunto de dados de saída:** "tableName" deve ser o mesmo nome de parâmetro de tipo de tabela no seu procedimento armazenado (veja abaixo o script do procedimento armazenado).

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
