---
title: Copiar dados de e para o SQL Server
description: Saiba como mover dados de e para a base de dados do SQL Server que esteja no local ou num VM Azure utilizando a Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/18/2020
ms.openlocfilehash: 5d35d0434f65f28b58a6d81172ade12aceb02987
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385001"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Copie dados de e para o SQL Server utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão da Azure Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-sqlserver-connector.md)
> * [Versão atual](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a atividade de cópia na Azure Data Factory para copiar dados de e para uma base de dados do SQL Server. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SQL Server é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)

Pode copiar dados de uma base de dados do SQL Server para qualquer loja de dados de lavatórios suportados. Ou, pode copiar dados de qualquer loja de dados de origem suportada para uma base de dados do SQL Server. Para obter uma lista de lojas de dados que são suportadas como fontes ou sumidouros pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SQL Server suporta:

- Versão SQL Server 2005 e acima.
- Copiar dados utilizando a autenticação SQL ou Windows.
- Como fonte, recuperar dados utilizando uma consulta SQL ou um procedimento armazenado. Também pode optar por copiar paralelamente a partir da fonte do SQL Server, ver a cópia paralela da secção [de base de dados SQL](#parallel-copy-from-sql-database) para obter mais detalhes.
- Como pia, criar automaticamente a tabela de destino, se não existir com base no esquema de origem; anexar dados a uma tabela ou invocar um procedimento armazenado com lógica personalizada durante a cópia. 

[SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb) não é suportado.

>[!NOTE]
>O SQL Server [Sempre Encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine) não é suportado por este conector agora. Para trabalhar, pode utilizar um [conector ODBC genérico](connector-odbc.md) e um controlador ODBC do SqL Server. Siga [esta orientação](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver) com as configurações de descarregamento e ligação do controlador ODBC.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector de base de dados SQL Server.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao SQL Server:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **SqlServer**. | Yes |
| conexãoStragem |Especifique **a informação** de ligação Desaquipeia as informações necessárias para ligar à base de dados do SQL Server utilizando a autenticação SQL ou a autenticação do Windows. Consulte as seguintes amostras.<br/>Também pode colocar uma palavra-passe no Cofre da Chave Azure. Se for a autenticação SQL, retire a `password` configuração da cadeia de ligação. Para obter mais informações, consulte o exemplo JSON seguindo as credenciais da tabela e [da loja no Cofre da Chave Azure](store-credentials-in-key-vault.md). |Yes |
| userName |Especifique um nome de utilizador se utilizar a autenticação do Windows. Um exemplo é **o nome de utilizador do nome de \\ domínio**. |No |
| palavra-passe |Especifique uma palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como **SecureString** para armazená-lo de forma segura na Azure Data Factory. Ou, pode [fazer referência a um segredo armazenado no Cofre da Chave Azure.](store-credentials-in-key-vault.md) |No |
| connectVia | Este [tempo de integração](concepts-integration-runtime.md) é utilizado para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza-se o tempo de execução da integração Azure predefinido. |No |

>[!TIP]
>Se tiver atingido um erro com o código de erro "UserErrorFailedToConnectToSqlServer" e uma mensagem como "O limite de sessão para a base de dados é XXX e foi atingido", adicione `Pooling=false` a sua cadeia de ligação e tente novamente.

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

**Exemplo 2: Utilize a autenticação SQL com uma palavra-passe no Cofre da Chave Azure**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados do SQL Server.

Para copiar dados de e para uma base de dados do SQL Server, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para **SqlServerTable**. | Yes |
| esquema | O nome do esquema. |Não para a fonte, sim para a pia  |
| table | Nome da mesa/vista. |Não para a fonte, sim para a pia  |
| tableName | Nome da tabela/vista com esquema. Esta propriedade é suportada para retrocompatibilidade. Para nova carga de trabalho, use `schema` e `table` . | Não para a fonte, sim para a pia |

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

Para obter uma lista completa de secções e propriedades disponíveis para uso para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte e pia do SQL Server.

### <a name="sql-server-as-a-source"></a>SQL Server como fonte

>[!TIP]
>Para carregar os dados do SQL Server de forma eficiente utilizando a partilha de dados, saiba mais a partir da [cópia paralela da base de dados SQL](#parallel-copy-from-sql-database).

Para copiar dados do SQL Server, deteta o tipo de origem na atividade de cópia para **SqlSource**. As seguintes propriedades são suportadas na secção fonte de origem da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida como **SqlSource**. | Yes |
| sqlReaderQuery |Utilize a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Esta propriedade é o nome do procedimento armazenado que lê dados da tabela de origem. A última declaração SQL deve ser uma declaração SELECT no procedimento armazenado. |No |
| parametrómetros de reserva armazenados |Estes parâmetros são para o procedimento armazenado.<br/>Os valores permitidos são pares de nomes ou valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |No |
| isolamentoLevel | Especifica o comportamento de bloqueio de transação para a fonte SQL. Os valores permitidos são: **ReadCommitted,** **ReadUncommitted,** **RepeatableRead,** **Serializable**, **Snapshot**. Se não for especificado, é utilizado o nível de isolamento predefinido da base de dados. Consulte [este doc](/dotnet/api/system.data.isolationlevel) para mais detalhes. | No |
| partitionOptions | Especifica as opções de partição de dados utilizadas para carregar dados do SQL Server. <br>Os valores permitidos são: **Nenhum** (padrão), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando uma opção de partição é ativada (isto é, `None` não), o grau de paralelismo para carregar simultaneamente dados do SQL Server é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) definição da atividade da cópia. | No |
| divisóriasSas | Especificar o grupo das definições para a partilha de dados. <br>Aplicar quando a opção de partição não `None` estiver. | No |
| ***Em `partitionSettings` :*** | | |
| partitionColumnName | Especificar o nome da coluna de origem **no tipo inteiro ou data/data** ( , , , , , `int` , , ou ) que será utilizado `smallint` por `bigint` `date` `smalldatetime` `datetime` `datetime2` `datetimeoffset` divisórias de alcance para cópia paralela. Se não for especificado, o índice ou a chave primária da tabela é detetado automaticamente e utilizado como coluna de partição.<br>Aplicar quando a opção de partição for `DynamicRange` . Se utilizar uma consulta para recuperar os dados de origem,  `?AdfDynamicRangePartitionCondition ` ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [de base de dados SQL.](#parallel-copy-from-sql-database) | No |
| partitionUpperBound | O valor máximo da coluna de partição para a divisão do intervalo de partição. Este valor é usado para decidir o passo de partição, não para filtrar as linhas na mesa. Todas as linhas da tabela ou resultado de consulta serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta o valor.  <br>Aplicar quando a opção de partição for `DynamicRange` . Por exemplo, consulte a cópia paralela da secção [de base de dados SQL.](#parallel-copy-from-sql-database) | No |
| partitionLowerBound | O valor mínimo da coluna de partição para a divisão do intervalo de divisão. Este valor é usado para decidir o passo de partição, não para filtrar as linhas na mesa. Todas as linhas da tabela ou resultado de consulta serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta o valor.<br>Aplicar quando a opção de partição for `DynamicRange` . Por exemplo, consulte a cópia paralela da secção [de base de dados SQL.](#parallel-copy-from-sql-database) | No |

**Tenha em atenção os seguintes pontos:**

- Se **o SqlReaderQuery** for especificado para **o SqlSource,** a atividade de cópia executa esta consulta com a fonte do SQL Server para obter os dados. Também pode especificar um procedimento armazenado especificando **o nome sqlReaderStoredProcedureName** e **os Computadores Deprocedures armazenados** se o procedimento armazenado tiver parâmetros.
- Ao utilizar o procedimento armazenado na fonte para obter dados, note se o seu procedimento armazenado for concebido como devolvendo esquemas diferentes quando o valor de parâmetro diferente for passado, poderá encontrar falhas ou ver resultados inesperados ao importar esquemas de UI ou ao copiar dados para base de dados SQL com criação de quadros automáticos.

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
> Saiba mais sobre os comportamentos de escrita suportados, configurações e melhores práticas das [melhores práticas para carregar dados no SQL Server](#best-practice-for-loading-data-into-sql-server).

Para copiar dados para o SQL Server, desaperte o tipo de pia na atividade da cópia para **o SqlSink**. As seguintes propriedades são suportadas na secção de lavatório de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do lavatório de atividade de cópia deve ser definida como **SqlSink**. | Yes |
| preCopyScript |Esta propriedade especifica uma consulta SQL para a atividade de cópia a executar antes de escrever dados no SQL Server. É invocado apenas uma vez por cópia. Pode utilizar esta propriedade para limpar os dados pré-carregados. |No |
| mesaOption | Especifica se deve [criar automaticamente a tabela do lavatório](copy-activity-overview.md#auto-create-sink-tables) se não existir com base no esquema de origem. A criação de tabela automática não é suportada quando a pia especifica o procedimento armazenado. Os valores permitidos são: `none` (padrão), `autoCreate` . |No |
| sqlWriterStorEdProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem numa tabela-alvo. <br/>Este procedimento armazenado é *invocado por lote*. Para operações que funcionam apenas uma vez e não têm nada a ver com dados de origem, por exemplo, apagar ou truncar, utilize a `preCopyScript` propriedade.<br>Veja o exemplo de [Invocar um procedimento armazenado a partir de um lavatório SQL](#invoke-a-stored-procedure-from-a-sql-sink). | No |
| nome de parametrómetro de computador |O nome do parâmetro do tipo de tabela especificado no procedimento armazenado.  |No |
| SqlWriterTableType |O nome do tipo de mesa a utilizar no procedimento armazenado. A atividade de cópia torna os dados disponíveis numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |No |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são pares de nomes e valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. | No |
| escreverBatchSize |Número de linhas para inserir na tabela SQL *por lote*.<br/>Os valores permitidos são inteiros para o número de linhas. Por predefinição, a Azure Data Factory determina dinamicamente o tamanho apropriado do lote com base no tamanho da linha. |No |
| escreverBatchTimeout |Esta propriedade especifica o tempo de espera para a operação de inserção do lote ser concluída antes do tempo de esmutar.<br/>Os valores permitidos são para o tempo. Um exemplo é "00:30:00" por 30 minutos. Se não for especificado qualquer valor, o intervalo de tempo predefinido para "02:00:00". |No |

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
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemplo 2: Invocar um procedimento armazenado durante a cópia**

Saiba mais detalhes da [Invoke um procedimento armazenado a partir de um lavatório SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="parallel-copy-from-sql-database"></a>Cópia paralela da base de dados SQL

O conector SQL Server na atividade de cópia fornece partição de dados incorporada para copiar dados em paralelo. Pode encontrar opções de partição de dados no separador **'Fonte'** da atividade da cópia.

![Screenshot das opções de partição](./media/connector-sql-server/connector-sql-partition-options.png)

Quando ativa a cópia dividida, a atividade de cópia executa consultas paralelas contra a fonte do seu SQL Server para carregar dados por divisórias. O grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) regulação da atividade da cópia. Por exemplo, se definir `parallelCopies` para quatro, data factory simultaneamente gera e executa quatro consultas com base na sua opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados do seu SQL Server.

Sugere-se que ative uma cópia paralela com a partilha de dados, especialmente quando carrega uma grande quantidade de dados do seu SQL Server. São sugeridas configurações para diferentes cenários. Ao copiar dados na loja de dados baseada em ficheiros, recomenda-se escrever para uma pasta como vários ficheiros (especificar apenas o nome da pasta), caso em que o desempenho é melhor do que escrever num único ficheiro.

| Scenario                                                     | Definições sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa da mesa grande, com divisórias físicas.        | **Opção de partição**: Divisórias físicas da tabela. <br><br/>Durante a execução, a Data Factory deteta automaticamente as divisórias físicas e copia dados por partições. <br><br/>Para verificar se a sua mesa tem ou não partição física, pode consultar [esta consulta](#sample-query-to-check-physical-partition). |
| Carga completa da mesa grande, sem divisórias físicas, enquanto com uma coluna de inteiro ou data para partição de dados. | **Opções de partição**: Partição dinâmica do alcance.<br>**Coluna de partição** (opcional): Especificar a coluna utilizada para os dados de partição. Se não for especificado, utiliza-se o índice ou a coluna-chave primária.<br/>**Limite superior da partição** e **divisória inferior** (opcional): Especifique se pretende determinar o passo de partição. Isto não é para filtrar as linhas na mesa, todas as linhas na mesa serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta automaticamente os valores.<br><br>Por exemplo, se a sua coluna de partição "ID" tiver valores que variam entre 1 e 100, e definir o limite inferior como 20 e o limite superior como 80, com cópia paralela como 4, Data Factory recupera dados por 4 partições - IDs na gama <=20, [21, 50], [51, 80], e >=81, respectivamente. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada, sem divisórias físicas, enquanto com uma coluna inteiro ou data/data para a partilha de dados. | **Opções de partição**: Partição dinâmica do alcance.<br>**Consulta:** `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Coluna de partição**: Especificar a coluna utilizada para os dados de partição.<br>**Limite superior da partição** e **divisória inferior** (opcional): Especifique se pretende determinar o passo de partição. Isto não é para filtrar as linhas na mesa, todas as linhas no resultado da consulta serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta o valor.<br><br>Durante a execução, a Data Factory `?AdfRangePartitionColumnName` substitui-se pelo nome real da coluna e gamas de valor para cada partição, e envia para o SQL Server. <br>Por exemplo, se a sua coluna de partição "ID" tiver valores que variam entre 1 e 100, e definir o limite inferior como 20 e o limite superior como 80, com cópia paralela como 4, Data Factory recupera dados por 4 partições- IDs na gama <=20, [21, 50], [51, 80], e >=81, respectivamente. <br><br>Aqui estão mais consultas de amostra para diferentes cenários:<br> 1. Consulta de toda a tabela: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. Consulta a partir de uma tabela com seleção de colunas e filtros adicionais de cláusulas: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. Consulta com subqueries: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. Consulta com partição em subquery: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Melhores práticas para carregar dados com opção de partição:

1. Escolha uma coluna distinta como coluna de partição (como chave primária ou chave única) para evitar distorções de dados. 
2. Se a mesa tiver partição incorporada, utilize a opção de partição "Divisórias físicas da mesa" para obter um melhor desempenho.    
3. Se utilizar o tempo de execução da integração do Azure para copiar dados, pode definir "[Unidades de Integração de Dados (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) para utilizar mais recursos informáticos. Verifique os cenários aplicáveis.
4. "[Grau de paralelismo de cópia](copy-activity-performance-features.md#parallel-copy)" controla os números de partição, definindo este número demasiado grande em algum momento prejudica o desempenho, recomendando definir este número como (DIU ou número de nós de IR auto-hospedados) * (2 a 4).

**Exemplo: carga completa da mesa grande com divisórias físicas**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exemplo: consulta com partição de gama dinâmica**

```json
"source": {
    "type": "SqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Consulta de amostra para verificar partição física

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Se a mesa tiver partição física, veria "HasPartition" como "sim" como o seguinte.

![Resultado da consulta sql](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-sql-server"></a>Melhores práticas para carregar dados no SQL Server

Ao copiar dados para o SQL Server, poderá necessitar de um comportamento de escrita diferente:

- [Apêndice:](#append-data)Os meus dados de origem têm apenas novos registos.
- [Upsert](#upsert-data): Os meus dados de origem têm inserções e atualizações.
- [Overwrite](#overwrite-the-entire-table): Quero recarregar cada vez toda a tabela de dimensões.
- [Escreva com lógica personalizada](#write-data-with-custom-logic): Preciso de processamento extra antes da inserção final na tabela de destino.

Consulte as respetivas secções para saber como configurar na Azure Data Factory e nas melhores práticas.

### <a name="append-data"></a>Dados do apêndice

Os dados appending são o comportamento padrão deste conector de pia SQL Server. A Azure Data Factory faz uma inserção a granel para escrever na sua mesa de forma eficiente. Pode configurar a fonte e afundar-se em conformidade na atividade da cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção 1:** Quando tiver uma grande quantidade de dados para copiar, pode carregar todos os registos em massa numa tabela de encenação utilizando a atividade da cópia e, em seguida, executar uma atividade de procedimento armazenada para aplicar uma declaração [DE FUSÃO](/sql/t-sql/statements/merge-transact-sql) ou INSERT/UPDATE numa única tomada. 

Atualmente, a atividade de cópia não suporta o carregamento de dados numa tabela temporária de base de dados. Existe uma forma avançada de o configurar com uma combinação de múltiplas atividades, consulte os [cenários de Upsert de Base de Dados SQL Otimize.](https://github.com/scoriani/azuresqlbulkupsert) Abaixo mostra uma amostra de usar uma tabela permanente como encenação.

Como exemplo, na Azure Data Factory, pode criar um oleoduto com uma **atividade Copy** acorrentada com uma atividade de **Procedimento Armazenado.** Os primeiros copiam dados da sua loja de origem para uma tabela de posição do SQL Server, por exemplo, **UpsertStagingTable,** como o nome da tabela no conjunto de dados. Em seguida, este último invoca um procedimento armazenado para fundir dados de origem da tabela de paragem na tabela alvo e limpar a tabela de preparação.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Na sua base de dados, defina um procedimento armazenado com lógica MERGE, como o exemplo a seguir, que é apontado a partir da atividade de procedimento armazenado anterior. Assuma que o alvo é a tabela **de Marketing** com três colunas: **ProfileID,** **Estado** e **Categoria**. Faça o upsert com base na coluna **ProfileID.**

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**Opção 2:** Pode optar por [invocar um procedimento armazenado no âmbito da atividade de cópia](#invoke-a-stored-procedure-from-a-sql-sink). Esta abordagem executa cada lote (tal como rege-se pela `writeBatchSize` propriedade) na tabela de origem em vez de utilizar o inserível a granel como abordagem padrão na atividade da cópia.

### <a name="overwrite-the-entire-table"></a>Sobrepor toda a mesa

Pode configurar a propriedade **pré-CopyScript** numa pia de atividade de cópia. Neste caso, para cada atividade de cópia que executa, a Azure Data Factory executa primeiro o script. Em seguida, executa a cópia para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, especifique um script para primeiro eliminar todos os registos antes de carregar em massa os novos dados da fonte.

### <a name="write-data-with-custom-logic"></a>Escreva dados com lógica personalizada

Os passos para escrever dados com lógica personalizada são semelhantes aos descritos na secção [de dados upsert.](#upsert-data) Quando tiver de aplicar um tratamento extra antes da inserção final dos dados de origem na tabela de destino, pode carregar para uma tabela de encenação e, em seguida, invocar a atividade do procedimento armazenado, ou invocar um procedimento armazenado na pia da atividade de cópia para aplicar dados.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado a partir de um lavatório SQL

Ao copiar dados na base de dados do SQL Server, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais em cada lote da tabela de origem. A função de procedimento armazenado tira partido dos [parâmetros valorizados da tabela](/dotnet/framework/data/adonet/sql/table-valued-parameters).

Pode utilizar um procedimento armazenado quando os mecanismos de cópia incorporados não servem o propósito. Um exemplo é quando pretende aplicar um processamento extra antes da inserção final dos dados de origem na tabela de destino. Alguns exemplos de processamento extra são quando se pretende fundir colunas, procurar valores adicionais e inserir em mais de uma tabela.

A amostra que se segue mostra como utilizar um procedimento armazenado para fazer um upsert numa tabela na base de dados do SQL Server. Assuma que os dados de entrada e a tabela **de marketing** do lavatório têm três colunas: **ProfileID,** **Estado** e **Categoria**. Faça o upsert com base na coluna **ProfileID,** e aplique-o apenas para uma categoria específica chamada "ProductA".

1. Na sua base de dados, defina o tipo de tabela com o mesmo nome que **sqlWriterTableType**. O esquema do tipo de tabela é o mesmo que o esquema devolvido pelos seus dados de entrada.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Na sua base de dados, defina o procedimento armazenado com o mesmo nome que **o sqlWriterStorEdProcedureName**. Trata os dados de entrada da sua fonte especificada e funde-se na tabela de saída. O nome do parâmetro do tipo de tabela no procedimento armazenado é o mesmo que o **nome de tabela** definido no conjunto de dados.

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

3. Na Azure Data Factory, defina a secção de **pia SQL** na atividade de cópia da seguinte forma:

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

## <a name="data-type-mapping-for-sql-server"></a>Mapeamento do tipo de dados para o SQL Server

Quando copia dados de e para o SQL Server, os seguintes mapeamentos são usados desde os tipos de dados do SQL Server até aos tipos de dados provisórios da Azure Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [o Schema e os mapeamentos do tipo de dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do SQL Server | Tipo de dados provisórios da Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| data |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datatimeoff |Início de execução de tempo de data |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinário(máx)) |Byte[] |
| Float |Double (Duplo) |
| image |Byte[] |
| int |Int32 |
| dinheiro |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Único |
| rowversão |Byte[] |
| hora pequena |DateTime |
| smallint |Int16 |
| pequeno dinheiro |Decimal |
| sql_variant |Objeto |
| texto |String, Char[] |
| hora |TimeSpan |
| carimbo de data/hora |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinário |Byte[] |
| varchar |String, Char[] |
| xml |String |

>[!NOTE]
> Para tipos de dados que mapeiam para o tipo decimal provisório, atualmente a atividade Copy suporta precisão até 28. Se tiver dados que exijam precisão superior a 28, considere converter-se a uma cadeia numa consulta SQL.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade getMetadata

Para saber mais detalhes sobre as propriedades, consulte a [atividade da GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="using-always-encrypted"></a>Usando sempre encriptado

Quando copiar dados de/para SQL Server com [Always Encrypted,](/sql/relational-databases/security/encryption/always-encrypted-database-engine)utilize [o conector ODBC genérico](connector-odbc.md) e o controlador ODBC do Servidor SQL através do tempo de execução de integração auto-hospedado. Este conector SQL Server não suporta sempre encriptado. 

Mais especificamente:

1. Crie um tempo de integração auto-hospedado se não tiver um. Consulte o artigo [de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.

2. Descarregue o controlador ODBC de 64 bits para o SQL Server a partir [daqui](/sql/connect/odbc/download-odbc-driver-for-sql-server)e instale na máquina de tempo de execução de integração. Saiba mais sobre como este condutor funciona a partir da [utilização sempre encriptada com o controlador ODBC para o SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Crie um serviço ligado com o tipo ODBC para ligar à sua base de dados SQL. Para utilizar a autenticação SQL, especifique a cadeia de ligação ODBC como abaixo e selecione a autenticação **Básica** para definir o nome de utilizador e a palavra-passe.

    ```
    Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
    ```

4. Crie conjunto de dados e copie a atividade com o tipo ODBC em conformidade. Saiba mais a partir do artigo do [conector ODBC.](connector-odbc.md)

## <a name="troubleshoot-connection-issues"></a>Resolver problemas de ligação

1. Configure a sua instância SQL Server para aceitar ligações remotas. Inicie **o SQL Server Management Studio,** **o servidor** de clique à direita e selecione **Propriedades**. Selecione **Ligações** da lista e selecione as **ligações remotas permitir esta** caixa de verificação do servidor.

    ![Ativar ligações remotas](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Para obter etapas detalhadas, consulte [configurar a opção de configuração do servidor de acesso remoto](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option).

2. Iniciar **o Gestor de Configuração do Servidor SQL**. Expanda a **configuração da rede de servidor SQL** para o caso que pretende e selecione **Protocolos para MSSQLSERVER**. Os protocolos aparecem no painel certo. Ativar o TCP/IP clicando à direita **no TCP/IP** e selecionando **Enable**.

    ![Ativar TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Para obter mais informações e formas alternativas de permitir o protocolo TCP/IP, consulte [Ativar ou desativar um protocolo de rede de servidores](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).

3. Na mesma janela, clique duas vezes em **TCP/IP** para lançar a janela **TCP/IP Properties.**
4. Mude para o separador **endereços IP.** Desloque-se para baixo para ver a secção **IPAll.** Escreva o **Porto TCP.** O padrão é **1433**.
5. Crie uma **regra para o Windows Firewall** na máquina para permitir a entrada de tráfego através desta porta. 
6. **Verificar a ligação**: Para ligar ao SQL Server utilizando um nome totalmente qualificado, utilize o SQL Server Management Studio a partir de uma máquina diferente. Um exemplo é `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte [lojas de dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).