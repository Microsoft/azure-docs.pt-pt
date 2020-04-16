---
title: Copiar dados de e para o Servidor SQL
description: Saiba como mover dados de e para a base de dados do SQL Server que está no local ou num VM Azure utilizando a Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 063ac32c98d4eb64b676247c0a16f98fa7d1702d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416690"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Copiar dados de e para o Servidor SQL utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão da Azure Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-sqlserver-connector.md)
> * [Versão atual](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia na Azure Data Factory para copiar dados de e para uma base de dados do SQL Server. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SQL Server é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)

Pode copiar dados de uma base de dados do SQL Server para qualquer loja de dados de sink suportado. Ou pode copiar dados de qualquer loja de dados de origem suportada para uma base de dados do SQL Server. Para obter uma lista de lojas de dados que sejam suportadas como fontes ou afunda-se pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SQL Server suporta:

- Versões SQL Server 2016, 2014, 2012, 2008 R2, 2008 e 2005.
- Copiar dados utilizando a autenticação SQL ou Windows.
- Como fonte, recuperar dados utilizando uma consulta SQL ou um procedimento armazenado.
- Como pia, anexando dados a uma tabela de destino ou invocando um procedimento armazenado com lógica personalizada durante a cópia.

[O SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) não é suportado.

>[!NOTE]
>O Servidor SQL [Sempre Encriptado](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) não é suportado por este conector agora. Para trabalhar, pode utilizar um [conector ODBC genérico](connector-odbc.md) e um controlador ODBC do Servidor SQL. Siga [esta orientação](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) com configurações de descarregamento e cadeia de ligação do condutor DaOBC.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do conector de base de dados Do Servidor SQL.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado ao Servidor SQL:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo deve ser definida para **SqlServer**. | Sim |
| conexãoString |Especifique as informações do String de **ligação** necessárias para se ligar à base de dados do Servidor SQL utilizando a autenticação SQL ou a autenticação do Windows. Consulte as seguintes amostras.<br/>Também pode colocar uma senha no Cofre de Chaves Azure. Se for autenticação SQL, `password` retire a configuração da cadeia de ligação. Para mais informações, consulte o exemplo jSON seguindo as credenciais da tabela e [da loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Sim |
| userName |Especifique um nome de utilizador se utilizar a autenticação do Windows. Um exemplo é o **nome de utilizador do nome\\de domínio.** |Não |
| palavra-passe |Especifique uma palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como **SecureString** para o armazenar de forma segura na Azure Data Factory. Ou pode [fazer referência a um segredo armazenado no Cofre de Chaves Azure.](store-credentials-in-key-vault.md) |Não |
| connectVia | Este tempo de execução de [integração](concepts-integration-runtime.md) é utilizado para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, o tempo de execução de integração do Azure padrão é utilizado. |Não |

>[!TIP]
>Se tiver atingido um erro com o código de erro "UserErrorFailedToConnectToSqlServer" e uma mensagem como `Pooling=false` "O limite de sessão para a base de dados é XXX e foi atingido", adicione à sua cadeia de ligação e tente novamente.

**Exemplo 1: Utilizar a autenticação SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: Utilize a autenticação SQL com uma palavra-passe no Cofre de Chaves Azure**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados do Servidor SQL.

Para copiar dados de e para uma base de dados do SQL Server, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para **SqlServerTable**. | Sim |
| schema | Nome do esquema. |Não para a fonte, sim para afundar  |
| tabela | Nome da mesa/vista. |Não para a fonte, sim para afundar  |
| tableName | Nome da mesa/vista com esquema. Esta propriedade é suportada para retrocompatibilidade. Para uma nova `schema` `table`carga de trabalho, use e . | Não para a fonte, sim para afundar |

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
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para utilização para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte e pia do Servidor SQL.

### <a name="sql-server-as-a-source"></a>SQL Server como fonte

Para copiar dados do SQL Server, delineie o tipo de origem na atividade de cópia para **SqlSource**. As seguintes propriedades são suportadas na secção de origem da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **SqlSource**. | Sim |
| sqlReaderQuery |Utilize a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Esta propriedade é o nome do procedimento armazenado que lê os dados da tabela de origem. A última declaração sQL deve ser uma declaração SELECT no procedimento armazenado. |Não |
| parâmetros de procedimento saqueados |Estes parâmetros são para o procedimento armazenado.<br/>Os valores permitidos são pares de nome ou valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |
| isolamentoN | Especifica o comportamento de bloqueio de transações para a fonte SQL. Os valores permitidos são: **ReadCommitted** (predefinido), **ReadUncommitted**, **RepeatableRead,** **Serializable**, **Snapshot**. Consulte [este doc](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para mais detalhes. | Não |

**Pontos a notar:**

- Se o **sqlReaderQuery** for especificado para **o SqlSource,** a atividade de cópia executa esta consulta contra a fonte do Servidor SQL para obter os dados. Também pode especificar um procedimento armazenado especificando **sqlReaderStoredProcedureName** e **storedProcedureParameters** se o procedimento armazenado levar parâmetros.
- Se não especificar o **sqlReaderQuery** ou **o sqlReaderStoredProcedureName,** as colunas definidas na secção "estrutura" do conjunto de dados JSON são usadas para construir uma consulta. A consulta `select column1, column2 from mytable` vai contra o Servidor SQL. Se a definição de conjunto de dados não tiver "estrutura", todas as colunas são selecionadas a partir da tabela.

**Exemplo: Utilize consulta SQL**

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

**Exemplo: Utilize um procedimento armazenado**

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

**A definição de procedimento armazenado**

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

### <a name="sql-server-as-a-sink"></a>SQL Server como um lavatório

> [!TIP]
> Saiba mais sobre os comportamentos de escrita suportados, configurações e boas práticas das [melhores práticas para carregar dados no SQL Server](#best-practice-for-loading-data-into-sql-server).

Para copiar dados para o SQL Server, delineie o tipo de pia na atividade de cópia para **SqlSink**. As seguintes propriedades são suportadas na secção de sumidouro da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do sumidouro da atividade de cópia deve ser definida para **SqlSink**. | Sim |
| escreverBatchSize |Número de linhas para inserir na tabela SQL *por lote*.<br/>Os valores permitidos são inteiros para o número de linhas. Por padrão, a Azure Data Factory determina dinamicamente o tamanho adequado do lote com base no tamanho da linha. |Não |
| escreverBatchTimeout |Esta propriedade especifica o tempo de espera para que a operação de inserção do lote esteja concluída antes de sair.<br/>Os valores permitidos são para o tempo. Um exemplo é "00:30:00" por 30 minutos. Se não for especificado qualquer valor, o prazo de paragem é de "02:00:00". |Não |
| preCopyScript |Esta propriedade especifica uma consulta SQL para que a atividade de cópia seja executada antes de escrever dados no SQL Server. É invocado apenas uma vez por cópia. Pode usar esta propriedade para limpar os dados pré-carregados. |Não |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem numa tabela-alvo. <br/>Este procedimento armazenado é *invocado por lote*. Para operações que funcionam apenas uma vez e não têm nada a `preCopyScript` ver com dados de origem, por exemplo, excluir ou truncar, utilizar a propriedade. | Não |
| storedProcedureTableTypeParâmetrometerName |O nome do parâmetro do tipo de mesa especificado no procedimento armazenado.  |Não |
| sqlWriterTableType |O nome do tipo de mesa a utilizar no procedimento armazenado. A atividade de cópia disponibiliza os dados numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são pares de nome e valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. | Não |
| tabelaOpção | Especifica se criar automaticamente a mesa do lavatório se não existir com base no esquema de origem. A criação de mesa automática não é suportada quando o lavatório especifica o procedimento armazenado ou a cópia encenada é configurada na atividade de cópia. Os valores `none` permitidos `autoCreate`são: (padrão), . |Não |

**Exemplo 1: Dados do apêndice**

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
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Exemplo 2: Invocar um procedimento armazenado durante a cópia**

Saiba mais detalhes a partir de [Invoke um procedimento armazenado a partir de um lavatório SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>As melhores práticas para carregar dados no Servidor SQL

Quando copia dados para o Servidor SQL, pode exigir um comportamento de escrita diferente:

- [Apêndice](#append-data): Os meus dados de origem têm apenas novos registos.
- [Upsert](#upsert-data): Os meus dados de origem têm inserções e atualizações.
- [Reescrever:](#overwrite-the-entire-table)Quero recarregar toda a tabela de dimensões de cada vez.
- [Escreva com lógica personalizada:](#write-data-with-custom-logic)Preciso de processamento extra antes da inserção final na tabela de destino.

Consulte as respetivas secções para saber como configurar na Azure Data Factory e nas melhores práticas.

### <a name="append-data"></a>Dados anexados

Os dados de aparação são o comportamento padrão deste conector sql server. A Azure Data Factory faz uma inserção a granel para escrever à sua mesa de forma eficiente. Pode configurar a fonte e afundar-se em conformidade na atividade da cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção 1:** Quando tiver uma grande quantidade de dados para copiar, utilize a seguinte abordagem para fazer um upsert: 

- Em primeiro lugar, utilize uma [tabela temporária](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) para carregar a granel todos os registos utilizando a atividade de cópia. Como as operações contra mesas temporárias não estão registadas, podes carregar milhões de discos em segundos.
- Executar uma atividade de procedimento armazenado na Azure Data Factory para aplicar uma declaração [de FUSÃO](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou INSERÇÃO/ATUALIZAÇÃO. Utilize a tabela temporária como fonte para efetuar todas as atualizações ou inserções como uma única transação. Desta forma, o número de viagens de ida e volta e de operações de registo é reduzido. No final da atividade do procedimento armazenado, a tabela temporária pode ser truncada para estar pronta para o próximo ciclo de upsert.

Como exemplo, na Azure Data Factory, pode criar um pipeline com uma **atividade de Cópia** acorrentada a uma atividade de Procedimento **Armazenado**. Os dados anteriores copiam os dados da sua loja de origem numa tabela temporária de base de dados, por exemplo, **##UpsertTempTable,** como o nome da tabela no conjunto de dados. Em seguida, este último invoca um procedimento armazenado para fundir os dados de origem da tabela temporária na tabela-alvo e limpar a tabela temporária.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Na sua base de dados, defina um procedimento armazenado com lógica MERGE, como o seguinte exemplo, que é apontado para a atividade de procedimento sinuoso anterior. Assuma que o alvo é a tabela **de Marketing** com três colunas: **ProfileID,** **Estado**e **Categoria**. Faça o upsert com base na coluna **ProfileID.**

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

**Opção 2:** Também pode optar por [invocar um procedimento armazenado dentro da atividade de cópia](#invoke-a-stored-procedure-from-a-sql-sink). Esta abordagem executa cada linha na tabela de origem em vez de usar a inserção a granel como a abordagem padrão na atividade de cópia, o que não é apropriado para o upsert em larga escala.

### <a name="overwrite-the-entire-table"></a>Sobrepor toda a mesa

Pode configurar a propriedade **préCopyScript** num afundado de atividade de cópia. Neste caso, para cada atividade de cópia que executa, a Azure Data Factory executa primeiro o script. Em seguida, executa a cópia para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, especifique um script para primeiro apagar todos os registos antes de carregar em massa os novos dados a partir da fonte.

### <a name="write-data-with-custom-logic"></a>Escreva dados com lógica personalizada

Os passos para escrever dados com lógica personalizada são semelhantes aos descritos na secção de [dados upsert.](#upsert-data) Quando necessitar de aplicar um tratamento extra antes da inserção final de dados de origem na tabela de destino, em larga escala, pode fazer uma de duas coisas: 

- Carregue para uma mesa temporária e, em seguida, invoque um procedimento armazenado. 
- Invoque um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Invoque um procedimento armazenado a partir de um lavatório SQL

Quando copia dados numa base de dados do SQL Server, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais. A função de procedimento armazenado tira partido dos [parâmetros de valor de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Invocar um procedimento armazenado processa os dados de linha a linha em vez de utilizar uma operação a granel, que não recomendamos para cópiaem em larga escala. Saiba mais sobre [as melhores práticas para carregar dados no Servidor SQL](#best-practice-for-loading-data-into-sql-server).

Pode utilizar um procedimento armazenado quando os mecanismos de cópia incorporados não servem o propósito. Um exemplo é quando pretende aplicar um tratamento extra antes da inserção final de dados de origem na tabela de destino. Alguns exemplos de processamento extra são quando você quer fundir colunas, procurar valores adicionais e inserir dados em mais de uma tabela.

A amostra que se segue mostra como utilizar um procedimento armazenado para fazer um upsert numa tabela na base de dados do SQL Server. Assuma que os dados de entrada e a tabela de **marketing** do lavatório têm cada uma três colunas: **ProfileID,** **Estado**e **Categoria**. Faça o upsert com base na coluna **ProfileID** e aplique-o apenas para uma categoria específica chamada "ProductA".

1. Na sua base de dados, defina o tipo de tabela com o mesmo nome que **o sqlWriterTableType**. O esquema do tipo de mesa é o mesmo que o esquema devolvido pelos seus dados de entrada.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Na sua base de dados, defina o procedimento armazenado com o mesmo nome que **o sqlWriterStoredProcedureName**. Trata os dados de entrada da sua fonte especificada e se funde na tabela de saída. O nome do parâmetro do tipo de tabela no procedimento armazenado é o mesmo que o nome do **quadro** definido no conjunto de dados.

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

3. Na Azure Data Factory, defina a secção **de sumidouro SQL** na atividade de cópia da seguinte forma:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Mapeamento de tipo de dados para Servidor SQL

Quando copia dados de e para o SQL Server, os seguintes mapeamentos são utilizados desde tipos de dados do Servidor SQL para tipos de dados provisórios da Azure Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [schema e mapeamentos de tipo](copy-activity-schema-and-type-mapping.md)de dados .

| Tipo de dados do Servidor SQL | Tipo de dados provisórios da Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datatimeoffset |DataTimeOffset |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| dinheiro |Decimal |
| nchar |String, Char[] |
| ntexto |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Único |
| linhaversão |Byte[] |
| tempo de data pequena |DateTime |
| smallint |Int16 |
| dinheiro pequeno |Decimal |
| sql_variant |Objeto |
| texto |String, Char[] |
| hora |TimeSpan |
| carimbo de data/hora |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Para tipos de dados que mapeiam para o tipo de decimal provisório, atualmente a Azure Data Factory suporta precisão até 28. Se tiver dados que requeiram precisão superior a 28, considere converter-se numa cadeia numa consulta SQL.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Obtenha propriedades de atividadede Metadados

Para saber mais detalhes sobre as propriedades, consulte [a atividade do GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="troubleshoot-connection-issues"></a>Resolver problemas de ligação

1. Configure a sua instância do Servidor SQL para aceitar ligações remotas. Inicie o Estúdio de Gestão do **Servidor SQL,** **servidor**de clique satislo, e selecione **Propriedades**. Selecione **Ligações** da lista e selecione as **ligações remotas permitir em relação a esta** caixa de verificação do servidor.

    ![Ativar ligações remotas](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Para obter passos detalhados, consulte [Configure a opção](https://msdn.microsoft.com/library/ms191464.aspx)de configuração do servidor de acesso remoto .

2. Inicie o gestor de configuração do **servidor SQL**. Expandir a configuração da **rede de servidores SQL** para a instância que deseja e selecionar **Protocolos para MSSQLSERVER**. Os protocolos aparecem no painel certo. Ativar o TCP/IP clicando no **TCP/IP** e selecionando **o Enable**.

    ![Ativar TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Para mais informações e formas alternativas de ativar o protocolo TCP/IP, consulte [Ativar ou desativar um protocolo](https://msdn.microsoft.com/library/ms191294.aspx)de rede de servidores .

3. Na mesma janela, clique duplo **no TCP/IP** para lançar a janela **TCP/IP Properties.**
4. Mude para o separador **endereços IP.** Desloque-se para baixo para ver a secção **IPAll.** Escreva a **porta TCP.** O predefinido é **1433**.
5. Crie uma **regra para o Windows Firewall** na máquina para permitir a entrada de tráfego através desta porta. 
6. **Verifique a ligação**: Para ligar ao Servidor SQL utilizando um nome totalmente qualificado, utilize o Estúdio de Gestão de Servidores SQL de uma máquina diferente. Um exemplo é `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
