---
title: Copiar e transformar dados na Base de Dados Azure SQL
description: Saiba como copiar dados de e para a Base de Dados Azure SQL e transformar dados na Base de Dados Azure SQL utilizando a Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/12/2020
ms.openlocfilehash: fa994525285ffe363f734e9b706252105b05ff26
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428444"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Copiar e transformar dados na Base de Dados Azure SQL utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão da Azure Data Factory que está a utilizar:"]
>
> - [Versão 1](v1/data-factory-azure-sql-connector.md)
> - [Versão atual](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Copy Activity in Azure Data Factory para copiar dados de e para Azure SQL Database, e utilizar o Fluxo de Dados para transformar dados na Base de Dados Azure SQL. Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure SQL Database é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com tabela [de matriz de fonte/pia suportada](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)

Para a atividade copy, este conector Azure SQL Database suporta estas funções:

- Copiar dados utilizando a autenticação SQL e o Azure Ative Directory (Azure AD) A autenticação de fichas de aplicação com um principal serviço ou identidades geridas para recursos Azure.
- Como fonte, recuperar dados utilizando uma consulta SQL ou um procedimento armazenado. Também pode optar por copiar paralelamente a partir de uma fonte de base de dados Azure SQL, consulte a cópia paralela da secção [de base de dados SQL](#parallel-copy-from-sql-database) para obter mais detalhes.
- Como pia, criar automaticamente a tabela de destino, se não existir com base no esquema de origem; anexar dados a uma tabela ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

Se utilizar o [nível sem servidor](../azure-sql/database/serverless-tier-overview.md)Azure SQL Database , note que quando o servidor está parado, a atividade falha em vez de esperar que o currículo automático esteja pronto. Pode adicionar atividades de redação ou cadeia de atividades adicionais para se certificar de que o servidor está ao vivo após a execução real.

>[!NOTE]
> A base de dados Azure SQL [Sempre encriptada](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) não é suportada por este conector agora. Para trabalhar, pode utilizar um [conector ODBC genérico](connector-odbc.md) e um controlador ODBC do SQL Server através de um tempo de integração auto-hospedado. Saiba mais [na secção "Sempre Encriptado".](#using-always-encrypted) 

> [!IMPORTANT]
> Se copiar dados utilizando o tempo de execução da integração do Azure, configure uma [regra de firewall ao nível do servidor](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) para que os serviços Azure possam aceder ao servidor.
> Se copiar dados utilizando um tempo de integração auto-hospedado, configufique a firewall para permitir o intervalo ip apropriado. Esta gama inclui o IP da máquina que é usado para ligar à Base de Dados Azure SQL.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Azure Data Factory específicas de um conector Azure SQL Database.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

Estas propriedades são suportadas para um serviço de base de dados Azure SQL ligado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **AzureSqlDatabase**. | Sim |
| conexãoStragem | Especifique as informações necessárias para ligar à instância de Base de Dados Azure SQL para a **propriedade connectionString.** <br/>Também pode colocar uma senha ou chave principal de serviço no Cofre da Chave Azure. Se for a autenticação SQL, retire a `password` configuração da cadeia de ligação. Para obter mais informações, consulte o exemplo JSON seguindo as credenciais da tabela e [da loja no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim |
| servicePrincipalId | Especifique a identificação do cliente da aplicação. | Sim, quando se usa a autenticação AD AZure com um diretor de serviço |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como **SecureString** para armazená-lo de forma segura na Azure Data Factory ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim, quando se usa a autenticação AD AZure com um diretor de serviço |
| inquilino | Especifique as informações do inquilino, como o nome de domínio ou iD do inquilino, sob o qual a sua aplicação reside. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | Sim, quando se usa a autenticação AD AZure com um diretor de serviço |
| AzureCloudType | Para a autenticação principal do serviço, especifique o tipo de ambiente em nuvem Azure para o qual a sua aplicação AD AZure está registada. <br/> Os valores permitidos são **AzurePublic,** **AzureChina,** **AzureUsGovernment,** e **AzureGermany**. Por padrão, o ambiente em nuvem da fábrica de dados é utilizado. | Não |
| connectVia | Este [tempo de integração](concepts-integration-runtime.md) é utilizado para se ligar à loja de dados. Pode utilizar o tempo de funcionamento da integração Azure ou um tempo de integração auto-hospedado se a sua loja de dados estiver localizada numa rede privada. Se não for especificado, utiliza-se o tempo de execução da integração Azure predefinido. | Não |

Para diferentes tipos de autenticação, consulte as seguintes secções sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação SQL](#sql-authentication)
- [Autenticação simbólica de aplicação Azure: Diretor de serviço](#service-principal-authentication)
- [Autenticação simbólica de aplicação Azure: Identidades geridas para recursos Azure](#managed-identity)

>[!TIP]
>Se tiver atingido um erro com o código de erro "UserErrorFailedToConnectToSqlServer" e uma mensagem como "O limite de sessão para a base de dados é XXX e foi atingido", adicione `Pooling=false` a sua cadeia de ligação e tente novamente.

### <a name="sql-authentication"></a>Autenticação do SQL

**Exemplo: utilização da autenticação SQL**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: senha no Cofre da Chave Azure**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para utilizar uma autenticação simbólica de aplicação Azure AD baseada em serviço, siga estes passos:

1. [Crie uma aplicação Azure Ative Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) a partir do portal Azure. Tome nota do nome da aplicação e dos seguintes valores que definem o serviço ligado:

    - ID da Aplicação
    - Chave de aplicação
    - ID do inquilino

2. [Forcê um administrador do Azure Ative Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) para o seu servidor no portal Azure se ainda não o fez. O administrador AD Azure deve ser um utilizador AZure AD ou um grupo Azure AD, mas não pode ser um diretor de serviço. Este passo é feito para que, no passo seguinte, possa utilizar uma identidade AD AZure para criar um utilizador de base de dados contido para o principal do serviço.

3. [Criar utilizadores de bases de dados contidos](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) para o principal serviço. Conecte-se à base de dados a partir ou à qual pretende copiar dados utilizando ferramentas como o SQL Server Management Studio, com uma identidade AD AZure que tem pelo menos qualquer permissão do UTILIZADOR. Executar o seguinte T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Conceda ao chefe de serviço permissões necessárias, como normalmente faz para utilizadores sql ou outros. Executar o seguinte código. Para mais opções, consulte [este documento.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Configure um serviço de base de dados Azure SQL ligado na Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço ligado que utiliza a autenticação principal do serviço

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Identidades geridas para autenticação de recursos Azure

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos da Azure](data-factory-service-identity.md) que representa a fábrica de dados específica. Pode utilizar esta identidade gerida para autenticação da Base de Dados Azure SQL. A fábrica designada pode aceder e copiar dados de ou para a sua base de dados utilizando esta identidade.

Para utilizar a autenticação de identidade gerida, siga estes passos.

1. [Forcê um administrador do Azure Ative Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) para o seu servidor no portal Azure se ainda não o fez. O administrador AD Azure pode ser um utilizador AD Azure ou um grupo AZure AD. Se conceder ao grupo com identidade gerida um papel de administrador, salte os passos 3 e 4. O administrador tem acesso total à base de dados.

2. [Crie utilizadores de bases de dados contidos](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) para a identidade gerida da Azure Data Factory. Conecte-se à base de dados a partir ou à qual pretende copiar dados utilizando ferramentas como o SQL Server Management Studio, com uma identidade AD AZure que tem pelo menos qualquer permissão do UTILIZADOR. Executar o seguinte T-SQL:
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Conceder à Data Factory permissões necessárias como normalmente faz para utilizadores de SQL e outros. Executar o seguinte código. Para mais opções, consulte [este documento.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Configure um serviço de base de dados Azure SQL ligado na Azure Data Factory.

**Exemplo**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services).

As seguintes propriedades são suportadas para o conjunto de dados da Base de Dados Azure SQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **do tipo** do conjunto de dados deve ser definida para **AzureSqlTable**. | Sim |
| esquema | O nome do esquema. |Não para a fonte, sim para a pia  |
| mesa | Nome da mesa/vista. |Não para a fonte, sim para a pia  |
| tableName | Nome da tabela/vista com esquema. Esta propriedade é suportada para retrocompatibilidade. Para nova carga de trabalho, use `schema` e `table` . | Não para a fonte, sim para a pia |

### <a name="dataset-properties-example"></a>Exemplo de propriedades de dataset

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades,](concepts-pipelines-activities.md)consulte Pipelines . Esta secção fornece uma lista de propriedades suportadas pela fonte e pia da Base de Dados Azure SQL.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Base de Dados como a fonte

>[!TIP]
>Para carregar os dados da Base de Dados Azure SQL de forma eficiente utilizando a partilha de dados, saiba mais a partir [da base de dados SQL](#parallel-copy-from-sql-database).

Para copiar dados da Base de Dados Azure SQL, as seguintes propriedades são suportadas na secção **fonte de origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** da fonte de atividade de cópia deve ser definida para **AzureSqlSource**. O tipo "SqlSource" ainda é suportado para retrocompatibilidade. | Sim |
| sqlReaderQuery | Esta propriedade usa a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. | Não |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê os dados da tabela de origem. A última declaração SQL deve ser uma declaração SELECT no procedimento armazenado. | Não |
| parametrómetros de reserva armazenados | Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são pares de nomes ou valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. | Não |
| isolamentoLevel | Especifica o comportamento de bloqueio de transação para a fonte SQL. Os valores permitidos são: **ReadCommitted,** **ReadUncommitted,** **RepeatableRead,** **Serializable**, **Snapshot**. Se não for especificado, é utilizado o nível de isolamento predefinido da base de dados. Consulte [este doc](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para mais detalhes. | Não |
| partitionOptions | Especifica as opções de partição de dados utilizadas para carregar dados a partir da Base de Dados Azure SQL. <br>Os valores permitidos são: **Nenhum** (padrão), **PhysicalPartitionsOfTable**e **DynamicRange**.<br>Quando uma opção de partição é ativada (isto é, `None` não), o grau de paralelismo para carregar simultaneamente dados de uma Base de Dados Azure SQL é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) definição da atividade da cópia. | Não |
| divisóriasSas | Especificar o grupo das definições para a partilha de dados. <br>Aplicar quando a opção de partição não `None` estiver. | Não |
| **_Em: `partitionSettings` __* | | |
| partitionColumnName | Especificar o nome da coluna de origem _*no tipo inteiro ou data/data** que será utilizado por divisórias de alcance para cópia paralela. Se não for especificado, o índice ou a chave primária da tabela é detetado automaticamente e utilizado como coluna de partição.<br>Aplicar quando a opção de partição for `DynamicRange` . Se utilizar uma consulta para recuperar os dados de origem,  `?AdfDynamicRangePartitionCondition ` ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [de base de dados SQL.](#parallel-copy-from-sql-database) | Não |
| partitionUpperBound | O valor máximo da coluna de partição para a divisão do intervalo de partição. Este valor é usado para decidir o passo de partição, não para filtrar as linhas na mesa. Todas as linhas da tabela ou resultado de consulta serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta o valor.  <br>Aplicar quando a opção de partição for `DynamicRange` . Por exemplo, consulte a cópia paralela da secção [de base de dados SQL.](#parallel-copy-from-sql-database) | Não |
| partitionLowerBound | O valor mínimo da coluna de partição para a divisão do intervalo de divisão. Este valor é usado para decidir o passo de partição, não para filtrar as linhas na mesa. Todas as linhas da tabela ou resultado de consulta serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta o valor.<br>Aplicar quando a opção de partição for `DynamicRange` . Por exemplo, consulte a cópia paralela da secção [de base de dados SQL.](#parallel-copy-from-sql-database) | Não |

**Pontos a notar:**

- Se **o SqlReaderQuery** for especificado para **a AzureSqlSource,** a atividade de cópia executa esta consulta com a fonte de Base de Dados Azure SQL para obter os dados. Também pode especificar um procedimento armazenado especificando **o nome sqlReaderStoredProcedureName** e **os Computadores Deprocedures armazenados** se o procedimento armazenado tiver parâmetros.
- Se não especificar nem **sqlReaderQuery** ou **sqlReaderStorEdProcedureName,** as colunas definidas na secção "estrutura" do conjunto de dados JSON são utilizadas para construir uma consulta. A consulta `select column1, column2 from mytable` vai contra a Base de Dados Azure SQL. Se a definição de conjunto de dados não tiver "estrutura", todas as colunas são selecionadas a partir da tabela.

#### <a name="sql-query-example"></a>Exemplo de consulta SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Exemplo de procedimento armazenado

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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

### <a name="stored-procedure-definition"></a>Definição de procedimento armazenado

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Base de Dados como a pia

> [!TIP]
> Saiba mais sobre os comportamentos de escrita suportados, configurações e boas práticas das [melhores práticas para carregar dados na Base de Dados Azure SQL](#best-practice-for-loading-data-into-azure-sql-database).

Para copiar dados para a Base de Dados Azure SQL, as seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** do lavatório de atividade de cópia deve ser definida para **AzureSqlSink**. O tipo "SqlSink" ainda é suportado para retrocompatibilidade. | Sim |
| preCopyScript | Especifique uma consulta SQL para a atividade da cópia a executar antes de escrever dados na Base de Dados Azure SQL. É invocado apenas uma vez por cópia. Utilize esta propriedade para limpar os dados pré-carregados. | Não |
| mesaOption | Especifica se deve [criar automaticamente a tabela do lavatório](copy-activity-overview.md#auto-create-sink-tables) se não existir com base no esquema de origem. <br>A criação de tabela automática não é suportada quando a pia especifica o procedimento armazenado. <br>Os valores permitidos são: `none` (padrão), `autoCreate` . | Não |
| sqlWriterStorEdProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem numa tabela-alvo. <br/>Este procedimento armazenado é *invocado por lote*. Para operações que funcionam apenas uma vez e não têm nada a ver com dados de origem, por exemplo, apagar ou truncar, utilize a `preCopyScript` propriedade.<br>Veja o exemplo de [Invocar um procedimento armazenado a partir de um lavatório SQL](#invoke-a-stored-procedure-from-a-sql-sink). | Não |
| nome de parametrómetro de computador |O nome do parâmetro do tipo de tabela especificado no procedimento armazenado.  |Não |
| SqlWriterTableType |O nome do tipo de mesa a utilizar no procedimento armazenado. A atividade de cópia torna os dados disponíveis numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Não |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são pares de nomes e valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. | Não |
| escreverBatchSize | Número de linhas para inserir na tabela SQL *por lote*.<br/> O valor permitido é **inteiro (número** de linhas). Por predefinição, a Azure Data Factory determina dinamicamente o tamanho apropriado do lote com base no tamanho da linha. | Não |
| escreverBatchTimeout | O tempo de espera para o funcionamento do encaixe do lote terminar antes de se esgotar.<br/> O valor permitido é **o tempo.** Um exemplo é "00:30:00" (30 minutos). | Não |
| desativaçãoMetricosCollecto | A Data Factory recolhe métricas como DTUs de base de dados Azure SQL para otimização de desempenho de cópia e recomendações. Se estiver preocupado com este comportamento, especifique `true` para desligá-lo. | Não (o padrão `false` é) |

**Exemplo 1: Dados do apêndice**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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

O conector Azure SQL Database na atividade de cópia fornece partição de dados incorporada para copiar dados em paralelo. Pode encontrar opções de partição de dados no separador **'Fonte'** da atividade da cópia.

![Screenshot das opções de partição](./media/connector-sql-server/connector-sql-partition-options.png)

Quando ativa a cópia dividida, a atividade de cópia executa consultas paralelas contra a sua fonte de Base de Dados Azure SQL para carregar dados por partições. O grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) regulação da atividade da cópia. Por exemplo, se definir `parallelCopies` para quatro, data factory simultaneamente gera e executa quatro consultas com base na sua opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados da sua Base de Dados Azure SQL.

Sugere-se que ative uma cópia paralela com a partilha de dados, especialmente quando carrega uma grande quantidade de dados da sua Base de Dados Azure SQL. São sugeridas configurações para diferentes cenários. Ao copiar dados na loja de dados baseada em ficheiros, recomenda-se escrever para uma pasta como vários ficheiros (especificar apenas o nome da pasta), caso em que o desempenho é melhor do que escrever num único ficheiro.

| Cenário                                                     | Definições sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa da mesa grande, com divisórias físicas.        | **Opção de partição**: Divisórias físicas da tabela. <br><br/>Durante a execução, a Data Factory deteta automaticamente as divisórias físicas e copia dados por partições. <br><br/>Para verificar se a sua mesa tem ou não partição física, pode consultar [esta consulta](#sample-query-to-check-physical-partition). |
| Carga completa da mesa grande, sem divisórias físicas, enquanto com uma coluna de inteiro ou data para partição de dados. | **Opções de partição**: Partição dinâmica do alcance.<br>**Coluna de partição** (opcional): Especificar a coluna utilizada para os dados de partição. Se não for especificado, utiliza-se o índice ou a coluna-chave primária.<br/>**Limite superior da partição** e **divisória inferior** (opcional): Especifique se pretende determinar o passo de partição. Isto não é para filtrar as linhas na mesa, todas as linhas na mesa serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta automaticamente os valores.<br><br>Por exemplo, se a sua coluna de partição "ID" tiver valores que variam entre 1 e 100, e definir o limite inferior como 20 e o limite superior como 80, com cópia paralela como 4, Data Factory recupera dados por 4 partições - IDs na gama <=20, [21, 50], [51, 80], e >=81, respectivamente. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada, sem divisórias físicas, enquanto com uma coluna inteiro ou data/data para a partilha de dados. | **Opções de partição**: Partição dinâmica do alcance.<br>**Consulta:** `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Coluna de partição**: Especificar a coluna utilizada para os dados de partição.<br>**Limite superior da partição** e **divisória inferior** (opcional): Especifique se pretende determinar o passo de partição. Isto não é para filtrar as linhas na mesa, todas as linhas no resultado da consulta serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta o valor.<br><br>Durante a execução, a Data Factory `?AdfRangePartitionColumnName` substitui-se pelo nome real da coluna e gamas de valor para cada partição, e envia para a Base de Dados Azure SQL. <br>Por exemplo, se a sua coluna de partição "ID" tiver valores que variam entre 1 e 100, e definir o limite inferior como 20 e o limite superior como 80, com cópia paralela como 4, Data Factory recupera dados por 4 partições- IDs na gama <=20, [21, 50], [51, 80], e >=81, respectivamente. <br><br>Aqui estão mais consultas de amostra para diferentes cenários:<br> 1. Consulta de toda a tabela: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. Consulta a partir de uma tabela com seleção de colunas e filtros adicionais de cláusulas: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. Consulta com subqueries: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. Consulta com partição em subquery: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Melhores práticas para carregar dados com opção de partição:

1. Escolha uma coluna distinta como coluna de partição (como chave primária ou chave única) para evitar distorções de dados. 
2. Se a mesa tiver partição incorporada, utilize a opção de partição "Divisórias físicas da mesa" para obter um melhor desempenho.  
3. Se utilizar o tempo de execução da integração do Azure para copiar dados, pode definir "[Unidades de Integração de Dados (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) para utilizar mais recursos informáticos. Verifique os cenários aplicáveis.
4. "[Grau de paralelismo de cópia](copy-activity-performance-features.md#parallel-copy)" controla os números de partição, definindo este número demasiado grande em algum momento prejudica o desempenho, recomendando definir este número como (DIU ou número de nós de IR auto-hospedados) * (2 a 4).

**Exemplo: carga completa da mesa grande com divisórias físicas**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exemplo: consulta com partição de gama dinâmica**

```json
"source": {
    "type": "AzureSqlSource",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Melhores práticas para carregar dados na Base de Dados Azure SQL

Ao copiar dados na Base de Dados Azure SQL, poderá necessitar de diferentes comportamentos de escrita:

- [Apêndice:](#append-data)Os meus dados de origem têm apenas novos registos.
- [Upsert](#upsert-data): Os meus dados de origem têm inserções e atualizações.
- [Overwrite](#overwrite-the-entire-table): Quero recarregar uma tabela de dimensão inteira de cada vez.
- [Escreva com lógica personalizada](#write-data-with-custom-logic): Preciso de processamento extra antes da inserção final na tabela de destino.

Consulte as respetivas secções sobre como configurar na Azure Data Factory e as melhores práticas.

### <a name="append-data"></a>Dados do apêndice

Os dados appending são o comportamento padrão deste conector de pia Azure SQL Database. A Azure Data Factory faz uma inserção a granel para escrever na sua mesa de forma eficiente. Pode configurar a fonte e afundar-se em conformidade na atividade da cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção 1:** Quando tiver uma grande quantidade de dados para copiar, pode carregar todos os registos em massa numa tabela de encenação utilizando a atividade da cópia e, em seguida, executar uma atividade de procedimento armazenada para aplicar uma declaração [DE FUSÃO](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql) ou INSERT/UPDATE numa única tomada. 

Atualmente, a atividade de cópia não suporta o carregamento de dados numa tabela temporária de base de dados. Existe uma forma avançada de o configurar com uma combinação de múltiplas atividades, consulte os [cenários de Upsert de Base de Dados Azure SQL](https://github.com/scoriani/azuresqlbulkupsert). Abaixo mostra uma amostra de usar uma tabela permanente como encenação.

Como exemplo, na Azure Data Factory, pode criar um oleoduto com uma **atividade Copy** acorrentada com uma atividade de **Procedimento Armazenado.** Os primeiros copiam dados da sua loja de origem para uma tabela de posição Azure SQL Database, por exemplo, **UpsertStagingTable,** como o nome da tabela no conjunto de dados. Em seguida, este último invoca um procedimento armazenado para fundir dados de origem da tabela de paragem na tabela alvo e limpar a tabela de preparação.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Na sua base de dados, defina um procedimento armazenado com lógica MERGE, como o exemplo a seguir, que é apontado a partir da atividade de procedimento armazenado anterior. Assuma que o alvo é a tabela **de Marketing** com três colunas: **ProfileID,** **Estado**e **Categoria**. Faça o upsert com base na coluna **ProfileID.**

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

**Opção 3:** Pode utilizar [o Mapping Data Flow](#sink-transformation) que oferece métodos incorporados de inserção/upsert/atualização.

### <a name="overwrite-the-entire-table"></a>Sobrepor toda a mesa

Pode configurar a propriedade **pré-CopyScript** na pia da atividade de cópia. Neste caso, para cada atividade de cópia que executa, a Azure Data Factory executa primeiro o script. Em seguida, executa a cópia para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, especifique um script para primeiro eliminar todos os registos antes de carregar em massa os novos dados da fonte.

### <a name="write-data-with-custom-logic"></a>Escreva dados com lógica personalizada

Os passos para escrever dados com lógica personalizada são semelhantes aos descritos na secção [de dados upsert.](#upsert-data) Quando tiver de aplicar um tratamento extra antes da inserção final dos dados de origem na tabela de destino, pode carregar para uma tabela de preparação e, em seguida, invocar a atividade do procedimento armazenado, ou invocar um procedimento armazenado na pia da atividade de cópia para aplicar dados ou utilizar o Mapping Data Flow.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado a partir de um lavatório SQL

Ao copiar dados para a Base de Dados Azure SQL, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais em cada lote da tabela de origem. A função de procedimento armazenado tira partido dos [parâmetros valorizados da tabela](https://msdn.microsoft.com/library/bb675163.aspx).

Pode utilizar um procedimento armazenado quando os mecanismos de cópia incorporados não servem o propósito. Um exemplo é quando pretende aplicar um processamento extra antes da inserção final dos dados de origem na tabela de destino. Alguns exemplos de processamento extra são quando se pretende fundir colunas, procurar valores adicionais e inserir em mais de uma tabela.

A amostra que se segue mostra como utilizar um procedimento armazenado para fazer um upsert numa tabela na Base de Dados Azure SQL. Assuma que os dados de entrada e a tabela **de marketing** do lavatório têm três colunas: **ProfileID,** **Estado**e **Categoria**. Faça o upsert com base na coluna **ProfileID,** e aplique-o apenas para uma categoria específica chamada "ProductA".

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
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, pode ler e escrever para tabelas a partir da Base de Dados Azure SQL. Para obter mais informações, consulte a [transformação](data-flow-source.md) da fonte e [a transformação do sumidouro](data-flow-sink.md) nos fluxos de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

As definições específicas da Base de Dados Azure SQL estão disponíveis no separador **Opções De Origem** da transformação da fonte.

**Entrada:** Selecione se aponta a sua fonte para uma mesa (equivalente ```Select * from <table-name>``` a) ou introduza uma consulta SQL personalizada.

**Consulta**: Se selecionar Consulta no campo de entrada, introduza uma consulta SQL para a sua fonte. Esta definição substitui qualquer tabela que tenha escolhido no conjunto de dados. **As** cláusulas de encomenda por não são suportadas aqui, mas pode definir uma declaração completa SELECT FROM. Também pode utilizar funções de tabela definidas pelo utilizador. **selecionar * do udfGetData()** é um UDF em SQL que devolve uma tabela. Esta consulta produzirá uma tabela de origem que pode utilizar no fluxo de dados. Usar consultas também é uma ótima maneira de reduzir linhas para testes ou para procuras.

- Exemplo SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Tamanho do lote**: Introduza o tamanho do lote para colar dados grandes em leituras.

**Nível de isolamento**: O padrão para as fontes SQL no fluxo de dados de mapeamento é lido sem compromisso. Pode alterar o nível de isolamento aqui para um destes valores:

- Ler Comprometido
- Ler Não Comprometido
- Leitura repetível
- Serializável
- Nenhum (ignorar o nível de isolamento)

![Nível de Isolamento](media/data-flow/isolationlevel.png "Nível de Isolamento")

### <a name="sink-transformation"></a>Transformação do sumidouro

As definições específicas da Base de Dados Azure SQL estão disponíveis no **separador Definições** da transformação do lavatório.

**Método de atualização:** Determina quais as operações permitidas no seu destino de base de dados. O padrão é apenas permitir inserções. Para atualizar, intensificar ou apagar linhas, é necessária uma transformação de linhas alteradas para marcar linhas para essas ações. Para atualizações, atualizações e eliminações, deve ser definida uma coluna ou colunas-chave para determinar qual a linha a alterar.

![Colunas-chave](media/data-flow/keycolumn.png "Colunas-chave")

O nome da coluna que escolher como chave aqui será usado pela ADF como parte da atualização subsequente, upsert, apagar. Portanto, deve escolher uma coluna que exista no mapeamento da Pia. Se não pretender não escrever o valor para esta coluna-chave, clique em "Saltar colunas-chave de escrita".

Pode parametrizar a coluna-chave utilizada aqui para atualizar a tabela de base de dados Azure SQL. Se tiver várias colunas para uma chave composta, clique em "Expressão Personalizada" e poderá adicionar conteúdo dinâmico utilizando a linguagem de expressão de fluxo de dados ADF, que pode incluir uma série de cordas com nomes de colunas para uma chave composta.

**Ação da tabela:** Determina se deve recriar ou remover todas as linhas da tabela de destino antes de escrever.

- Nenhuma: nenhuma ação será feita à mesa.
- Recriar: A mesa será largada e recriada. Necessário se criar uma nova tabela dinamicamente.
- Truncato: Todas as linhas da mesa-alvo serão removidas.

**Tamanho do lote**: Controla quantas linhas estão a ser escritas em cada balde. Tamanhos maiores do lote melhoram a compressão e a otimização da memória, mas arriscam-se a sair das exceções de memória ao caching dados.

**Scripts pré e post SQL**: Introduza scripts SQL de várias linhas que serão executados antes (pré-processamento) e depois (pós-processamento) dados são escritos na sua base de dados de Sink

![scripts de processamento pré e pós SQL](media/data-flow/prepost1.png "Scripts de processamento SQL")

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapeamento do tipo de dados para Azure SQL Database

Quando os dados são copiados de ou para Azure SQL Database, os seguintes mapeamentos são usados desde os tipos de dados da Base de Dados Azure SQL até aos tipos de dados provisórios da Azure Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [o Schema e os mapeamentos do tipo de dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados da base de dados Azure SQL | Tipo de dados provisórios da Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| date |DateTime |
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
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinário |Byte[] |
| varchar |String, Char[] |
| xml |String |

>[!NOTE]
> Para tipos de dados que mapeiam para o tipo decimal provisório, atualmente a atividade Copy suporta precisão até 28. Se tiver dados com precisão superior a 28, considere converter-se a uma cadeia em consulta SQL.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade getMetadata

Para saber mais detalhes sobre as propriedades, consulte a [atividade da GetMetadata](control-flow-get-metadata-activity.md)

## <a name="using-always-encrypted"></a>Usando sempre encriptado

Quando copiar dados de/para Azure SQL Database com [Always Encrypted,](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)utilize [o conector ODBC genérico](connector-odbc.md) e o controlador ODBC do Servidor SQL através do tempo de execução de integração auto-hospedado. Este conector Azure SQL Database não suporta sempre encriptado. 

Mais especificamente:

1. Crie um tempo de integração auto-hospedado se não tiver um. Consulte o artigo [de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.

2. Descarregue o controlador ODBC de 64 bits para o SQL Server a partir [daqui](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)e instale na máquina de tempo de execução de integração. Saiba mais sobre como este condutor funciona a partir da [utilização sempre encriptada com o controlador ODBC para o SQL Server](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Crie um serviço ligado com o tipo ODBC para ligar à sua base de dados SQL, consulte as seguintes amostras:

    - Para utilizar **a autenticação SQL**: Especifique a cadeia de ligação ODBC como abaixo e selecione a autenticação **básica** para definir o nome de utilizador e a palavra-passe.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Para utilizar a **autenticação de identidade gerida pela fábrica de dados:** 

        1. Siga os [mesmos pré-requisitos](#managed-identity) para criar o utilizador de base de dados para a identidade gerida e conceder o papel adequado na sua base de dados.
        2. No serviço ligado, especifique a cadeia de ligação ODBC como abaixo e selecione a autenticação **anónima** como a própria cadeia de ligação indica `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Crie conjunto de dados e copie a atividade com o tipo ODBC em conformidade. Saiba mais a partir do artigo do [conector ODBC.](connector-odbc.md)

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
