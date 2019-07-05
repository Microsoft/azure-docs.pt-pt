---
title: Copiar dados de ou para a base de dados do Azure SQL com o Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de arquivos de dados de origem suportada para a base de dados do Azure SQL ou da base de dados SQL para arquivos de dados de sink suportado com o Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 5dbd739070b1f66fe5ff04f319a3818269d0bdaa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449614"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copiar dados de ou para a base de dados do Azure SQL com o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory, que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-sql-connector.md)
> * [Versão atual](connector-azure-sql-database.md)

Este artigo descreve como copiar dados de e para a base de dados do Azure SQL. Para saber mais sobre o Azure Data Factory, leia os [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SQL Database do Azure é suportada para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [suportado de matriz de origem/sink](copy-activity-overview.md) tabela
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, este conector SQL Database do Azure oferece suporte a essas funções:

- Copiar dados utilizando autenticação do SQL e autenticação de token de aplicação do Azure Active Directory (Azure AD) com identidades de gerido ou principal de serviço para recursos do Azure.
- Como uma origem, obter dados utilizando uma consulta SQL ou um procedimento armazenado.
- Como um sink, acrescentando dados a uma tabela de destino ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

>[!NOTE]
>Base de dados SQL do Azure [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) não é suportada por este conector agora. Para contornar, pode utilizar um [conector do ODBC genérico](connector-odbc.md) e um driver ODBC do SQL Server por meio de um runtime de integração autoalojado. Siga [esta orientação](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) com ODBC download e a ligação de cadeia de caracteres as configurações de driver.

> [!IMPORTANT]
> Se copiar dados com o runtime de integração do Azure Data Factory, configure uma [firewall de servidor SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para que os serviços do Azure podem aceder ao servidor.
> Se copiar dados com um runtime de integração autoalojado, configure a firewall de servidor SQL do Azure para permitir que o intervalo IP adequado. Este intervalo inclui o IP do computador que é utilizada para ligar à base de dados do Azure SQL.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Azure Data Factory específicas para um conector da base de dados do Azure SQL.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

Estas propriedades são suportadas para um serviço de base de dados do SQL do Azure ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade tem de ser definida como **AzureSqlDatabase**. | Sim |
| connectionString | Especificar as informações necessárias para ligar à instância de SQL Database do Azure para o **connectionString** propriedade. <br/>Marcar esse campo como **SecureString** armazena de forma segura no Azure Data Factory. Também pode colocar uma chave de principal de serviço ou a palavra-passe no Azure Key Vault. Se é autenticação de SQL, extrair o `password` configuração fora de cadeia de ligação. Para obter mais informações, consulte o exemplo JSON a tabela a seguir e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim, ao utilizar a autenticação do Azure AD com um principal de serviço |
| servicePrincipalKey | Especifique a chave da aplicação. Marcar esse campo como **SecureString** armazena de forma segura no Azure Data Factory ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim, ao utilizar a autenticação do Azure AD com um principal de serviço |
| tenant | Especifique as informações de inquilino, como o nome de domínio ou ID, sob a qual reside a aplicação de inquilino. Recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim, ao utilizar a autenticação do Azure AD com um principal de serviço |
| connectVia | Isso [runtime de integração](concepts-integration-runtime.md) é utilizada para ligar ao arquivo de dados. Pode usar o runtime de integração do Azure ou um runtime de integração autoalojado, se seu arquivo de dados está localizado numa rede privada. Se não for especificado, é utilizado o runtime de integração do Azure padrão. | Não |

Para tipos de autenticação diferentes, consulte as secções seguintes em pré-requisitos e exemplos de JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- [Autenticação do Azure AD application token: Principal de serviço](#service-principal-authentication)
- [Autenticação do Azure AD application token: identidades geridas para recursos do Azure](#managed-identity)

>[!TIP]
>Se atingir um erro com o código de erro "UserErrorFailedToConnectToSqlServer" e uma mensagem como "o limite de sessão para a base de dados é XXX e foi atingido", adicione `Pooling=false` para sua cadeia de ligação e tente novamente.

### <a name="sql-authentication"></a>Autenticação do SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemplo de serviço ligado que utilize a autenticação SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Palavra-passe no Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para usar uma autenticação de token de aplicação de serviço baseada em principais do Azure AD, siga estes passos:

1. [Criar uma aplicação do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) do portal do Azure. Tome nota do nome da aplicação e os valores seguintes que definem o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do inquilino

2. [Aprovisionar um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) para o servidor de SQL do Azure no portal do Azure, se ainda não o tiver feito. O administrador do Azure AD tem de ser um utilizador do Azure AD ou o grupo do Azure AD, mas não pode ser um principal de serviço. Este passo é feito para que, no próximo passo, pode utilizar uma identidade do Azure AD para criar o principal de um utilizador de base de dados contido para o serviço.

3. [Criar utilizadores de base de dados contida](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para o principal de serviço. Ligar à base de dados de ou para o qual pretende copiar dados com ferramentas como o SQL Server Management Studio, com uma identidade do Azure AD que tenha, pelo menos, permissão de alterar qualquer utilizador. Execute o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Conceda que o principal de serviço as permissões necessárias, como faria normalmente para utilizadores SQL ou outras pessoas. Execute o seguinte código. Para obter mais opções, consulte [este documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Configure um serviço de base de dados do SQL do Azure ligada no Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço ligado que utilize autenticação do principal de serviço

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
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

### <a name="managed-identity"></a> Identidades geridas para a autenticação de recursos do Azure

Pode ser associada uma fábrica de dados com um [identidade de recursos do Azure gerida](data-factory-service-identity.md) que representa a fábrica de dados específicos. Pode utilizar esta identidade gerida para a autenticação de base de dados do Azure SQL. A fábrica designada pode aceder e copiar dados de ou para a base de dados com esta identidade.

Para utilizar a autenticação de identidade gerida, siga estes passos.

1. [Aprovisionar um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) para o servidor de SQL do Azure no portal do Azure, se ainda não o tiver feito. O administrador do Azure AD pode ser um utilizador ou um grupo do Azure AD. Se conceder ao grupo com a identidade gerida de uma função de administrador, ignore os passos 3 e 4. O administrador tem acesso total à base de dados.

2. [Criar utilizadores de base de dados contida](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) da fábrica de dados do Azure de identidade gerida. Ligar à base de dados de ou para o qual pretende copiar dados com ferramentas como o SQL Server Management Studio, com uma identidade do Azure AD que tenha, pelo menos, permissão de alterar qualquer utilizador. Execute o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Conceda a identidade de gerido a fábrica de dados necessitam permissões à medida que normalmente são necessárias para utilizadores SQL e outras pessoas. Execute o seguinte código. Para obter mais opções, consulte [este documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Configure um serviço de base de dados do SQL do Azure ligada no Azure Data Factory.

**Exemplo**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte [conjuntos de dados](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados de base de dados do Azure SQL.

Para copiar dados de ou para a base de dados do Azure SQL, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade do conjunto de dados tem de ser definida como **AzureSqlTable**. | Sim |
| tableName | O nome da tabela ou vista na instância da base de dados do Azure SQL que o serviço ligado refere-se a. | Não para a origem, Sim para o sink |

#### <a name="dataset-properties-example"></a>Exemplo de propriedades do conjunto de dados

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte [Pipelines](concepts-pipelines-activities.md). Esta secção fornece uma lista das propriedades compatíveis com a origem de base de dados do Azure SQL e de sink.

### <a name="azure-sql-database-as-the-source"></a>Base de dados de SQL do Azure como origem

Para copiar dados de base de dados do Azure SQL, defina o **tipo** propriedade na origem de atividade de cópia para **SqlSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade da origem de atividade de cópia tem de ser definida como **SqlSource**. | Sim |
| sqlReaderQuery | Esta propriedade usa a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. | Não |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê dados da tabela de origem. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. | Não |
| storedProcedureParameters | Parâmetros do procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. | Não |

**Pontos a serem observados:**

- Se **sqlReaderQuery** especificado para **SqlSource**, a atividade de cópia executa esta consulta em relação à origem de SQL Database do Azure para obter os dados. Também pode especificar um procedimento armazenado, especificando **sqlReaderStoredProcedureName** e **storedProcedureParameters** se o procedimento armazenado utiliza parâmetros.
- Se não especificar qualquer um **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, as colunas definidas na secção "estrutura" do conjunto de dados JSON são utilizadas para construir uma consulta. A consulta `select column1, column2 from mytable` for executado relativamente a base de dados do Azure SQL. Se a definição do conjunto de dados não tiver "estrutura", todas as colunas são selecionadas da tabela.

#### <a name="sql-query-example"></a>Exemplo de consulta de SQL

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

### <a name="stored-procedure-definition"></a>Definição do procedimento armazenado

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

### <a name="azure-sql-database-as-the-sink"></a>Base de dados de SQL do Azure como o sink

> [!TIP]
> Saiba mais sobre os comportamentos de escrita suportados, configurações e melhores práticas da [melhor prática para carregar dados para a base de dados do Azure SQL](#best-practice-for-loading-data-into-azure-sql-database).

Para copiar dados para a base de dados do Azure SQL, defina o **tipo** propriedade da atividade de cópia de sink para **SqlSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | O **tipo** propriedade do coletor de atividade de cópia tem de ser definida como **SqlSink**. | Sim |
| writeBatchSize | Número de linhas a inserir na tabela SQL *por lote*.<br/> O valor permitido é **número inteiro** (número de linhas). Por predefinição, o Azure Data Factory dinamicamente determina o tamanho do lote apropriado com base no tamanho de linha. | Não |
| writeBatchTimeout | O tempo de espera para o lote de inserção operação seja concluída antes de atingir o tempo limite.<br/> O valor permitido é **timespan**. Um exemplo é "00: 30:00" (30 minutos). | Não |
| preCopyScript | Especifica uma consulta SQL para a atividade de cópia a executar antes da escrita de dados na base de dados do Azure SQL. Ele é invocado apenas uma vez por cópia executar. Use essa propriedade para limpar os dados pré-carregado. | Não |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem para uma tabela de destino. <br/>Este procedimento armazenado está *invocado por lote*. Para operações que execute apenas uma vez e não têm nada a com dados de origem, por exemplo, delete ou truncate, utilize o `preCopyScript` propriedade. | Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado.<br/>Valores permitidos são pares nome / valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. | Não |
| sqlWriterTableType | Especifique um nome de tipo de tabela a ser utilizado no procedimento armazenado. A atividade de cópia torna os dados que está a ser movidos disponível numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados que estão a ser copiados com os dados existentes. | Não |

**Exemplo 1: Anexar dados**

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Prática recomendada para carregar dados para a base de dados do Azure SQL

Quando copiar dados na base de dados do Azure SQL, poderá necessitar de comportamento de escrita diferentes:

- [Acrescentar](#append-data): Meus dados de origem tem apenas de novos registos.
- [Upsert](#upsert-data): Meus dados de origem têm inserções e atualizações.
- [Substituir](#overwrite-the-entire-table): Quero recarregar uma tabela de toda a dimensão de cada vez.
- [Escrever com lógica personalizada](#write-data-with-custom-logic): Preciso de um processamento extra antes da última inserção na tabela de destino.

Consulte as secções correspondentes sobre como configurar no Azure Data Factory e melhores práticas.

### <a name="append-data"></a>Anexar dados

Acrescentar dados é o comportamento predefinido deste conector de sink do SQL Database do Azure. O Azure Data Factory faz uma inserção em massa para gravação eficiente à sua tabela. Pode configurar a origem e sink em conformidade na atividade de cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção 1:** Se tiver uma grande quantidade de dados para copiar, utilize a seguinte abordagem para fazer um upsert: 

- Em primeiro lugar, utilize um [tabela temporária de âmbito de base de dados](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) para carregamento em massa todos os registos através da atividade de cópia. Uma vez que operações em tabelas temporárias de âmbito de base de dados não tiver sessão iniciadas, é possível carregar milhões de registos em segundos.
- Executar uma atividade de procedimento armazenado no Azure Data Factory para aplicar uma [intercalar](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou instrução de inserção/ATUALIZAÇÃO. Utilize a tabela temporária, como a origem de executar todas as atualizações ou insere como uma única transação. Dessa forma, o número de ida e volta e operações de registo é reduzido. No fim da atividade de procedimento armazenado, a tabela temporária pode ser truncada para estar preparado para o próximo ciclo de upsert.

Por exemplo, no Azure Data Factory, pode criar um pipeline com uma **atividade de cópia** em cadeia com um **atividade Stored Procedure**. O primeiro copia dados de seu arquivo de origem para uma tabela temporária da base de dados do Azure SQL, por exemplo, **##UpsertTempTable**, como o nome da tabela no conjunto de dados. Em seguida, o último invoca um procedimento armazenado para intercalar dados de origem de tabela temporária para a tabela de destino e limpar a tabela temporária.

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

**Opção 2:** Também pode optar por [invocar um procedimento armazenado dentro da atividade de cópia](#invoke-a-stored-procedure-from-a-sql-sink). Essa abordagem é executado cada linha na tabela de origem em vez de utilizar a inserção em massa como a abordagem de predefinição na atividade de cópia, que não é adequada para upsert em grande escala.

### <a name="overwrite-the-entire-table"></a>Substituir a tabela inteira

Pode configurar o **preCopyScript** propriedade no sink de atividade de cópia. Neste caso, para cada atividade de cópia que é executado, Azure Data Factory é executado o script primeiro. Em seguida, ele executa a cópia para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, especifica um script para eliminar primeiro todos os registos antes de em massa carregar os novos dados a partir da origem.

### <a name="write-data-with-custom-logic"></a>Escrever dados com lógica personalizada

Os passos para escrever dados com lógica personalizada são semelhantes às descritas a [Upsert dados](#upsert-data) secção. Quando precisar de aplicar extra de processamento antes da inserção de final de origem de dados na tabela de destino, de grande escala, pode efetuar uma das duas coisas:

- Carregar para uma tabela temporária com âmbito da base de dados e, em seguida, chamar um procedimento armazenado. 
- Invoca um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocar um procedimento armazenado a partir de um sink SQL

Quando copiar dados na base de dados do Azure SQL, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais.

> [!TIP]
> Invocar um procedimento armazenado processa os dados de linha por linha em vez de através da utilização de uma operação em massa, que não é recomendada para cópia em grande escala. Saiba mais a partir da [melhor prática para carregar dados para a base de dados do Azure SQL](#best-practice-for-loading-data-into-azure-sql-database).

Pode usar um procedimento armazenado quando os mecanismos de cópia interna não atendem a finalidade. Um exemplo é quando deseja aplicar um processamento extra antes da inserção de final de origem de dados na tabela de destino. Alguns exemplos de processamento extra são quando deseja intercalar colunas, procurar valores adicionais e inserir em mais de uma tabela.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert numa tabela na base de dados do Azure SQL. Partem do princípio de que os dados de entrada e o sink **Marketing** cada tabela têm três colunas: **ProfileID**, **estado**, e **categoria**. Fazer o upsert com base na **ProfileID** coluna e aplicá-la apenas para uma categoria específica.

**Conjunto de dados de saída:** "tableName" é o mesmo nome de parâmetro de tipo de tabela no seu procedimento armazenado, conforme mostrado no seguinte script do procedimento armazenado:

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
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

A funcionalidade de procedimento armazenado aproveita [parâmetros de valor de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="mapping-data-flow-properties"></a>Propriedades de fluxo de dados de mapeamento

Conheça os detalhes da [transformação de origem](data-flow-source.md) e [sink transformação](data-flow-sink.md) no mapeamento de fluxo de dados.

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapeamento do tipo de dados de base de dados do Azure SQL

Quando os dados são copiados de ou para a base de dados do Azure SQL, os seguintes mapeamentos são utilizados entre tipos de dados do Azure SQL Database para tipos de dados intermediárias do Azure Data Factory. Para saber como a atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink, veja [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados de base de dados SQL do Azure | Tipo de dados intermediárias do Azure Data Factory |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Para tipos de dados que são mapeados para o tipo Decimal provisório, Azure Data Factory suporta atualmente precisão até 28. Se tiver dados com precisão maior que 28, considere a conversão para uma cadeia de caracteres na consulta SQL.

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados e formatos suportados](copy-activity-overview.md##supported-data-stores-and-formats).
