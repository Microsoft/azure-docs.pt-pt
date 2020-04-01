---
title: Copiar e transformar dados na Base de Dados Azure SQL
description: Saiba como copiar dados de e para a Base de Dados Azure SQL e transforme dados na Base de Dados Azure SQL utilizando a Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 8f5065a0f4a2a96a747a45f64e00e86f7990bfb8
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437800"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Copiar e transformar dados na Base de Dados Azure SQL utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão da Azure Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-sql-connector.md)
> * [Versão atual](connector-azure-sql-database.md)

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para a Base de Dados SQL azure, e usar o Fluxo de Dados para transformar dados na Base de Dados Azure SQL. Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector azure SQL Database é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com tabela [de matriz de origem/pia suportada](copy-activity-overview.md)
- [Mapeando o fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)

Para a atividade da Cópia, este conector de base de dados Azure SQL suporta estas funções:

- Copiar dados utilizando a autenticação SQL e o Azure Ative Directory (Azure AD) Autenticação simbólica com um diretor de serviço ou identidades geridas para recursos Azure.
- Como fonte, recuperar dados utilizando uma consulta SQL ou um procedimento armazenado.
- Como pia, anexando dados a uma tabela de destino ou invocando um procedimento armazenado com lógica personalizada durante a cópia.

>[!NOTE]
>A base de dados Azure SQL [Sempre Encriptada](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) não é suportada por este conector agora. Para trabalhar, pode utilizar um [conector ODBC genérico](connector-odbc.md) e um controlador ODBC sQL Server através de um tempo de funcionação de integração auto-hospedado. Siga [esta orientação](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) com configurações de descarregamento e cadeia de ligação do condutor DaOBC.

> [!IMPORTANT]
> Se copiar dados utilizando o tempo de execução de integração da Fábrica de Dados Azure, configure uma [firewall Do Servidor Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) para que os serviços do Azure possam aceder ao servidor.
> Se copiar dados utilizando um tempo de execução de integração auto-hospedado, configure a firewall do Servidor Azure SQL para permitir o intervalo IP apropriado. Esta gama inclui o IP da máquina que é usado para ligar à Base de Dados Azure SQL.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados Azure específicas de um conector de base de dados Azure SQL.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

Estas propriedades são suportadas para um serviço ligado à Base de Dados Azure SQL:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** deve ser definida para **AzureSqlDatabase**. | Sim |
| conexãoString | Especifique as informações necessárias para se ligar à base de dados Azure SQL para a **propriedade de ligaçãoString.** <br/>Também pode colocar uma senha ou chave principal de serviço no Cofre de Chaves Azure. Se for autenticação SQL, `password` retire a configuração da cadeia de ligação. Para mais informações, consulte o exemplo jSON seguindo as credenciais da tabela e [da loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| serviçoPrincipalId | Especifique a identificação do cliente do pedido. | Sim, quando se utiliza a autenticação Azure AD com um diretor de serviço |
| serviçoPrincipalKey | Especifique a chave da aplicação. Marque este campo como **SecureString** para o armazenar de forma segura na Azure Data Factory ou [referência a um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim, quando se utiliza a autenticação Azure AD com um diretor de serviço |
| inquilino | Especifique as informações do inquilino, como o nome de domínio ou identificação do inquilino, segundo a qual reside a sua candidatura. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | Sim, quando se utiliza a autenticação Azure AD com um diretor de serviço |
| connectVia | Este tempo de execução de [integração](concepts-integration-runtime.md) é utilizado para se ligar à loja de dados. Pode utilizar o tempo de execução da integração Azure ou um tempo de execução de integração auto-hospedado se a sua loja de dados estiver localizada numa rede privada. Se não for especificado, o tempo de execução de integração do Azure padrão é utilizado. | Não |

Para diferentes tipos de autenticação, consulte as seguintes secções em pré-requisitos e amostras JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- [Autenticação simbólica da aplicação Azure AD: Diretor de serviço](#service-principal-authentication)
- [Autenticação simbólica da aplicação Azure AD: Identidades geridas para recursos Azure](#managed-identity)

>[!TIP]
>Se tiver atingido um erro com o código de erro "UserErrorFailedToConnectToSqlServer" e uma mensagem como `Pooling=false` "O limite de sessão para a base de dados é XXX e foi atingido", adicione à sua cadeia de ligação e tente novamente.

### <a name="sql-authentication"></a>Autenticação do SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemplo de serviço ligado que utiliza a autenticação SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

**Senha no Cofre da Chave Azure** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

Para utilizar uma autenticação token de aplicação ad ida e volta do serviço, siga estas etapas:

1. [Crie uma aplicação azure Ative Diretório](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) a partir do portal Azure. Tome nota do nome da candidatura e dos seguintes valores que definem o serviço ligado:

    - ID da aplicação
    - Chave de aplicação
    - ID do inquilino

2. [Disponibilize um administrador de Diretório Ativo Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) para o seu Servidor Azure SQL no portal Azure se ainda não o tiver feito. O administrador da AD Azure deve ser um utilizador de Anúncios Azure ou um grupo Azure AD, mas não pode ser um diretor de serviço. Este passo é feito para que, no próximo passo, possa utilizar uma identidade Azure AD para criar um utilizador de base de dados contido para o diretor de serviço.

3. [Crie utilizadores de bases de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para o diretor de serviço. Ligue-se à base de dados a partir ou à qual pretende copiar dados utilizando ferramentas como o SQL Server Management Studio, com uma identidade Azure AD que tem pelo menos a permissão do UTILIZADOR. Executar o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Conceda ao diretor de serviço permissões necessárias, como normalmente faz para utilizadores SQL ou outros. Executar o seguinte código. Para mais opções, consulte [este documento.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Configure um serviço ligado à Base de Dados Azure SQL na Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço ligado que usa autenticação principal de serviço

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos Azure](data-factory-service-identity.md) que representa a fábrica de dados específica. Pode utilizar esta identidade gerida para autenticação de Base de Dados Azure SQL. A fábrica designada pode aceder e copiar dados de ou para a sua base de dados utilizando esta identidade.

Para utilizar a autenticação de identidade gerida, siga estes passos.

1. [Disponibilize um administrador de Diretório Ativo Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) para o seu Servidor Azure SQL no portal Azure se ainda não o tiver feito. O administrador da AD Azure pode ser um utilizador de Anúncios Azure ou um grupo Azure AD. Se conceder ao grupo uma identidade gerida um papel de administrador, ignore os passos 3 e 4. O administrador tem acesso total à base de dados.

2. [Crie utilizadores de bases de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para a identidade gerida pela Azure Data Factory. Ligue-se à base de dados a partir ou à qual pretende copiar dados utilizando ferramentas como o SQL Server Management Studio, com uma identidade Azure AD que tem pelo menos a permissão do UTILIZADOR. Executar o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Conceda a identidade gerida pela Data Factory necessárias permissões, como normalmente faz para utilizadores SQL e outros. Executar o seguinte código. Para mais opções, consulte [este documento.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Configure um serviço ligado à Base de Dados Azure SQL na Azure Data Factory.

**Exemplo**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). 

As seguintes propriedades são suportadas para o conjunto de dados da Base de Dados Azure SQL:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A **type** propriedade tipo do conjunto de dados deve ser definida para **AzureSqlTable**. | Sim |
| schema | Nome do esquema. |Não para a fonte, sim para afundar  |
| tabela | Nome da mesa/vista. |Não para a fonte, sim para afundar  |
| tableName | Nome da mesa/vista com esquema. Esta propriedade é suportada para retrocompatibilidade. Para uma nova `schema` `table`carga de trabalho, use e . | Não para a fonte, sim para afundar |

#### <a name="dataset-properties-example"></a>Exemplo de propriedades dataset

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). Esta secção fornece uma lista de propriedades suportadas pela fonte e pia da Base de Dados Azure SQL.

### <a name="azure-sql-database-as-the-source"></a>Base de Dados Azure SQL como fonte

Para copiar dados da Base de Dados Azure SQL, as seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do **tipo** da fonte de atividade de cópia deve ser definida para **AzureSqlSource**. O tipo "SqlSource" ainda é suportado para a retrocompatibilidade. | Sim |
| sqlReaderQuery | Esta propriedade utiliza a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. | Não |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê os dados da tabela de origem. A última declaração sQL deve ser uma declaração SELECT no procedimento armazenado. | Não |
| parâmetros de procedimento saqueados | Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são pares de nome ou valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. | Não |
| isolamentoN | Especifica o comportamento de bloqueio de transações para a fonte SQL. Os valores permitidos são: **ReadCommitted** (predefinido), **ReadUncommitted**, **RepeatableRead,** **Serializable**, **Snapshot**. Consulte [este doc](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para mais detalhes. | Não |

**Pontos a notar:**

- Se for especificado o **sqlReaderQuery** para **o AzureSqlSource,** a atividade de cópia executa esta consulta contra a fonte de base de dados Azure SQL para obter os dados. Também pode especificar um procedimento armazenado especificando **sqlReaderStoredProcedureName** e **storedProcedureParameters** se o procedimento armazenado levar parâmetros.
- Se não especificar o **sqlReaderQuery** ou **o sqlReaderStoredProcedureName,** as colunas definidas na secção "estrutura" do conjunto de dados JSON são usadas para construir uma consulta. A consulta `select column1, column2 from mytable` vai contra a Base de Dados Azure SQL. Se a definição de conjunto de dados não tiver "estrutura", todas as colunas são selecionadas a partir da tabela.

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

### <a name="azure-sql-database-as-the-sink"></a>Base de Dados Azure SQL como pia

> [!TIP]
> Saiba mais sobre os comportamentos de escrita suportados, configurações e boas práticas das [melhores práticas para carregar dados na Base de Dados Azure SQL](#best-practice-for-loading-data-into-azure-sql-database).

Para copiar dados para a Base de Dados Azure SQL, as seguintes propriedades são suportadas na secção de **sink da** atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade **tipo** do sumidouro da atividade de cópia deve ser fixada em **AzureSqlSink**. O tipo "SqlSink" ainda é suportado para a retrocompatibilidade. | Sim |
| escreverBatchSize | Número de linhas para inserir na tabela SQL *por lote*.<br/> O valor permitido é **inteiro** (número de linhas). Por padrão, a Azure Data Factory determina dinamicamente o tamanho adequado do lote com base no tamanho da linha. | Não |
| escreverBatchTimeout | O tempo de espera para a operação de inserção do lote terminar antes de sair.<br/> O valor permitido é **tempo de tempo.** Um exemplo é "00:30:00" (30 minutos). | Não |
| preCopyScript | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de escrever dados na Base de Dados Azure SQL. É invocado apenas uma vez por cópia. Utilize esta propriedade para limpar os dados pré-carregados. | Não |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem numa tabela-alvo. <br/>Este procedimento armazenado é *invocado por lote*. Para operações que funcionam apenas uma vez e não têm nada a `preCopyScript` ver com dados de origem, por exemplo, excluir ou truncar, utilizar a propriedade. | Não |
| storedProcedureTableTypeParâmetrometerName |O nome do parâmetro do tipo de mesa especificado no procedimento armazenado.  |Não |
| sqlWriterTableType |O nome do tipo de mesa a utilizar no procedimento armazenado. A atividade de cópia disponibiliza os dados numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são pares de nome e valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. | Não |
| tabelaOpção | Especifica se criar automaticamente a mesa do lavatório se não existir com base no esquema de origem. A criação de mesa automática não é suportada quando o lavatório especifica o procedimento armazenado ou a cópia encenada é configurada na atividade de cópia. Os valores `none` permitidos `autoCreate`são: (padrão), . |Não |
| desativarAColeção Métrica | Data Factory recolhe métricas como Azure SQL Database DTUs para otimização de desempenho de cópias e recomendações. Se estiver preocupado com este `true` comportamento, especifique para desligá-lo. | Não (padrão `false`é) |

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Boas práticas para carregar dados na Base de Dados Azure SQL

Quando copia dados para a Base de Dados Azure SQL, pode exigir um comportamento de escrita diferente:

- [Apêndice](#append-data): Os meus dados de origem têm apenas novos registos.
- [Upsert](#upsert-data): Os meus dados de origem têm inserções e atualizações.
- [Reescrever:](#overwrite-the-entire-table)Quero recarregar uma tabela de dimensões inteira de cada vez.
- [Escreva com lógica personalizada:](#write-data-with-custom-logic)Preciso de processamento extra antes da inserção final na tabela de destino.

Consulte as respetivas secções sobre como configurar na Azure Data Factory e as melhores práticas.

### <a name="append-data"></a>Dados anexados

Os dados de adesão são o comportamento padrão deste conector de base de dados Azure SQL. A Azure Data Factory faz uma inserção a granel para escrever à sua mesa de forma eficiente. Pode configurar a fonte e afundar-se em conformidade na atividade da cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção 1:** Quando tiver uma grande quantidade de dados para copiar, utilize a seguinte abordagem para fazer um upsert: 

- Em primeiro lugar, utilize uma [tabela temporária de aplicação de uma base](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) de dados para carregar a granel todos os registos utilizando a atividade de cópia. Como as operações contra as tabelas temporárias de bases de dados não estão registadas, podes carregar milhões de discos em segundos.
- Executar uma atividade de procedimento armazenado na Azure Data Factory para aplicar uma declaração [de FUSÃO](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou INSERÇÃO/ATUALIZAÇÃO. Utilize a tabela temporária como fonte para efetuar todas as atualizações ou inserções como uma única transação. Desta forma, o número de viagens de ida e volta e de operações de registo é reduzido. No final da atividade do procedimento armazenado, a tabela temporária pode ser truncada para estar pronta para o próximo ciclo de upsert.

Como exemplo, na Azure Data Factory, pode criar um pipeline com uma **atividade de Cópia** acorrentada a uma atividade de Procedimento **Armazenado**. Os dados anteriores copiam os dados da sua loja de origem numa tabela temporária da Base de Dados Azure SQL, por exemplo, **##UpsertTempTable,** como nome de mesa no conjunto de dados. Em seguida, este último invoca um procedimento armazenado para fundir os dados de origem da tabela temporária na tabela-alvo e limpar a tabela temporária.

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

Pode configurar a propriedade **préCopyScript** no afundatório de atividade de cópia. Neste caso, para cada atividade de cópia que executa, a Azure Data Factory executa primeiro o script. Em seguida, executa a cópia para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, especifique um script para primeiro apagar todos os registos antes de carregar em massa os novos dados a partir da fonte.

### <a name="write-data-with-custom-logic"></a>Escreva dados com lógica personalizada

Os passos para escrever dados com lógica personalizada são semelhantes aos descritos na secção de [dados upsert.](#upsert-data) Quando necessitar de aplicar um tratamento extra antes da inserção final de dados de origem na tabela de destino, em larga escala, pode fazer uma de duas coisas:

- Carregue para uma tabela temporária de telescópios e, em seguida, invoque um procedimento armazenado. 
- Invoque um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Invoque um procedimento armazenado a partir de um lavatório SQL

Quando copia dados para a Base de Dados Azure SQL, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais. A função de procedimento armazenado tira partido dos [parâmetros de valor de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Invocar um procedimento armazenado processa os dados de linha a linha em vez de utilizar uma operação a granel, que não recomendamos para cópiaem em larga escala. Saiba mais sobre [as melhores práticas para carregar dados na Base de Dados Azure SQL](#best-practice-for-loading-data-into-azure-sql-database).

Pode utilizar um procedimento armazenado quando os mecanismos de cópia incorporados não servem o propósito. Um exemplo é quando pretende aplicar um tratamento extra antes da inserção final de dados de origem na tabela de destino. Alguns exemplos de processamento extra são quando você quer fundir colunas, procurar valores adicionais e inserir em mais de uma tabela.

A amostra seguinte mostra como utilizar um procedimento armazenado para fazer um upsert numa tabela na Base de Dados Azure SQL. Assuma que os dados de entrada e a tabela de **marketing** do lavatório têm cada uma três colunas: **ProfileID,** **Estado**e **Categoria**. Faça o upsert com base na coluna **ProfileID** e aplique-o apenas para uma categoria específica chamada "ProductA".

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

## <a name="mapping-data-flow-properties"></a>Mapeando propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, pode ler e escrever para tabelas da Base de Dados Azure SQL. Para obter mais informações, consulte a [transformação](data-flow-source.md) de origem e a transformação do [sumidouro](data-flow-sink.md) nos fluxos de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

As definições específicas da Base de Dados SQL do Azure estão disponíveis no separador **Opções de Origem** da transformação da fonte. 

**Entrada:** Selecione se aponta a sua fonte ```Select * from <table-name>```para uma tabela (equivalente a ) ou introduza uma consulta SQL personalizada.

**Consulta**: Se selecionar Consulta no campo de entrada, introduza uma consulta SQL para a sua fonte. Esta definição substitui qualquer tabela que tenha escolhido no conjunto de dados. **Encomenda Por** cláusulas não são suportadas aqui, mas pode definir uma declaração completa SELECT FROM. Também pode utilizar funções de tabela definidas pelo utilizador. **selecionar * a partir do udfGetData é** uma UDF em SQL que devolve uma tabela. Esta consulta produzirá uma tabela de origem que pode utilizar no fluxo de dados. A utilização de consultas também é uma ótima maneira de reduzir as linhas para testes ou para procurações. 

* Exemplo SQL:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Tamanho do lote**: Introduza um tamanho de lote para encaixar grandes dados em leituras.

**Nível de isolamento**: O padrão para fontes SQL no fluxo de dados de mapeamento é lido sem compromisso. Pode alterar o nível de isolamento aqui para um destes valores:
* Ler Comprometido
* Ler não comprometido
* Leitura repetível
* Serializável
* Nenhum (ignorar o nível de isolamento)

![Nível de Isolamento](media/data-flow/isolationlevel.png "Nível de Isolamento")

### <a name="sink-transformation"></a>Transformação de pia

Definições específicas para a Base de Dados SQL Azure estão disponíveis no separador **Definições** da transformação do lavatório.

Método de **atualização:** Determina quais as operações permitidas no seu destino de base de dados. O padrão é apenas permitir inserções. Para atualizar, atualizar ou eliminar linhas, é necessária uma transformação alter-row para marcar linhas para essas ações. Para atualizações, upserts e eliminações, deve ser definida uma coluna ou colunas chave para determinar qual a linha a alterar.

![Colunas-chave](media/data-flow/keycolumn.png "Colunas-chave")

O nome da coluna que escolher como chave aqui será utilizado pela ADF como parte da atualização subsequente, upsert, eliminar. Portanto, deve escolher uma coluna que exista no mapeamento da Pia. Se desejar não escrever o valor desta coluna de teclas, clique em "Saltar as colunas de teclas".

**Ação de mesa:** Determina se recriar ou remover todas as linhas da tabela de destino antes de escrever.
* Nenhuma: nenhuma ação será feita à mesa.
* Recriar: A mesa será largada e recriada. Necessário se criar uma nova tabela dinamicamente.
* Todas as filas da mesa-alvo serão removidas.

**Tamanho do lote**: Controla quantas filas estão a ser escritas em cada balde. Tamanhos maiores do lote melhoram a compressão e a otimização da memória, mas arriscam-se a partir de exceções de memória ao cortar dados.

**Scripts Pré e Post SQL**: Introduza scripts SQL multi-line que serão executados antes (pré-processamento) e depois de (pós-processamento) os dados forem escritos na sua base de dados sink

![scripts de processamento pré e pós SQL](media/data-flow/prepost1.png "Scripts de processamento SQL")

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapeamento de tipo de dados para Base de Dados Azure SQL

Quando os dados são copiados de ou para a Base de Dados SQL azure, os seguintes mapeamentos são utilizados desde tipos de dados da Base de Dados Azure SQL para tipos de dados provisórios da Azure Data Factory. Para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia, consulte [schema e mapeamentos de tipo](copy-activity-schema-and-type-mapping.md)de dados .

| Tipo de dados da Base de Dados Azure SQL | Tipo de dados provisórios da Azure Data Factory |
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
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Para tipos de dados que mapeiam para o tipo de decimal provisório, atualmente a Azure Data Factory suporta precisão até 28. Se tiver dados com precisão superior a 28, considere converter-se numa cadeia em consulta SQL.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Obtenha propriedades de atividadede Metadados

Para saber mais detalhes sobre as propriedades, consulte [a atividade do GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
