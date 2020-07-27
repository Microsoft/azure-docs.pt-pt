---
title: Copiar e transformar dados em Azure Synapse Analytics
description: Aprenda a copiar dados de e para a Azure Synapse Analytics e transforme dados em Azure Synapse Analytics utilizando a Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: 8d7171bafb292b0520b8873bad0ce8f55ab4040d
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171506"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiar e transformar dados em Azure Synapse Analytics (anteriormente Azure SQL Data Warehouse) utilizando a Azure Data Factory

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
- Como fonte, recupere os dados utilizando uma consulta SQL ou procedimento armazenado.
- Como pia, carregue os dados utilizando a declaração [PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) ou [COPY](#use-copy-statement) (pré-visualização) ou a peça a granel. Recomendamos a declaração PolyBase ou COPY (pré-visualização) para um melhor desempenho da cópia. O conector também suporta automaticamente a criação de uma tabela de destino se não existir com base no esquema de origem.

> [!IMPORTANT]
> Se copiar dados utilizando o tempo de execução da integração da Azure Data Factory, configure uma [regra de firewall ao nível do servidor](../azure-sql/database/firewall-configure.md) para que os serviços Azure possam aceder ao servidor [lógico SQL](../azure-sql/database/logical-servers.md).
> Se copiar dados utilizando um tempo de integração auto-hospedado, configufique a firewall para permitir o intervalo ip apropriado. Esta gama inclui o IP da máquina que é usado para ligar ao Azure Synapse Analytics.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter o melhor desempenho, utilize a PolyBase para carregar dados no Azure Synapse Analytics. A [Utilização polyBase para carregar dados na secção Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) tem detalhes. Para uma passagem com uma caixa de utilização, consulte [a Carga 1 TB em Azure Synapse Analytics em menos de 15 minutos com a Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que definem entidades da Data Factory específicas de um conector Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para um serviço Azure Synapse Analytics ligado:

| Propriedade            | Descrição                                                  | Obrigatório                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| tipo                | A propriedade tipo deve ser definida para **AzureSqlDW**.             | Sim                                                          |
| conexãoStragem    | Especifique as informações necessárias para ligar à instância Azure Synapse Analytics para a **propriedade connectionString.** <br/>Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory. Também pode colocar a chave principal de senha/serviço no Cofre da Chave Azure, e se for a autenticação SQL retire a `password` configuração da cadeia de ligação. Veja o exemplo JSON abaixo da tabela e [guarde as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim                                                          |
| servicePrincipalId  | Especifique a identificação do cliente da aplicação.                         | Sim, quando utilizar a autenticação AZure AD com um principal de serviço. |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim, quando utilizar a autenticação AZure AD com um principal de serviço. |
| inquilino              | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Sim, quando utilizar a autenticação AZure AD com um principal de serviço. |
| connectVia          | O [tempo de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou um tempo de integração auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. | Não                                                           |

Para diferentes tipos de autenticação, consulte as seguintes secções sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- Autenticação simbólica de aplicação Azure: Diretor [de serviço](#service-principal-authentication)
- Autenticação simbólica de aplicação Azure: [Identidades geridas para recursos Azure](#managed-identity)

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

4. **Conceda ao chefe de serviço permissões necessárias,** como normalmente faz para utilizadores sql ou outros. Executar o seguinte código, ou consulte mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se pretender utilizar o PolyBase para carregar os dados, saiba qual é a permissão necessária para a [base de dados](#required-database-permission).

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Identidades geridas para autenticação de recursos Azure

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos da Azure](data-factory-service-identity.md) que representa a fábrica específica. Pode utilizar esta identidade gerida para a autenticação Azure Synapse Analytics. A fábrica designada pode aceder e copiar dados de ou para o seu armazém de dados utilizando esta identidade.

Para utilizar a autenticação de identidade gerida, siga estes passos:

1. **[Forcê um administrador do Azure Ative Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** para o seu servidor no portal Azure se ainda não o fez. O administrador AD Azure pode ser um utilizador AD Azure ou grupo AZure AD. Se conceder ao grupo com identidade gerida um papel de administrador, salte os passos 3 e 4. O administrador terá acesso total à base de dados.

2. **[Criar utilizadores de bases de dados contidos](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** para a Identidade Gerida pela Fábrica de Dados. Conecte-se ao armazém de dados a partir ou ao qual pretende copiar dados utilizando ferramentas como sSMS, com uma identidade AD Azure que tem pelo menos qualquer permissão do UTILIZADOR. Executar o seguinte T-SQL.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Conceder à Data Factory Managed Identity permissões necessárias,** como normalmente faz para utilizadores de SQL e outros. Executar o seguinte código, ou consulte mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se pretender utilizar o PolyBase para carregar os dados, saiba qual é a permissão necessária para a [base de dados](#required-database-permission).

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
| tipo      | A propriedade **do tipo** do conjunto de dados deve ser definida para **AzureSqlDWTable**. | Sim                         |
| esquema | O nome do esquema. |Não para a fonte, sim para a pia  |
| tabela | Nome da mesa/vista. |Não para a fonte, sim para a pia  |
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

Para copiar dados da Azure Synapse Analytics, decreva a propriedade **tipo** na fonte de Atividade de Cópia para **SqlDWSource**. As seguintes propriedades são suportadas na secção **origem** da Atividade de Cópia:

| Propriedade                     | Descrição                                                  | Obrigatório |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| tipo                         | A propriedade **tipo** da fonte de Atividade de Cópia deve ser definida como **SqlDWSource**. | Sim      |
| sqlReaderQuery               | Utilize a consulta SQL personalizada para ler dados. Exemplo: `select * from MyTable`. | Não       |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê os dados da tabela de origem. A última declaração SQL deve ser uma declaração SELECT no procedimento armazenado. | Não       |
| parametrómetros de reserva armazenados    | Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são pares de nomes ou valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. | Não       |
| isolamentoLevel | Especifica o comportamento de bloqueio de transação para a fonte SQL. Os valores permitidos são: **ReadCommitted,** **ReadUncommitted,** **RepeatableRead,** **Serializable**, **Snapshot**. Se não for especificado, é utilizado o nível de isolamento predefinido da base de dados. Consulte [este doc](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para mais detalhes. | Não |

**Exemplo: utilização da consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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

**Exemplo: utilização do procedimento armazenado**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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

**Procedimento de amostragem armazenada:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Azure Synapse Analytics como pia

A Azure Data Factory suporta três formas de carregar dados no SQL Data Warehouse.

![Opções de cópia de pia SQL DW](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Use PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse)
- [Use a declaração COPY (pré-visualização)](#use-copy-statement)
- Use inserção a granel

A forma mais rápida e escalável de carregar dados é através da [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) ou da [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (pré-visualização).

Para copiar os dados para o Azure SQL Data Warehouse, decreva o tipo de pia em Atividade de Cópia para **SqlDWSink**. As seguintes propriedades são suportadas na secção de **lavatório** Copy Activity:

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| tipo              | A propriedade **tipo** do lavatório Copy Activity deve ser definida como **SqlDWSink**. | Sim                                           |
| permitir A Base DePoly     | Indica se deve utilizar o PolyBase para carregar dados no SQL Data Warehouse. `allowCopyCommand`e `allowPolyBase` não pode ser ambos verdadeiros. <br/><br/>Consulte [o PolyBase para carregar dados na secção Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter constrangimentos e detalhes.<br/><br/>Os valores permitidos são **verdadeiros** e **falsos** (padrão). | Não.<br/>Aplicar quando utilizar o PolyBase.     |
| poliBaseSettings  | Um grupo de propriedades que podem ser especificadas quando a `allowPolybase` propriedade é definida como **verdadeira.** | Não.<br/>Aplicar quando utilizar o PolyBase. |
| permitirCopyCommand | Indica se deve utilizar a [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (pré-visualização) para carregar dados no SQL Data Warehouse. `allowCopyCommand`e `allowPolyBase` não pode ser ambos verdadeiros. <br/><br/>Consulte [a declaração de Utilização COPY para carregar dados na secção Azure SQL Data Warehouse](#use-copy-statement) para obter constrangimentos e detalhes.<br/><br/>Os valores permitidos são **verdadeiros** e **falsos** (padrão). | Não.<br>Aplicar ao utilizar COPY. |
| copyCommandSettings | Um grupo de propriedades que podem ser especificadas quando `allowCopyCommand` a propriedade é definida para TRUE. | Não.<br/>Aplicar ao utilizar COPY. |
| escreverBatchSize    | Número de linhas para inserir na tabela SQL **por lote**.<br/><br/>O valor permitido é **inteiro (número** de linhas). Por predefinição, a Data Factory determina dinamicamente o tamanho apropriado do lote com base no tamanho da linha. | Não.<br/>Aplicar quando utilizar a inserção a granel.     |
| escreverBatchTimeout | Aguarde o tempo para que a operação de inserção do lote termine antes de se esgotar.<br/><br/>O valor permitido é **o tempo.** Exemplo: "00:30:00" (30 minutos). | Não.<br/>Aplicar quando utilizar a inserção a granel.        |
| preCopyScript     | Especifique uma consulta SQL para a Copy Activity a executar antes de escrever dados no Azure SQL Data Warehouse em cada execução. Utilize esta propriedade para limpar os dados pré-carregados. | Não                                            |
| mesaOption | Especifica se deve [criar automaticamente a tabela do lavatório](copy-activity-overview.md#auto-create-sink-tables) se não existir com base no esquema de origem. A criação de tabela automática não é suportada quando a cópia encenada é configurada na atividade de cópia. Os valores permitidos são: `none` (padrão), `autoCreate` . |Não |
| desativaçãoMetricosCollecto | A Data Factory recolhe métricas como OS DWUs do SQL Data Warehouse para otimização de desempenho de cópia e recomendações. Se estiver preocupado com este comportamento, especifique `true` para desligá-lo. | Não (o padrão `false` é) |

#### <a name="sql-data-warehouse-sink-example"></a>SQL Data Warehouse exemplo de afundanço

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

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Utilize a PolyBase para carregar dados no Armazém de Dados Azure SQL

A utilização do [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) é uma forma eficiente de carregar uma grande quantidade de dados no Azure Synapse Analytics com alta produção. Verá um grande ganho na produção utilizando o PolyBase em vez do mecanismo padrão BULKINSERT. Para uma passagem com uma caixa de utilização, consulte [a Carga 1 TB em Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

- Se os seus dados de origem estiverem em **Azure Blob, Azure Data Lake Storage Gen1 ou Azure Data Lake Storage Gen2**, e o **formato é compatível com PolyBase,** pode utilizar a atividade de cópia para invocar diretamente a PolyBase para permitir que o Azure SQL Data Warehouse retire os dados da fonte. Para mais informações, consulte **[a cópia direta utilizando a PolyBase](#direct-copy-by-using-polybase)**.
- Se a sua loja de dados de origem e o seu formato não forem originalmente suportados pela PolyBase, utilize a cópia Encenada utilizando a funcionalidade **[PolyBase.](#staged-copy-by-using-polybase)** A funcionalidade de cópia encenada também lhe proporciona uma melhor produção. Converte automaticamente os dados em formato compatível com a PolyBase, armazena os dados no armazenamento do Azure Blob e, em seguida, chama a PolyBase para carregar dados no SQL Data Warehouse.

> [!TIP]
> Saiba mais sobre [as melhores práticas para a utilização do PolyBase.](#best-practices-for-using-polybase)

As seguintes definições de PolyBase são suportadas `polyBaseSettings` na atividade de cópia:

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejeitarValue       | Especifica o número ou percentagem de linhas que podem ser rejeitadas antes da consulta falhar.<br/><br/>Saiba mais sobre as opções de rejeição da PolyBase na secção de Argumentos da [TABELA EXTERNA CREATE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Os valores permitidos são 0 (padrão), 1, 2, etc. | Não                                            |
| rejeitarType        | Especifica se a opção **rejeitar o Valor** é um valor literal ou uma percentagem.<br/><br/>Os valores permitidos são **Valor** (padrão) e **Percentagem**. | Não                                            |
| rejeitarSampleValue | Determina o número de linhas a recuperar antes que a PolyBase recalcule a percentagem de linhas rejeitadas.<br/><br/>Os valores permitidos são 1, 2, etc. | Sim, se o **Tipo rejeitado** for **percentagem.** |
| utilizarTypeDefault    | Especifica como lidar com valores em falta em ficheiros de texto delimitados quando o PolyBase recupera dados do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade a partir da secção Argumentos em [FORMATO DE FICHEIRO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Os valores permitidos são **verdadeiros** e **falsos** (padrão).<br><br> | Não                                            |

### <a name="direct-copy-by-using-polybase"></a>Cópia direta utilizando o PolyBase

O SQL Data Warehouse PolyBase suporta diretamente Azure Blob, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2. Se os seus dados de origem satisfaçam os critérios descritos nesta secção, utilize a PolyBase para copiar diretamente da loja de dados de origem para a Azure Synapse Analytics. Caso contrário, utilize [cópia encenada utilizando a PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Para copiar dados de forma eficiente para o SQL Data Warehouse, saiba mais com [a Azure Data Factory, tornando ainda mais fácil e conveniente descobrir insights de dados ao utilizar a Data Lake Store com o SQL Data Warehouse.](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)

Se os requisitos não forem cumpridos, a Azure Data Factory verifica as definições e volta automaticamente ao mecanismo BULKINSERT para o movimento de dados.

1. O **serviço ligado à fonte** encontra-se com os seguintes tipos e métodos de autenticação:

    | Tipo de loja de dados de origem suportada                             | Tipo de autenticação de origem suportada                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | Autenticação chave de conta, autenticação de identidade gerida |
    | [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md) | Autenticação do principal de serviço                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2) | Autenticação chave de conta, autenticação de identidade gerida |

    >[!IMPORTANT]
    >Se o seu Azure Storage estiver configurado com o ponto final do serviço VNet, deve utilizar a autenticação de identidade gerida - consulte o [Impacto da utilização de Pontos Finais de Serviço VNet com armazenamento Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Aprenda as configurações necessárias na Data Factory a partir de [Azure Blob - autenticação de identidade gerida](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2 -](connector-azure-data-lake-storage.md#managed-identity) secção de autenticação de identidade gerida respectivamente.

2. O formato de dados de **origem** é de **Parquet,** **ORC,** ou **texto delimitado,** com as seguintes configurações:

   1. O caminho da pasta não contém filtro wildcard.
   2. O nome do ficheiro está vazio ou aponta para um único ficheiro. Se especificar o nome do ficheiro wildcard na atividade da cópia, só pode ser `*` ou `*.*` .
   3. `rowDelimiter`é **padrão**, **\n**, **\r\n,** ou **\r**.
   4. `nullValue`é deixado como padrão ou definido para **cadeia vazia** (""), e `treatEmptyAsNull` é deixado como padrão ou definido para verdadeiro.
   5. `encodingName`é deixado como padrão ou definido para **utf-8**.
   6. `quoteChar`E `escapeChar` `skipLineCount` não estão especificados. Linha de suporte polyBase salte a linha do cabeçalho, que pode ser configurada como `firstRowAsHeader` em ADF.
   7. `compression`não pode ser **compressões,** **GZip,** ou **Deflate**.

3. Se a sua fonte for uma pasta, `recursive` a atividade de cópia deve ser definida como verdadeira.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart` e não `modifiedDateTimeEnd` `additionalColumns` estão especificados.

>[!NOTE]
>Se a sua fonte for uma pasta, note que a PolyBase recupera ficheiros da pasta e de todas as suas sub-dobradeiras, e não obtém dados de ficheiros para os quais o nome do ficheiro começa com um sublinhado (_) ou um período (.), como documentado [aqui - argumento de LOCALIZAÇÃO](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

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

Quando os seus dados de origem não são de forma nativa compatível com a PolyBase, ative a cópia de dados através de uma instância de armazenamento provisória de Azure Blob (não pode ser Azure Premium Storage). Neste caso, a Azure Data Factory converte automaticamente os dados para satisfazer os requisitos do formato de dados da PolyBase. Em seguida, invoca a PolyBase para carregar dados no SQL Data Warehouse. Finalmente, limpa os seus dados temporários do armazenamento de bolhas. Consulte [a cópia encenada](copy-activity-performance-features.md#staged-copy) para obter mais detalhes sobre a cópia de dados através de uma fase de armazenamento Azure Blob.

Para utilizar esta funcionalidade, crie um [serviço ligado ao Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) que se refira à conta de armazenamento Azure com o armazenamento provisório do blob. Em seguida, especifique as `enableStaging` propriedades e propriedades para a Atividade de `stagingSettings` Cópia, como mostrado no código seguinte.

>[!IMPORTANT]
>Se a sua encenação Azure Storage estiver configurada com o ponto final do serviço VNet, deve utilizar a autenticação de identidade gerida - consulte o [Impacto da utilização de Pontos Finais de Serviço VNet com armazenamento Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Aprenda as configurações necessárias na Data Factory a partir de [Azure Blob - autenticação de identidade gerida](connector-azure-blob-storage.md#managed-identity).

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
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Melhores práticas para utilizar o PolyBase

As seguintes secções proporcionam as melhores práticas para além das mencionadas nas [melhores práticas para o Azure Synapse Analytics](../synapse-analytics/sql/best-practices-sql-pool.md).

#### <a name="required-database-permission"></a>Autorização de base de dados necessária

Para utilizar o PolyBase, o utilizador que carrega dados no SQL Data Warehouse deve ter [permissão "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) na base de dados-alvo. Uma forma de o conseguir é adicionar o utilizador como membro do **db_owner** papel. Saiba como fazê-lo na visão geral do [SqL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Tamanho da linha e limites do tipo de dados

As cargas PolyBase limitam-se a filas inferiores a 1 MB. Não pode ser utilizado para carregar para VARCHR (MAX), NVARCHAR (MAX) ou VARBINARY (MAX). Para obter mais informações, consulte [os limites de capacidade de serviço do SQL Data Warehouse.](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)

Quando os dados de origem têm linhas superiores a 1 MB, é melhor dividir verticalmente as tabelas de origem em várias pequenas. Certifique-se de que o maior tamanho de cada linha não excede o limite. As tabelas mais pequenas podem então ser carregadas utilizando a PolyBase e fundidas em Azure Synapse Analytics.

Em alternativa, para os dados com colunas tão largas, pode utilizar a não-Base PoliBase para carregar os dados utilizando o ADF, desligando a definição "permitir a PoliBase".

#### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do Armazém de Dados SQL

Para obter o melhor rendimento possível, atribua uma classe de recursos maior ao utilizador que carrega dados no SQL Data Warehouse via PolyBase.

#### <a name="polybase-troubleshooting"></a>Resolução de problemas da PolyBase

**Carregamento para coluna decimal**

Se os seus dados de origem estiverem em formato de texto ou noutras lojas compatíveis com a PolyBase (utilizando cópia encenada e PolyBase), e contiver valor vazio para ser carregado na coluna Decimal do Armazém de Dados SQL, poderá atingir o seguinte erro:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A solução é desmarcar a opção "**Use type default**" (como falso) na atividade de cópia -> as definições de PolyBase. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" é uma configuração nativa da PolyBase, que especifica como lidar com valores em falta em ficheiros de texto delimitados quando o PolyBase recupera dados do ficheiro de texto.

**`tableName`em Azure Synapse Analytics**

A tabela seguinte dá exemplos de como especificar a propriedade **tableName** no conjunto de dados JSON. Mostra várias combinações de esquemas e nomes de mesa.

| DB Schema | Nome da tabela | **nome de mesa** Propriedade JSON               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable ou dbo. MyTable ou [dbo]. [MyTable] |
| dbo1      | MyTable    | dbo1. MyTable ou [dbo1]. [MyTable]          |
| dbo       | A minha.mesa   | [My.Table] ou [dbo]. [Minha.Mesa]            |
| dbo1      | A minha.mesa   | [dbo1]. [Minha.Mesa]                         |

Se vir o seguinte erro, o problema pode ser o valor especificado para a **propriedade tableName.** Consulte a tabela anterior para obter a forma correta de especificar valores para a propriedade JSON de nome de **mesa.**

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Colunas com valores predefinidos**

Atualmente, a funcionalidade PolyBase na Data Factory aceita apenas o mesmo número de colunas que na tabela-alvo. Um exemplo é uma tabela com quatro colunas onde uma delas é definida com um valor padrão. Os dados de entrada ainda precisam de ter quatro colunas. Um conjunto de dados de entrada de três colunas produz um erro semelhante à seguinte mensagem:

```
All columns of the table must be specified in the INSERT BULK statement.
```

O valor NU É uma forma especial do valor padrão. Se a coluna for anulada, os dados de entrada na bolha para essa coluna podem estar vazios. Mas não pode faltar do conjunto de dados de entrada. PolyBase insere NU POR valores em falta no Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>Utilize a declaração COPY para carregar dados no Armazém de Dados Azure SQL (pré-visualização)

A declaração de [CÓPIA](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) do Armazém de Dados SQL (pré-visualização) suporta diretamente os dados de carregamento da **Azure Blob e da Azure Data Lake Storage Gen2**. Se os seus dados de origem satisfaçam os critérios descritos nesta secção, pode optar por utilizar a declaração COPY em ADF para carregar dados no Armazém de Dados Azure SQL. A Azure Data Factory verifica as definições e falha a atividade de cópia executada se os critérios não forem cumpridos.

>[!NOTE]
>Atualmente, a Data Factory apenas suporta cópia a partir de fontes compatíveis com a declaração COPY mencionadas abaixo.

A utilização da declaração COPY suporta a seguinte configuração:

1. O **serviço e formato ligados à fonte** estão com os seguintes tipos e métodos de autenticação:

    | Tipo de loja de dados de origem suportada                             | Formato suportado           | Tipo de autenticação de origem suportada                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | [Texto delimitado](format-delimited-text.md)             | Autenticação chave da conta, autenticação de assinatura de acesso partilhado, autenticação principal do serviço, autenticação de identidade gerida |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Autenticação chave de conta, autenticação de assinatura de acesso partilhado |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Autenticação chave de conta, autenticação de assinatura de acesso partilhado |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2) | [Texto delimitado](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Autenticação chave de conta, autenticação principal do serviço, autenticação de identidade gerida |

    >[!IMPORTANT]
    >Se o seu Azure Storage estiver configurado com o ponto final do serviço VNet, deve utilizar a autenticação de identidade gerida - consulte o [Impacto da utilização de Pontos Finais de Serviço VNet com armazenamento Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Aprenda as configurações necessárias na Data Factory a partir de [Azure Blob - autenticação de identidade gerida](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2 -](connector-azure-data-lake-storage.md#managed-identity) secção de autenticação de identidade gerida respectivamente.

2. As definições do formato são com as seguintes:

   1. Para **Parquet:** `compression` não pode ser **compressões,** **Snappy,** ou **GZip**.
   2. Para **o ORC:** `compression` não pode ser **compressões,** **```zlib```** ou **Snappy**.
   3. Para **texto delimitado:**
      1. `rowDelimiter`é explicitamente definido como **single character** ou "**\r\n**", o valor padrão não é suportado.
      2. `nullValue`é deixado como padrão ou definido para **corda vazia** ("").
      3. `encodingName`é deixado como padrão ou definido para **utf-8 ou utf-16**.
      4. `escapeChar`deve ser o mesmo `quoteChar` que , e não é vazio.
      5. `skipLineCount`é deixado como padrão ou definido para 0.
      6. `compression`não pode ser **compressões** ou **GZip**.

3. Se a sua fonte for uma pasta, `recursive` a atividade de cópia deve ser definida como verdadeira, e tem de ser `wildcardFilename` `*` . 

4. `wildcardFolderPath`, `wildcardFilename` (além `*` de ), e não são `modifiedDateTimeStart` `modifiedDateTimeEnd` `additionalColumns` especificados.

As seguintes definições de declaração COPY são suportadas `allowCopyCommand` na atividade de cópia:

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| padrãoValues | Especifica os valores predefinidos para cada coluna-alvo em SQL DW.  Os valores predefinidos na propriedade substituem a restrição DEFAULT definida no armazém de dados, e a coluna de identidade não pode ter um valor padrão. | Não |
| opções adicionais | Opções adicionais que serão passadas para a declaração DE DW DW SQL diretamente na cláusula "With" na [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Cite o valor necessário para alinhar com os requisitos de declaração COPY. | Não |

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

**Ativar a encenação** Recomenda-se vivamente que utilize esta opção em cargas de trabalho de produção com fontes DW da Synapse. Quando executa uma atividade de fluxo de dados com fontes synapase a partir de um oleoduto, a ADF irá solicitar-lhe uma conta de armazenamento de localização de localização de localização e irá usá-la para carregamento de dados encenado. É o mecanismo mais rápido para carregar dados da Synapse DW.

**Consulta**: Se selecionar Consulta no campo de entrada, introduza uma consulta SQL para a sua fonte. Esta definição substitui qualquer tabela que tenha escolhido no conjunto de dados. **As** cláusulas de encomenda por não são suportadas aqui, mas pode definir uma declaração completa SELECT FROM. Também pode utilizar funções de tabela definidas pelo utilizador. **selecionar * do udfGetData()** é um UDF em SQL que devolve uma tabela. Esta consulta produzirá uma tabela de origem que pode utilizar no fluxo de dados. Usar consultas também é uma ótima maneira de reduzir linhas para testes ou para procuras.

Exemplo SQL:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Tamanho do lote**: Introduza o tamanho do lote para colar dados grandes em leituras. Nos fluxos de dados, a ADF utilizará esta definição para definir o caching colunar Spark. Este é um campo de opção, que utilizará os padrão spark se ficar em branco.

**Nível de isolamento**: O padrão para as fontes SQL no fluxo de dados de mapeamento é lido sem compromisso. Pode alterar o nível de isolamento aqui para um destes valores:

- Ler Comprometido
- Ler Não Comprometido
- Leitura repetível
- Serializável *- Nenhum (ignorar o nível de isolamento)

![Nível de Isolamento](media/data-flow/isolationlevel.png "Nível de Isolamento")

### <a name="sink-transformation"></a>Transformação do sumidouro

As definições específicas do Azure Synapse Analytics estão disponíveis no **separador Definições** da transformação do lavatório.

**Método de atualização:** Determina quais as operações permitidas no seu destino de base de dados. O padrão é apenas permitir inserções. Para atualizar, intensificar ou apagar linhas, é necessária uma transformação de linhas alteradas para marcar linhas para essas ações. Para atualizações, atualizações e eliminações, deve ser definida uma coluna ou colunas-chave para determinar qual a linha a alterar.

**Ação da tabela:** Determina se deve recriar ou remover todas as linhas da tabela de destino antes de escrever.

- Nenhuma: nenhuma ação será feita à mesa.
- Recriar: A mesa será largada e recriada. Necessário se criar uma nova tabela dinamicamente.
- Truncato: Todas as linhas da mesa-alvo serão removidas.

**Ativar a encenação:** Determina se deve ou não utilizar a [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) ao escrever para a Azure Synapse Analytics

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
>Consulte os tipos de dados de tabela no artigo [Azure Synapse Analytics](../synapse-analytics/sql/develop-tables-data-types.md) sobre tipos de dados suportados pela SQL DW e as soluções alternativas para os não suportados.

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

## <a name="next-steps"></a>Próximos passos

Para uma lista de lojas de dados suportadas como fontes e sumidouros pela Copy Activity na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
