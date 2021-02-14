---
title: Copiar e transformar dados em Azure Synapse Analytics
description: Aprenda a copiar dados de e para a Azure Synapse Analytics e transforme dados em Azure Synapse Analytics utilizando a Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 38306b2fb3c0a51aeedbf1ebd9079dd787783093
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364295"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-by-using-azure-data-factory"></a>Copiar e transformar dados em Azure Synapse Analytics utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
>
> - [Versão1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [Versão atual](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Copy Activity in Azure Data Factory para copiar dados de e para Azure Synapse Analytics, e usar o Data Flow para transformar dados em Azure Data Lake Storage Gen2. Para saber mais sobre a Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Synapse Analytics é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com tabela [de matriz de fonte/pia suportada](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)

Para a atividade copy, este conector Azure Synapse Analytics suporta estas funções:

- Copie os dados utilizando a autenticação SQL e o Azure Ative Directory (Azure AD) A autenticação de ficha de aplicação com um principal serviço ou identidades geridas para recursos Azure.
- Como fonte, recupere os dados utilizando uma consulta SQL ou procedimento armazenado. Também pode optar por copiar paralelamente a partir de uma fonte Azure Synapse Analytics, consulte a cópia paralela da secção [Azure Synapse Analytics](#parallel-copy-from-azure-synapse-analytics) para obter mais detalhes.
- Como pia, carregue os dados utilizando a declaração [PolyBase](#use-polybase-to-load-data-into-azure-synapse-analytics) ou [COPY](#use-copy-statement) ou a partir de cada um. Recomendamos a declaração polyBase ou COPY para um melhor desempenho da cópia. O conector também suporta automaticamente a criação de uma tabela de destino se não existir com base no esquema de origem.

> [!IMPORTANT]
> Se copiar dados utilizando o tempo de execução da integração da Azure Data Factory, configure uma [regra de firewall ao nível do servidor](../azure-sql/database/firewall-configure.md) para que os serviços Azure possam aceder ao servidor [lógico SQL](../azure-sql/database/logical-servers.md).
> Se copiar dados utilizando um tempo de integração auto-hospedado, configufique a firewall para permitir o intervalo ip apropriado. Esta gama inclui o IP da máquina que é usado para ligar ao Azure Synapse Analytics.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter o melhor desempenho, utilize a declaração PolyBase ou COPY para carregar dados no Azure Synapse Analytics. A [Utilização da PolyBase para carregar dados em Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) e usar a declaração COPY para carregar dados nas secções [Azure Synapse Analytics](#use-copy-statement) tem detalhes. Para uma passagem com uma caixa de utilização, consulte [a Carga 1 TB em Azure Synapse Analytics em menos de 15 minutos com a Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que definem entidades da Data Factory específicas de um conector Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para um serviço Azure Synapse Analytics ligado:

| Propriedade            | Descrição                                                  | Obrigatório                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| tipo                | A propriedade tipo deve ser definida para **AzureSqlDW**.             | Yes                                                          |
| conexãoStragem    | Especifique as informações necessárias para ligar à instância Azure Synapse Analytics para a **propriedade connectionString.** <br/>Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory. Também pode colocar a chave principal de senha/serviço no Cofre da Chave Azure, e se for a autenticação SQL retire a `password` configuração da cadeia de ligação. Veja o exemplo JSON abaixo da tabela e [guarde as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Yes                                                          |
| servicePrincipalId  | Especifique a identificação do cliente da aplicação.                         | Sim, quando utilizar a autenticação AZure AD com um principal de serviço. |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim, quando utilizar a autenticação AZure AD com um principal de serviço. |
| inquilino              | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Sim, quando utilizar a autenticação AZure AD com um principal de serviço. |
| AzureCloudType | Para a autenticação principal do serviço, especifique o tipo de ambiente em nuvem Azure para o qual a sua aplicação AD AZure está registada. <br/> Os valores permitidos `AzurePublic` `AzureChina` `AzureUsGovernment` são, `AzureGermany` e. Por padrão, o ambiente em nuvem da fábrica de dados é utilizado. | No |
| connectVia          | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou um tempo de integração auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. | No                                                           |

Para diferentes tipos de autenticação, consulte as seguintes secções sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação SQL](#sql-authentication)
- Autenticação simbólica de aplicação Azure: Diretor [de serviço](#service-principal-authentication)
- Autenticação simbólica de aplicação Azure: [Identidades geridas para recursos Azure](#managed-identity)

>[!TIP]
>Ao criar o serviço ligado para a piscina SQL **sem servidor** Azure Synapse da UI, escolha "insira manualmente" em vez de navegar a partir da subscrição.

>[!TIP]
>Se atingir o erro com código de erro como "UserErrorFailedToConnectToSqlServer" e uma mensagem como "O limite de sessão para a base de dados é XXX e foi atingido.", adicione `Pooling=false` a sua cadeia de ligação e tente novamente.

### <a name="sql-authentication"></a>Autenticação do SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemplo de serviço ligado que utiliza a autenticação SQL

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Senha no cofre da chave Azure:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

Para utilizar a autenticação de fichas Azure AD baseadas em serviços, siga estes passos:

1. **[Crie uma aplicação Azure Ative Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** a partir do portal Azure. Tome nota do nome da aplicação e dos seguintes valores que definem o serviço ligado:

   - ID da Aplicação
   - Chave de aplicação
   - ID do inquilino

2. **[Forcê um administrador do Azure Ative Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** para o seu servidor no portal Azure se ainda não o fez. O administrador AD Azure pode ser um utilizador AD Azure ou grupo AZure AD. Se conceder ao grupo com identidade gerida um papel de administrador, salte os passos 3 e 4. O administrador terá acesso total à base de dados.

3. **[Criar utilizadores de bases de dados contidos](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** para o principal serviço. Conecte-se ao armazém de dados a partir ou ao qual pretende copiar dados utilizando ferramentas como sSMS, com uma identidade AD Azure que tem pelo menos qualquer permissão do UTILIZADOR. Executar o seguinte T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Conceda ao chefe de serviço permissões necessárias,** como normalmente faz para utilizadores sql ou outros. Executar o seguinte código, ou consulte mais opções [aqui](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Se pretender utilizar o PolyBase para carregar os dados, saiba qual é a permissão necessária para a [base de dados](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Configure um serviço Azure Synapse Analytics ligado na** Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço ligado que utiliza a autenticação principal do serviço

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos da Azure](data-factory-service-identity.md) que representa a fábrica específica. Pode utilizar esta identidade gerida para a autenticação Azure Synapse Analytics. A fábrica designada pode aceder e copiar dados de ou para o seu armazém de dados utilizando esta identidade.

Para utilizar a autenticação de identidade gerida, siga estes passos:

1. **[Forcê um administrador do Azure Ative Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** para o seu servidor no portal Azure se ainda não o fez. O administrador AD Azure pode ser um utilizador AD Azure ou grupo AZure AD. Se conceder ao grupo com identidade gerida um papel de administrador, salte os passos 3 e 4. O administrador terá acesso total à base de dados.

2. **[Criar utilizadores de bases de dados contidos](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** para a Identidade Gerida pela Fábrica de Dados. Conecte-se ao armazém de dados a partir ou ao qual pretende copiar dados utilizando ferramentas como sSMS, com uma identidade AD Azure que tem pelo menos qualquer permissão do UTILIZADOR. Executar o seguinte T-SQL.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Conceder à Data Factory Managed Identity permissões necessárias,** como normalmente faz para utilizadores de SQL e outros. Executar o seguinte código, ou consulte mais opções [aqui](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Se pretender utilizar o PolyBase para carregar os dados, saiba qual é a permissão necessária para a [base de dados](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. **Configure um serviço Azure Synapse Analytics ligado na** Azure Data Factory.

**Exemplo:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md)

As seguintes propriedades são suportadas para o conjunto de dados Azure Synapse Analytics:

| Propriedade  | Descrição                                                  | Obrigatório                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| tipo      | A propriedade **do tipo** do conjunto de dados deve ser definida para **AzureSqlDWTable**. | Yes                         |
| esquema | O nome do esquema. |Não para a fonte, sim para a pia  |
| table | Nome da mesa/vista. |Não para a fonte, sim para a pia  |
| tableName | Nome da tabela/vista com esquema. Esta propriedade é suportada para retrocompatibilidade. Para nova carga de trabalho, use `schema` e `table` . | Não para a fonte, sim para a pia |

### <a name="dataset-properties-example"></a>Exemplo de propriedades de dataset

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

## <a name="copy-activity-properties"></a>Propriedades de Atividade de Cópia

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte e pia Azure Synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics como a fonte

>[!TIP]
>Para carregar os dados da Azure Synapse Analytics de forma eficiente utilizando a partição de dados, saiba mais com [cópia paralela da Azure Synapse Analytics](#parallel-copy-from-azure-synapse-analytics).

Para copiar dados da Azure Synapse Analytics, decreva a propriedade **tipo** na fonte de Atividade de Cópia para **SqlDWSource**. As seguintes propriedades são suportadas na secção **origem** da Atividade de Cópia:

| Propriedade                     | Descrição                                                  | Obrigatório |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| tipo                         | A propriedade **tipo** da fonte de Atividade de Cópia deve ser definida como **SqlDWSource**. | Yes      |
| sqlReaderQuery               | Utilize a consulta SQL personalizada para ler dados. Exemplo: `select * from MyTable`. | No       |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê os dados da tabela de origem. A última declaração SQL deve ser uma declaração SELECT no procedimento armazenado. | No       |
| parametrómetros de reserva armazenados    | Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são pares de nomes ou valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. | No       |
| isolamentoLevel | Especifica o comportamento de bloqueio de transação para a fonte SQL. Os valores permitidos são: **ReadCommitted,** **ReadUncommitted,** **RepeatableRead,** **Serializable**, **Snapshot**. Se não for especificado, é utilizado o nível de isolamento predefinido da base de dados. Para obter mais informações, consulte [system.data.isolationlevel](/dotnet/api/system.data.isolationlevel). | No |
| partitionOptions | Especifica as opções de partição de dados utilizadas para carregar dados da Azure Synapse Analytics. <br>Os valores permitidos são: **Nenhum** (padrão), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando uma opção de partição é ativada (isto é, `None` não), o grau de paralelismo para carregar simultaneamente dados de um Azure Synapse Analytics é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) definição na atividade da cópia. | No |
| divisóriasSas | Especificar o grupo das definições para a partilha de dados. <br>Aplicar quando a opção de partição não `None` estiver. | No |
| ***Em `partitionSettings` :*** | | |
| partitionColumnName | Especificar o nome da coluna de origem **no tipo inteiro ou data/data** ( , , , , , `int` , , ou ) que será utilizado `smallint` por `bigint` `date` `smalldatetime` `datetime` `datetime2` `datetimeoffset` divisórias de alcance para cópia paralela. Se não for especificado, o índice ou a chave primária da tabela é detetado automaticamente e utilizado como coluna de partição.<br>Aplicar quando a opção de partição for `DynamicRange` . Se utilizar uma consulta para recuperar os dados de origem,  `?AdfDynamicRangePartitionCondition ` ligue-se à cláusula WHERE. Por exemplo, consulte a cópia paralela da secção [de base de dados SQL.](#parallel-copy-from-azure-synapse-analytics) | No |
| partitionUpperBound | O valor máximo da coluna de partição para a divisão do intervalo de partição. Este valor é usado para decidir o passo de partição, não para filtrar as linhas na mesa. Todas as linhas da tabela ou resultado de consulta serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta o valor.  <br>Aplicar quando a opção de partição for `DynamicRange` . Por exemplo, consulte a cópia paralela da secção [de base de dados SQL.](#parallel-copy-from-azure-synapse-analytics) | No |
| partitionLowerBound | O valor mínimo da coluna de partição para a divisão do intervalo de divisão. Este valor é usado para decidir o passo de partição, não para filtrar as linhas na mesa. Todas as linhas da tabela ou resultado de consulta serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta o valor.<br>Aplicar quando a opção de partição for `DynamicRange` . Por exemplo, consulte a cópia paralela da secção [de base de dados SQL.](#parallel-copy-from-azure-synapse-analytics) | No |

**Note o seguinte ponto:**

- Ao utilizar o procedimento armazenado na fonte para obter dados, note se o seu procedimento armazenado for concebido como devolvendo esquemas diferentes quando o valor de parâmetro diferente for passado, poderá encontrar falhas ou ver resultados inesperados ao importar esquemas de UI ou ao copiar dados para base de dados SQL com criação de quadros automáticos.

#### <a name="example-using-sql-query"></a>Exemplo: utilização da consulta SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="example-using-stored-procedure"></a>Exemplo: utilização do procedimento armazenado

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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
                "type": "SqlDWSource",
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

#### <a name="sample-stored-procedure"></a>Procedimento de amostragem armazenada:

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> Azure Synapse Analytics como pia

A Azure Data Factory suporta três formas de carregar dados em Azure Synapse Analytics.

![Azure Synapse Analytics opções de cópia de pia](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Use PolyBase](#use-polybase-to-load-data-into-azure-synapse-analytics)
- [Use a declaração COPY](#use-copy-statement)
- Use inserção a granel

A forma mais rápida e escalável de carregar dados é através da [PolyBase](/sql/relational-databases/polybase/polybase-guide) ou da [declaração COPY](/sql/t-sql/statements/copy-into-transact-sql).

Para copiar os dados para a Azure Synapse Analytics, descreva o tipo de pia em Atividade de Cópia para **SqlDWSink**. As seguintes propriedades são suportadas na secção de **lavatório** Copy Activity:

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| tipo              | A propriedade **tipo** do lavatório Copy Activity deve ser definida como **SqlDWSink**. | Yes                                           |
| permitir A Base DePoly     | Indica se deve utilizar o PolyBase para carregar dados no Azure Synapse Analytics. `allowCopyCommand` e `allowPolyBase` não pode ser ambos verdadeiros. <br/><br/>Consulte [o PolyBase para carregar dados na secção Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) para obter constrangimentos e detalhes.<br/><br/>Os valores permitidos são **verdadeiros** e **falsos** (padrão). | N.º<br/>Aplicar quando utilizar o PolyBase.     |
| poliBaseSettings  | Um grupo de propriedades que podem ser especificadas quando a `allowPolybase` propriedade é definida como **verdadeira.** | N.º<br/>Aplicar quando utilizar o PolyBase. |
| permitirCopyCommand | Indica se deve utilizar a [declaração COPY](/sql/t-sql/statements/copy-into-transact-sql) para carregar dados no Azure Synapse Analytics. `allowCopyCommand` e `allowPolyBase` não pode ser ambos verdadeiros. <br/><br/>Consulte a declaração copy para carregar dados na secção [Azure Synapse Analytics](#use-copy-statement) para obter constrangimentos e detalhes.<br/><br/>Os valores permitidos são **verdadeiros** e **falsos** (padrão). | N.º<br>Aplicar ao utilizar COPY. |
| copyCommandSettings | Um grupo de propriedades que podem ser especificadas quando `allowCopyCommand` a propriedade é definida para TRUE. | N.º<br/>Aplicar ao utilizar COPY. |
| escreverBatchSize    | Número de linhas para inserir na tabela SQL **por lote**.<br/><br/>O valor permitido é **inteiro (número** de linhas). Por predefinição, a Data Factory determina dinamicamente o tamanho apropriado do lote com base no tamanho da linha. | N.º<br/>Aplicar quando utilizar a inserção a granel.     |
| escreverBatchTimeout | Aguarde o tempo para que a operação de inserção do lote termine antes de se esgotar.<br/><br/>O valor permitido é **o tempo.** Exemplo: "00:30:00" (30 minutos). | N.º<br/>Aplicar quando utilizar a inserção a granel.        |
| preCopyScript     | Especifique uma consulta SQL para a Copy Activity para executar antes de escrever dados em Azure Synapse Analytics em cada execução. Utilize esta propriedade para limpar os dados pré-carregados. | No                                            |
| mesaOption | Especifica se deve [criar automaticamente a tabela do lavatório](copy-activity-overview.md#auto-create-sink-tables) se não existir com base no esquema de origem. Os valores permitidos são: `none` (padrão), `autoCreate` . |No |
| desativaçãoMetricosCollecto | A Data Factory recolhe métricas como Azure Synapse Analytics DWUs para otimização de desempenho de cópia e recomendações, que introduzem acesso adicional ao DB principal. Se estiver preocupado com este comportamento, especifique `true` para desligá-lo. | Não (o padrão `false` é) |

#### <a name="azure-synapse-analytics-sink-example"></a>Exemplo de pia Azure Synapse Analytics

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="parallel-copy-from-azure-synapse-analytics"></a>Cópia paralela da Azure Synapse Analytics

O conector Azure Synapse Analytics na atividade de cópia fornece partição de dados incorporada para copiar dados em paralelo. Pode encontrar opções de partição de dados no separador **'Fonte'** da atividade da cópia.

![Screenshot das opções de partição](./media/connector-sql-server/connector-sql-partition-options.png)

Quando ativa a cópia dividida, a atividade de cópia executa consultas paralelas contra a sua fonte Azure Synapse Analytics para carregar dados por partições. O grau paralelo é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) regulação da atividade da cópia. Por exemplo, se definir `parallelCopies` para quatro, data factory simultaneamente gera e executa quatro consultas com base na sua opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados do Azure Synapse Analytics.

Sugere-se que ative uma cópia paralela com a partilha de dados, especialmente quando carrega uma grande quantidade de dados do seu Azure Synapse Analytics. São sugeridas configurações para diferentes cenários. Ao copiar dados na loja de dados baseada em ficheiros, recomenda-se escrever para uma pasta como vários ficheiros (especificar apenas o nome da pasta), caso em que o desempenho é melhor do que escrever num único ficheiro.

| Scenario                                                     | Definições sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa da mesa grande, com divisórias físicas.        | **Opção de partição**: Divisórias físicas da tabela. <br><br/>Durante a execução, a Data Factory deteta automaticamente as divisórias físicas e copia dados por partições. <br><br/>Para verificar se a sua mesa tem ou não partição física, pode consultar [esta consulta](#sample-query-to-check-physical-partition). |
| Carga completa da mesa grande, sem divisórias físicas, enquanto com uma coluna de inteiro ou data para partição de dados. | **Opções de partição**: Partição dinâmica do alcance.<br>**Coluna de partição** (opcional): Especificar a coluna utilizada para os dados de partição. Se não for especificado, utiliza-se o índice ou a coluna-chave primária.<br/>**Limite superior da partição** e **divisória inferior** (opcional): Especifique se pretende determinar o passo de partição. Isto não é para filtrar as linhas na mesa, todas as linhas na mesa serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta automaticamente os valores.<br><br>Por exemplo, se a sua coluna de partição "ID" tiver valores que variam entre 1 e 100, e definir o limite inferior como 20 e o limite superior como 80, com cópia paralela como 4, Data Factory recupera dados por 4 partições - IDs na gama <=20, [21, 50], [51, 80], e >=81, respectivamente. |
| Carregue uma grande quantidade de dados utilizando uma consulta personalizada, sem divisórias físicas, enquanto com uma coluna inteiro ou data/data para a partilha de dados. | **Opções de partição**: Partição dinâmica do alcance.<br>**Consulta:** `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Coluna de partição**: Especificar a coluna utilizada para os dados de partição.<br>**Limite superior da partição** e **divisória inferior** (opcional): Especifique se pretende determinar o passo de partição. Isto não é para filtrar as linhas na mesa, todas as linhas no resultado da consulta serão divididas e copiadas. Se não for especificado, a atividade de cópia deteta o valor.<br><br>Durante a execução, a Data Factory `?AdfRangePartitionColumnName` substitui-se pelo nome real da coluna e gamas de valor para cada partição, e envia para a Azure Synapse Analytics. <br>Por exemplo, se a sua coluna de partição "ID" tiver valores que variam entre 1 e 100, e definir o limite inferior como 20 e o limite superior como 80, com cópia paralela como 4, Data Factory recupera dados por 4 partições- IDs na gama <=20, [21, 50], [51, 80], e >=81, respectivamente. <br><br>Aqui estão mais consultas de amostra para diferentes cenários:<br> 1. Consulta de toda a tabela: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. Consulta a partir de uma tabela com seleção de colunas e filtros adicionais de cláusulas: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. Consulta com subqueries: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. Consulta com partição em subquery: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Melhores práticas para carregar dados com opção de partição:

1. Escolha uma coluna distinta como coluna de partição (como chave primária ou chave única) para evitar distorções de dados. 
2. Se a mesa tiver partição incorporada, utilize a opção de partição "Divisórias físicas da mesa" para obter um melhor desempenho.
3. Se utilizar o tempo de execução da integração do Azure para copiar dados, pode definir "[Unidades de Integração de Dados (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) para utilizar mais recursos informáticos. Verifique os cenários aplicáveis.
4. "[Grau de paralelismo de cópia](copy-activity-performance-features.md#parallel-copy)" controla os números de partição, definindo este número demasiado grande em algum momento prejudica o desempenho, recomendando definir este número como (DIU ou número de nós de IR auto-hospedados) * (2 a 4).
5. Nota Azure Synapse Analytics pode executar um máximo de 32 consultas de cada vez, definindo "Grau de paralelismo de cópia" demasiado grande pode causar problemas de estrangulamento da Sinapse.

**Exemplo: carga completa da mesa grande com divisórias físicas**

```json
"source": {
    "type": "SqlDWSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exemplo: consulta com partição de gama dinâmica**

```json
"source": {
    "type": "SqlDWSource",
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
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, c.name AS ColumnName, CASE WHEN c.name IS NULL THEN 'no' ELSE 'yes' END AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id
LEFT JOIN sys.types AS y ON c.system_type_id = y.system_type_id
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Se a mesa tiver partição física, veria "HasPartition" como "sim".

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Utilize a PolyBase para carregar dados em Azure Synapse Analytics

A utilização do [PolyBase](/sql/relational-databases/polybase/polybase-guide) é uma forma eficiente de carregar uma grande quantidade de dados no Azure Synapse Analytics com alta produção. Verá um grande ganho na produção utilizando o PolyBase em vez do mecanismo padrão BULKINSERT. Para uma passagem com uma caixa de utilização, consulte [a Carga 1 TB em Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

- Se os seus dados de origem estiverem em **Azure Blob, Azure Data Lake Storage Gen1 ou Azure Data Lake Storage Gen2**, e o **formato é compatível com PolyBase,** pode utilizar a atividade de cópia para invocar diretamente a PolyBase para permitir que o Azure Synapse Analytics retire os dados da fonte. Para mais informações, consulte **[a cópia direta utilizando a PolyBase](#direct-copy-by-using-polybase)**.
- Se a sua loja de dados de origem e o seu formato não forem originalmente suportados pela PolyBase, utilize a cópia Encenada utilizando a funcionalidade **[PolyBase.](#staged-copy-by-using-polybase)** A funcionalidade de cópia encenada também lhe proporciona uma melhor produção. Converte automaticamente os dados em formato compatível com a PolyBase, armazena os dados no armazenamento Azure Blob e, em seguida, chama a PolyBase para carregar dados em Azure Synapse Analytics.

> [!TIP]
> Saiba mais sobre [as melhores práticas para a utilização do PolyBase.](#best-practices-for-using-polybase) Ao utilizar a PolyBase com O Tempo de Execução da Integração Azure, as unidades eficazes de integração de [dados (DIU)](copy-activity-performance-features.md#data-integration-units) para armazenamento direto ou encenado para sinapse são sempre 2. A sintonização do DIU não afeta o desempenho, uma vez que os dados de carregamento do armazenamento são alimentados pelo motor Synapse.

As seguintes definições de PolyBase são suportadas `polyBaseSettings` na atividade de cópia:

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejeitarValue       | Especifica o número ou percentagem de linhas que podem ser rejeitadas antes da consulta falhar.<br/><br/>Saiba mais sobre as opções de rejeição da PolyBase na secção de Argumentos da [TABELA EXTERNA CREATE (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql). <br/><br/>Os valores permitidos são 0 (padrão), 1, 2, etc. | No                                            |
| rejeitarType        | Especifica se a opção **rejeitar o Valor** é um valor literal ou uma percentagem.<br/><br/>Os valores permitidos são **Valor** (padrão) e **Percentagem**. | No                                            |
| rejeitarSampleValue | Determina o número de linhas a recuperar antes que a PolyBase recalcule a percentagem de linhas rejeitadas.<br/><br/>Os valores permitidos são 1, 2, etc. | Sim, se o **Tipo rejeitado** for **percentagem.** |
| utilizarTypeDefault    | Especifica como lidar com valores em falta em ficheiros de texto delimitados quando o PolyBase recupera dados do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade a partir da secção Argumentos em [FORMATO DE FICHEIRO EXTERNO (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql).<br/><br/>Os valores permitidos são **verdadeiros** e **falsos** (padrão).<br><br> | No                                            |

### <a name="direct-copy-by-using-polybase"></a>Cópia direta utilizando o PolyBase

A azure Synapse Analytics PolyBase suporta diretamente Azure Blob, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2. Se os seus dados de origem satisfaçam os critérios descritos nesta secção, utilize a PolyBase para copiar diretamente da loja de dados de origem para a Azure Synapse Analytics. Caso contrário, utilize [cópia encenada utilizando a PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Para copiar dados de forma eficiente para a Azure Synapse Analytics, saiba mais com [a Azure Data Factory torna ainda mais fácil e conveniente descobrir insights a partir de dados ao utilizar a Data Lake Store com a Azure Synapse Analytics.](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse)

Se os requisitos não forem cumpridos, a Azure Data Factory verifica as definições e volta automaticamente ao mecanismo BULKINSERT para o movimento de dados.

1. O **serviço ligado à fonte** encontra-se com os seguintes tipos e métodos de autenticação:

    | Tipo de loja de dados de origem suportada                             | Tipo de autenticação de origem suportada                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | Autenticação chave de conta, autenticação de identidade gerida |
    | [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md) | Autenticação do principal de serviço                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2) | Autenticação chave de conta, autenticação de identidade gerida |

    >[!IMPORTANT]
    >- Quando utilizar a autenticação de identidade gerida para o seu serviço ligado ao armazenamento, aprenda as configurações necessárias para [Azure Blob](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md#managed-identity) respectivamente.
    >- Se o seu Azure Storage estiver configurado com o ponto final do serviço VNet, deve utilizar a autenticação de identidade gerida com "permitir um serviço Microsoft confiável" ativado na conta de armazenamento, consulte o [Impacto da utilização de Pontos Finais do Serviço VNet com armazenamento Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

2. O formato de dados de **origem** é de **Parquet,** **ORC,** ou **texto delimitado,** com as seguintes configurações:

   1. O caminho da pasta não contém filtro wildcard.
   2. O nome do ficheiro está vazio ou aponta para um único ficheiro. Se especificar o nome do ficheiro wildcard na atividade da cópia, só pode ser `*` ou `*.*` .
   3. `rowDelimiter` é **padrão**, **\n**, **\r\n,** ou **\r**.
   4. `nullValue` é deixado como padrão ou definido para **cadeia vazia** (""), e `treatEmptyAsNull` é deixado como padrão ou definido para verdadeiro.
   5. `encodingName` é deixado como padrão ou definido para **utf-8**.
   6. `quoteChar`E `escapeChar` `skipLineCount` não estão especificados. Linha de suporte polyBase salte a linha do cabeçalho, que pode ser configurada como `firstRowAsHeader` em ADF.
   7. `compression` não pode ser **compressões,** **GZip,** ou **Deflate**.

3. Se a sua fonte for uma pasta, `recursive` a atividade de cópia deve ser definida como verdadeira.

4. `wildcardFolderPath` E `wildcardFilename` `modifiedDateTimeStart` `modifiedDateTimeEnd` não `prefix` `enablePartitionDiscovery` `additionalColumns` estão especificados.

>[!NOTE]
>Se a sua fonte for uma pasta, note que a PolyBase recupera ficheiros da pasta e de todas as suas sub-dobradeiras, e não obtém dados de ficheiros para os quais o nome do ficheiro começa com um sublinhado (_) ou um período (.), como documentado [aqui - argumento de LOCALIZAÇÃO](/sql/t-sql/statements/create-external-table-transact-sql#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Cópia encenada usando PolyBase

Quando os seus dados de origem não são de forma nativa compatível com a PolyBase, ative a cópia de dados através de uma encenação provisória Azure Blob ou Azure Data Lake Storage Gen2 (não pode ser Azure Premium Storage). Neste caso, a Azure Data Factory converte automaticamente os dados para satisfazer os requisitos do formato de dados da PolyBase. Em seguida, invoca a PolyBase para carregar dados no Azure Synapse Analytics. Finalmente, limpa os seus dados temporários do armazenamento. Consulte [a cópia encenada](copy-activity-performance-features.md#staged-copy) para obter mais detalhes sobre a cópia dos dados através de uma encenação.

Para utilizar esta funcionalidade, crie um [serviço ligado ao Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) ou serviço de armazenamento de [dados Azure Data Lake Gen2](connector-azure-data-lake-storage.md#linked-service-properties) com **chave de conta ou autenticação de identidade gerida** que se refere à conta de armazenamento Azure como armazenamento provisório.

>[!IMPORTANT]
>- Quando utilizar a autenticação de identidade gerida para o seu serviço ligado à encenação, aprenda as configurações necessárias para [Azure Blob](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md#managed-identity) respectivamente.
>- Se a sua encenação Azure Storage estiver configurada com o ponto final do serviço VNet, deve utilizar a autenticação de identidade gerida com "permitir um serviço Microsoft confiável" ativado na conta de armazenamento, consulte o [Impacto da utilização de Pontos Finais do Serviço VNet com armazenamento Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). 

>[!IMPORTANT]
>Se a sua encenação Azure Storage estiver configurada com o Managed Private Endpoint e tiver a firewall de armazenamento ativada, deve utilizar a autenticação de identidade gerida e conceder permissões de Armazenamento Blob Data Reader ao Sinapse SQL Server para garantir que pode aceder aos ficheiros encenados durante a carga PolyBase.

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Melhores práticas para utilizar o PolyBase

As seguintes secções proporcionam as melhores práticas para além das práticas mencionadas nas [melhores práticas para o Azure Synapse Analytics](../synapse-analytics/sql/best-practices-sql-pool.md).

#### <a name="required-database-permission"></a>Autorização de base de dados necessária

Para utilizar o PolyBase, o utilizador que carrega dados no Azure Synapse Analytics deve ter [permissão "CONTROL"](/sql/relational-databases/security/permissions-database-engine) na base de dados-alvo. Uma forma de o conseguir é adicionar o utilizador como membro do **db_owner** papel. Saiba como fazê-lo na visão geral do [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Tamanho da linha e limites do tipo de dados

As cargas PolyBase limitam-se a filas inferiores a 1 MB. Não pode ser utilizado para carregar para VARCHR (MAX), NVARCHAR (MAX) ou VARBINARY (MAX). Para mais informações, consulte [os limites de capacidade de serviço da Azure Synapse Analytics.](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)

Quando os dados de origem têm linhas superiores a 1 MB, é melhor dividir verticalmente as tabelas de origem em várias pequenas. Certifique-se de que o maior tamanho de cada linha não excede o limite. As tabelas mais pequenas podem então ser carregadas utilizando a PolyBase e fundidas em Azure Synapse Analytics.

Em alternativa, para os dados com colunas tão largas, pode utilizar a não-Base PoliBase para carregar os dados utilizando o ADF, desligando a definição "permitir a PoliBase".

#### <a name="azure-synapse-analytics-resource-class"></a>Classe de recursos Azure Synapse Analytics

Para obter o melhor rendimento possível, atribua uma classe de recursos maior ao utilizador que carrega dados em Azure Synapse Analytics via PolyBase.

#### <a name="polybase-troubleshooting"></a>Resolução de problemas da PolyBase

#### <a name="loading-to-decimal-column"></a>Carregamento para coluna decimal

Se os seus dados de origem estiverem em formato de texto ou noutras lojas compatíveis com a PolyBase (utilizando cópia encenada e PolyBase), e contiver valor vazio para ser carregado na coluna Decimal Azure Synapse Analytics, poderá obter o seguinte erro:

```output
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A solução é desmarcar a opção "**Use type default**" (como falso) na atividade de cópia -> as definições de PolyBase. "[USE_TYPE_DEFAULT](/sql/t-sql/statements/create-external-file-format-transact-sql#arguments)" é uma configuração nativa da PolyBase, que especifica como lidar com valores em falta em ficheiros de texto delimitados quando o PolyBase recupera dados do ficheiro de texto.

#### <a name="check-the-tablename-property-in-azure-synapse-analytics"></a>Consulte a propriedade tableName em Azure Synapse Analytics

A tabela seguinte dá exemplos de como especificar a propriedade **tableName** no conjunto de dados JSON. Mostra várias combinações de esquemas e nomes de mesa.

| DB Schema | Nome da tabela | **nome de mesa** Propriedade JSON               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable ou dbo. MyTable ou [dbo]. [MyTable] |
| dbo1      | MyTable    | dbo1. MyTable ou [dbo1]. [MyTable]          |
| dbo       | A minha.mesa   | [My.Table] ou [dbo]. [Minha.Mesa]            |
| dbo1      | A minha.mesa   | [dbo1]. [Minha.Mesa]                         |

Se vir o seguinte erro, o problema pode ser o valor especificado para a **propriedade tableName.** Consulte a tabela anterior para obter a forma correta de especificar valores para a propriedade JSON de nome de **mesa.**

```output
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

#### <a name="columns-with-default-values"></a>Colunas com valores predefinidos

Atualmente, a funcionalidade PolyBase na Data Factory aceita apenas o mesmo número de colunas que na tabela-alvo. Um exemplo é uma tabela com quatro colunas onde uma delas é definida com um valor padrão. Os dados de entrada ainda precisam de ter quatro colunas. Um conjunto de dados de entrada de três colunas produz um erro semelhante à seguinte mensagem:

```output
All columns of the table must be specified in the INSERT BULK statement.
```

O valor NU É uma forma especial do valor padrão. Se a coluna for anulada, os dados de entrada na bolha para essa coluna podem estar vazios. Mas não pode faltar do conjunto de dados de entrada. PolyBase insere NU POR valores em falta no Azure Synapse Analytics.

#### <a name="external-file-access-failed"></a>O acesso externo ao ficheiro falhou

Se receber o seguinte erro, certifique-se de que está a utilizar a autenticação de identidade gerida e que concedeu permissões ao Leitor de Dados do Armazenamento Blob para a identidade gerida do espaço de trabalho Azure Synapse.

```output
Job failed due to reason: at Sink '[SinkName]': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: External file access failed due to internal error: 'Error occurred while accessing HDFS: Java exception raised on call to HdfsBridge_IsDirExist. Java exception message:\r\nHdfsBridge::isDirExist 
```

Para obter mais informações, consulte [permissões grant para gerir a identidade após a criação do espaço de trabalho.](../synapse-analytics/security/how-to-grant-workspace-managed-identity-permissions.md#grant-permissions-to-managed-identity-after-workspace-creation)

## <a name="use-copy-statement-to-load-data-into-azure-synapse-analytics"></a><a name="use-copy-statement"></a> Utilize a declaração COPY para carregar dados no Azure Synapse Analytics

A declaração Azure Synapse Analytics [COPY](/sql/t-sql/statements/copy-into-transact-sql) suporta diretamente os dados de carregamento da **Azure Blob e da Azure Data Lake Storage Gen2**. Se os seus dados de origem satisfaçam os critérios descritos nesta secção, pode optar por utilizar a declaração COPY em ADF para carregar dados no Azure Synapse Analytics. A Azure Data Factory verifica as definições e falha a atividade de cópia executada se os critérios não forem cumpridos.

>[!NOTE]
>Atualmente, a Data Factory apenas suporta cópia a partir de fontes compatíveis com a declaração COPY mencionadas abaixo.

>[!TIP]
>Ao utilizar a declaração COPY com o Tempo de Execução da Integração Azure, as Unidades eficazes [de Integração de Dados (DIU)](copy-activity-performance-features.md#data-integration-units) são sempre 2. A sintonização do DIU não afeta o desempenho, uma vez que os dados de carregamento do armazenamento são alimentados pelo motor Synapse.

A utilização da declaração COPY suporta a seguinte configuração:

1. O **serviço e formato ligados à fonte** estão com os seguintes tipos e métodos de autenticação:

    | Tipo de loja de dados de origem suportada                             | Formato suportado           | Tipo de autenticação de origem suportada                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | [Texto delimitado](format-delimited-text.md)             | Autenticação chave da conta, autenticação de assinatura de acesso partilhado, autenticação principal do serviço, autenticação de identidade gerida |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Autenticação chave de conta, autenticação de assinatura de acesso partilhado |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Autenticação chave de conta, autenticação de assinatura de acesso partilhado |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2) | [Texto delimitado](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Autenticação chave de conta, autenticação principal do serviço, autenticação de identidade gerida |

    >[!IMPORTANT]
    >- Quando utilizar a autenticação de identidade gerida para o seu serviço ligado ao armazenamento, aprenda as configurações necessárias para [Azure Blob](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md#managed-identity) respectivamente.
    >- Se o seu Azure Storage estiver configurado com o ponto final do serviço VNet, deve utilizar a autenticação de identidade gerida com "permitir um serviço Microsoft confiável" ativado na conta de armazenamento, consulte o [Impacto da utilização de Pontos Finais do Serviço VNet com armazenamento Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

2. As definições do formato são com as seguintes:

   1. Para **Parquet:** `compression` não pode ser **compressões,** **Snappy,** ou **GZip**.
   2. Para **o ORC:** `compression` não pode ser **compressões,** **```zlib```** ou **Snappy**.
   3. Para **texto delimitado:**
      1. `rowDelimiter` é explicitamente definido como **single character** ou "**\r\n**", o valor padrão não é suportado.
      2. `nullValue` é deixado como padrão ou definido para **corda vazia** ("").
      3. `encodingName` é deixado como padrão ou definido para **utf-8 ou utf-16**.
      4. `escapeChar` deve ser o mesmo `quoteChar` que , e não é vazio.
      5. `skipLineCount` é deixado como padrão ou definido para 0.
      6. `compression` não pode ser **compressões** ou **GZip**.

3. Se a sua fonte for uma pasta, `recursive` a atividade de cópia deve ser definida como verdadeira, e tem de ser `wildcardFilename` `*` . 

4. `wildcardFolderPath` , `wildcardFilename` (além `*` de , , , , e não são `modifiedDateTimeStart` `modifiedDateTimeEnd` `prefix` `enablePartitionDiscovery` `additionalColumns` especificados.

As seguintes definições de declaração COPY são suportadas `allowCopyCommand` na atividade de cópia:

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| padrãoValues | Especifica os valores predefinidos de cada coluna-alvo no Azure Synapse Analytics.  Os valores predefinidos na propriedade substituem a restrição DEFAULT definida no armazém de dados, e a coluna de identidade não pode ter um valor padrão. | No |
| opções adicionais | Opções adicionais que serão passadas para uma declaração Azure Synapse Analytics COPY diretamente na cláusula "With" em [declaração COPY](/sql/t-sql/statements/copy-into-transact-sql). Cite o valor necessário para alinhar com os requisitos de declaração COPY. | No |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true,
                "copyCommandSettings": {
                    "defaultValues": [
                        {
                            "columnName": "col_string",
                            "defaultValue": "DefaultStringValue"
                        }
                    ],
                    "additionalOptions": {
                        "MAXERRORS": "10000",
                        "DATEFORMAT": "'ymd'"
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, pode ler e escrever para tabelas a partir de Azure Synapse Analytics. Para obter mais informações, consulte a [transformação](data-flow-source.md) da fonte e [a transformação do sumidouro](data-flow-sink.md) nos fluxos de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

As definições específicas do Azure Synapse Analytics estão disponíveis no separador **Opções De Origem** da transformação da fonte.

**Entrada** Selecione se aponta a sua fonte para uma mesa (equivalente ```Select * from <table-name>``` a) ou introduza uma consulta SQL personalizada.

**Ativar a encenação** Recomenda-se vivamente que utilize esta opção em cargas de trabalho de produção com fontes Azure Synapse Analytics. Quando executa uma [atividade de fluxo de dados](control-flow-execute-data-flow-activity.md) com fontes Azure Synapse Analytics a partir de um oleoduto, a ADF irá solicitar-lhe uma conta de armazenamento de localização de localização de localização e irá usá-la para carregamento de dados encenado. É o mecanismo mais rápido para carregar dados da Azure Synapse Analytics.

- Quando utilizar a autenticação de identidade gerida para o seu serviço ligado ao armazenamento, aprenda as configurações necessárias para [Azure Blob](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md#managed-identity) respectivamente.
- Se o seu Azure Storage estiver configurado com o ponto final do serviço VNet, deve utilizar a autenticação de identidade gerida com "permitir um serviço Microsoft confiável" ativado na conta de armazenamento, consulte o [Impacto da utilização de Pontos Finais do Serviço VNet com armazenamento Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).
- Quando utilizar o pool SQL **sem servidor** Azure Synapse como fonte, ativar a paragem não é suportada.

**Consulta**: Se selecionar Consulta no campo de entrada, introduza uma consulta SQL para a sua fonte. Esta definição substitui qualquer tabela que tenha escolhido no conjunto de dados. **As** cláusulas de encomenda por não são suportadas aqui, mas pode definir uma declaração completa SELECT FROM. Também pode utilizar funções de tabela definidas pelo utilizador. **selecionar * do udfGetData()** é um UDF em SQL que devolve uma tabela. Esta consulta produzirá uma tabela de origem que pode utilizar no fluxo de dados. Usar consultas também é uma ótima maneira de reduzir linhas para testes ou para procuras.

Exemplo SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Tamanho do lote**: Introduza o tamanho do lote para colar dados grandes em leituras. Nos fluxos de dados, a ADF utilizará esta definição para definir o caching colunar Spark. Este é um campo de opção, que utilizará os padrão spark se ficar em branco.

**Nível de isolamento**: O padrão para as fontes SQL no fluxo de dados de mapeamento é lido sem compromisso. Pode alterar o nível de isolamento aqui para um destes valores:

- Ler Comprometido
- Ler Não Comprometido
- Leitura repetível
- Serializável
- Nenhum (ignorar o nível de isolamento)

![Nível de Isolamento](media/data-flow/isolationlevel.png)

### <a name="sink-transformation"></a>Transformação do sumidouro

As definições específicas do Azure Synapse Analytics estão disponíveis no **separador Definições** da transformação do lavatório.

**Método de atualização:** Determina quais as operações permitidas no seu destino de base de dados. O padrão é apenas permitir inserções. Para atualizar, intensificar ou apagar linhas, é necessária uma transformação de linhas alteradas para marcar linhas para essas ações. Para atualizações, atualizações e eliminações, deve ser definida uma coluna ou colunas-chave para determinar qual a linha a alterar.

**Ação da tabela:** Determina se deve recriar ou remover todas as linhas da tabela de destino antes de escrever.

- Nenhuma: nenhuma ação será feita à mesa.
- Recriar: A mesa será largada e recriada. Necessário se criar uma nova tabela dinamicamente.
- Truncato: Todas as linhas da mesa-alvo serão removidas.

**Ativar a encenação:** Determina se deve ou não utilizar a [PolyBase](/sql/relational-databases/polybase/polybase-guide) ao escrever para o Azure Synapse Analytics. O armazenamento de encenação está configurado na [atividade executar fluxo de dados](control-flow-execute-data-flow-activity.md). 

- Quando utilizar a autenticação de identidade gerida para o seu serviço ligado ao armazenamento, aprenda as configurações necessárias para [Azure Blob](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md#managed-identity) respectivamente.
- Se o seu Azure Storage estiver configurado com o ponto final do serviço VNet, deve utilizar a autenticação de identidade gerida com "permitir um serviço Microsoft confiável" ativado na conta de armazenamento, consulte o [Impacto da utilização de Pontos Finais do Serviço VNet com armazenamento Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

**Tamanho do lote**: Controla quantas linhas estão a ser escritas em cada balde. Tamanhos maiores do lote melhoram a compressão e a otimização da memória, mas arriscam-se a sair das exceções de memória ao caching dados.

**Scripts pré e post SQL**: Introduza scripts SQL de várias linhas que serão executados antes (pré-processamento) e depois (pós-processamento) dados são escritos na sua base de dados de Sink

![scripts de processamento pré e pós SQL](media/data-flow/prepost1.png "Scripts de processamento SQL")

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade getMetadata

Para saber mais detalhes sobre as propriedades, consulte a [atividade da GetMetadata](control-flow-get-metadata-activity.md)

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapeamento do tipo de dados para Azure Synapse Analytics

Quando copia dados de ou para Azure Synapse Analytics, os seguintes mapeamentos são usados desde os tipos de dados Azure Synapse Analytics até aos tipos de dados provisórios da Azure Data Factory. Consulte [os mapeamentos de esquemas e tipos de dados](copy-activity-schema-and-type-mapping.md) para saber como a Copy Activity mapeia o esquema de origem e o tipo de dados para a pia.

>[!TIP]
>Consulte os tipos de dados de tabela no artigo [Azure Synapse Analytics](../synapse-analytics/sql/develop-tables-data-types.md) sobre os tipos de dados suportados pela Azure Synapse Analytics e as soluções alternativas para os não suportados.

| Tipo de dados Azure Synapse Analytics    | Tipo de dados provisórios da Data Factory |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Booleano                        |
| char                                  | String, Char[]                 |
| data                                  | DateTime                       |
| Datetime                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datatimeoff                        | Início de execução de tempo de data                 |
| Decimal                               | Decimal                        |
| Atributo FILESTREAM (varbinário(máx)) | Byte[]                         |
| Float                                 | Double (Duplo)                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| dinheiro                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Único                         |
| rowversão                            | Byte[]                         |
| hora pequena                         | DateTime                       |
| smallint                              | Int16                          |
| pequeno dinheiro                            | Decimal                        |
| hora                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | GUID                           |
| varbinário                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Passos seguintes

Para uma lista de lojas de dados suportadas como fontes e sumidouros pela Copy Activity na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
