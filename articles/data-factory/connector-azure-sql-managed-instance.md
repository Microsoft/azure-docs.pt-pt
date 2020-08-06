---
title: Copiar dados de e para Azure SQL Caso Gerido
description: Saiba como mover dados de e para a Azure SQL Managed Instance utilizando a Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 5244d9711886376fe9502e31c227d8d74de4c21a
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800386"
---
# <a name="copy-data-to-and-from-azure-sql-managed-instance-by-using-azure-data-factory"></a>Copiar dados de e para Azure SQL Caso Gerido através da Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a atividade de cópia na Azure Data Factory para copiar dados de e para Azure SQL Managed Instance. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade da Cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SQL Managed Instance é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)

Pode copiar dados da SQL Managed Instance para qualquer loja de dados de lavatórios suportados. Também pode copiar dados de qualquer loja de dados de origem suportada para a SQL Managed Instance. Para obter uma lista de lojas de dados que são suportadas como fontes e sumidouros pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector sql Managed Instance suporta:

- Copiar dados utilizando a autenticação SQL e o Azure Ative Directory (Azure AD) A autenticação de fichas de aplicação com um principal serviço ou identidades geridas para recursos Azure.
- Como fonte, recuperar dados utilizando uma consulta SQL ou um procedimento armazenado.
- Como pia, criar automaticamente a tabela de destino, se não existir com base no esquema de origem; anexar dados a uma tabela ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

>[!NOTE]
> SQL Gestd Instance [Sempre Encriptado](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) não é suportado por este conector agora. Para trabalhar, pode utilizar um [conector ODBC genérico](connector-odbc.md) e um controlador ODBC do SQL Server através de um tempo de integração auto-hospedado. Saiba mais [na secção "Sempre Encriptado".](#using-always-encrypted) 

## <a name="prerequisites"></a>Pré-requisitos

Para aceder ao ponto [final público](../azure-sql/managed-instance/public-endpoint-overview.md)sql Managed Instance, pode utilizar um tempo de integração Azure gerido pela Azure. Certifique-se de que ativa o ponto final público e também permite o tráfego de pontos finais públicos no grupo de segurança da rede para que a Azure Data Factory possa ligar-se à sua base de dados. Para mais informações, consulte [esta orientação.](../azure-sql/managed-instance/public-endpoint-configure.md)

Para aceder ao ponto final privado SQL Managed Instance, crie um [tempo de integração auto-hospedado](create-self-hosted-integration-runtime.md) que possa aceder à base de dados. Se providenciar o tempo de integração auto-hospedado na mesma rede virtual que o seu caso gerido, certifique-se de que a sua máquina de tempo de execução de integração está numa sub-rede diferente da sua instância gerida. Se providenciar o tempo de integração auto-hospedado numa rede virtual diferente da sua instância gerida, pode utilizar um espreitamento de rede virtual ou uma rede virtual para a ligação de rede virtual. Para obter mais informações, consulte [Conecte a sua aplicação à SQL Managed Instance](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Azure Data Factory específicas do conector SQL Managed Instance.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à SQL Managed Instance:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **AzureSqlMI**. | Yes |
| conexãoStragem |Esta propriedade especifica a **informação de conexão Desatalamento** que é necessária para ligar à SQL Managed Instance usando a autenticação SQL. Para mais informações, consulte os seguintes exemplos. <br/>A porta predefinida é 1433. Se estiver a utilizar a SQL Managed Instance com um ponto final público, especifique explicitamente a porta 3342.<br> Também pode colocar uma palavra-passe no Cofre da Chave Azure. Se for a autenticação SQL, retire a `password` configuração da cadeia de ligação. Para obter mais informações, consulte o exemplo JSON seguindo as credenciais da tabela e [da loja no Cofre da Chave Azure](store-credentials-in-key-vault.md). |Yes |
| servicePrincipalId | Especifique a identificação do cliente da aplicação. | Sim, quando se usa a autenticação AD AZure com um diretor de serviço |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como **SecureString** para armazená-lo de forma segura na Azure Data Factory ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). | Sim, quando se usa a autenticação AD AZure com um diretor de serviço |
| inquilino | Especifique as informações do inquilino, como o nome de domínio ou iD do inquilino, sob o qual a sua aplicação reside. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | Sim, quando se usa a autenticação AD AZure com um diretor de serviço |
| connectVia | Este [tempo de integração](concepts-integration-runtime.md) é utilizado para se ligar à loja de dados. Você pode usar um tempo de integração auto-hospedado ou um tempo de integração Azure se o seu caso gerido tiver um ponto final público e permitir que a Azure Data Factory aceda a ele. Se não for especificado, utiliza-se o tempo de execução da integração Azure predefinido. |Yes |

Para diferentes tipos de autenticação, consulte as seguintes secções sobre pré-requisitos e amostras JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- [Autenticação simbólica de aplicação Azure: Diretor de serviço](#service-principal-authentication)
- [Autenticação simbólica de aplicação Azure: Identidades geridas para recursos Azure](#managed-identity)

### <a name="sql-authentication"></a>Autenticação do SQL

**Exemplo 1: utilizar a autenticação SQL**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: utilize a autenticação SQL com uma palavra-passe no Cofre da Chave Azure**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

Para utilizar uma autenticação simbólica de aplicação Azure AD baseada em serviço, siga estes passos:

1. Siga os passos para [Provisionar um administrador do Azure Ative Directory para a sua Instância Gerida](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Crie uma aplicação Azure Ative Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) a partir do portal Azure. Tome nota do nome da aplicação e dos seguintes valores que definem o serviço ligado:

    - ID da Aplicação
    - Chave de aplicação
    - ID do inquilino

3. [Crie logins](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) para a identidade gerida da Azure Data Factory. No SQL Server Management Studio (SSMS), ligue-se à sua instância gerida utilizando uma conta SQL Server que é uma **sysadmin**. Na base de **dados principal,** execute o seguinte T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Crie utilizadores de bases de dados contidos](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) para a identidade gerida da Azure Data Factory. Ligue à base de dados a partir ou à qual pretende copiar dados, execute o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Conceder à Data Factory permissões necessárias como normalmente faz para utilizadores de SQL e outros. Executar o seguinte código. Para mais opções, consulte [este documento.](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Configure um serviço de casos geridos SQL na Azure Data Factory.

**Exemplo: utilizar a autenticação principal do serviço**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos da Azure](data-factory-service-identity.md) que representa a fábrica de dados específica. Pode utilizar esta identidade gerida para a autenticação sql Managed Instance. A fábrica designada pode aceder e copiar dados de ou para a sua base de dados utilizando esta identidade.

Para utilizar a autenticação de identidade gerida, siga estes passos.

1. Siga os passos para [Provisionar um administrador do Azure Ative Directory para a sua Instância Gerida](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Crie logins](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) para a identidade gerida da Azure Data Factory. No SQL Server Management Studio (SSMS), ligue-se à sua instância gerida utilizando uma conta SQL Server que é uma **sysadmin**. Na base de **dados principal,** execute o seguinte T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Crie utilizadores de bases de dados contidos](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) para a identidade gerida da Azure Data Factory. Ligue à base de dados a partir ou à qual pretende copiar dados, execute o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Conceder à Data Factory permissões necessárias como normalmente faz para utilizadores de SQL e outros. Executar o seguinte código. Para mais opções, consulte [este documento.](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Configure um serviço de casos geridos SQL na Azure Data Factory.

**Exemplo: utiliza a autenticação de identidade gerida**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para utilização para definir conjuntos de dados, consulte o artigo conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados sql Managed Instance.

Para copiar dados de e para a SQL Managed Instance, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para **AzureSqlMITable**. | Yes |
| esquema | O nome do esquema. |Não para a fonte, sim para a pia  |
| tabela | Nome da mesa/vista. |Não para a fonte, sim para a pia  |
| tableName | Nome da tabela/vista com esquema. Esta propriedade é suportada para retrocompatibilidade. Para nova carga de trabalho, use `schema` e `table` . | Não para a fonte, sim para a pia |

**Exemplo**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<SQL Managed Instance linked service name>",
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

Para obter uma lista completa de secções e propriedades disponíveis para uso para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte e pia sql Managed Instance.

### <a name="sql-managed-instance-as-a-source"></a>SQL Gestão de Instância como fonte

Para copiar dados da SQL Managed Instance, as seguintes propriedades são suportadas na secção fonte de origem da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida como **SqlMISource**. | Yes |
| sqlReaderQuery |Esta propriedade usa a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Esta propriedade é o nome do procedimento armazenado que lê dados da tabela de origem. A última declaração SQL deve ser uma declaração SELECT no procedimento armazenado. |No |
| parametrómetros de reserva armazenados |Estes parâmetros são para o procedimento armazenado.<br/>Os valores permitidos são pares de nomes ou valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |No |
| isolamentoLevel | Especifica o comportamento de bloqueio de transação para a fonte SQL. Os valores permitidos são: **ReadCommitted,** **ReadUncommitted,** **RepeatableRead,** **Serializable**, **Snapshot**. Se não for especificado, é utilizado o nível de isolamento predefinido da base de dados. Consulte [este doc](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para mais detalhes. | No |

**Tenha em atenção os seguintes pontos:**

- Se **o SqlReaderQuery** for especificado para **o SqlMISource,** a atividade de cópia executa esta consulta com a fonte sql Managed Instance para obter os dados. Também pode especificar um procedimento armazenado especificando **o nome sqlReaderStoredProcedureName** e **os Computadores Deprocedures armazenados** se o procedimento armazenado tiver parâmetros.
- Se não especificar a propriedade **sqlReaderQuery** ou **sqlReaderStoredProcedureName,** as colunas definidas na secção "estrutura" do conjunto de dados JSON são usadas para construir uma consulta. A consulta `select column1, column2 from mytable` vai contra a SQL Managed Instance. Se a definição de conjunto de dados não tiver "estrutura", todas as colunas são selecionadas a partir da tabela.

**Exemplo: Utilize uma consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

### <a name="sql-managed-instance-as-a-sink"></a>SQL Caso Gerido como pia

> [!TIP]
> Saiba mais sobre os comportamentos de escrita suportados, configurações e boas práticas das [melhores práticas para carregar dados em SQL Managed Instance](#best-practice-for-loading-data-into-sql-managed-instance).

Para copiar dados para a SQL Managed Instance, as seguintes propriedades são suportadas na secção de lavatório de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do lavatório de atividade de cópia deve ser definida como **SqlMISink**. | Yes |
| preCopyScript |Esta propriedade especifica uma consulta SQL para a atividade de cópia a executar antes de escrever dados em SQL Managed Instance. É invocado apenas uma vez por cópia. Pode utilizar esta propriedade para limpar dados pré-carregados. |No |
| mesaOption | Especifica se deve [criar automaticamente a tabela do lavatório](copy-activity-overview.md#auto-create-sink-tables) se não existir com base no esquema de origem. A criação de tabela automática não é suportada quando a pia especifica o procedimento armazenado. Os valores permitidos são: `none` (padrão), `autoCreate` . |No |
| sqlWriterStorEdProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem numa tabela-alvo. <br/>Este procedimento armazenado é *invocado por lote*. Para operações que funcionam apenas uma vez e não têm nada a ver com dados de origem, por exemplo, apagar ou truncar, utilize a `preCopyScript` propriedade.<br>Veja o exemplo de [Invocar um procedimento armazenado a partir de um lavatório SQL](#invoke-a-stored-procedure-from-a-sql-sink). | No |
| nome de parametrómetro de computador |O nome do parâmetro do tipo de tabela especificado no procedimento armazenado.  |No |
| SqlWriterTableType |O nome do tipo de mesa a utilizar no procedimento armazenado. A atividade de cópia torna os dados disponíveis numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |No |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são pares de nomes e valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. | No |
| escreverBatchSize |Número de linhas para inserir na tabela SQL *por lote*.<br/>Os valores permitidos são inteiros para o número de linhas. Por predefinição, a Azure Data Factory determina dinamicamente o tamanho apropriado do lote com base no tamanho da linha.  |No |
| escreverBatchTimeout |Esta propriedade especifica o tempo de espera para a operação de inserção do lote ser concluída antes do tempo de esmutar.<br/>Os valores permitidos são para o tempo. Um exemplo é "00:30:00", que é 30 minutos. |No |

**Exemplo 1: Dados do apêndice**

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
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemplo 2: Invocar um procedimento armazenado durante a cópia**

Saiba mais detalhes da [Invoke um procedimento armazenado a partir de um lavatório SQL MI](#invoke-a-stored-procedure-from-a-sql-sink).

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
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
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

## <a name="best-practice-for-loading-data-into-sql-managed-instance"></a>Melhores práticas para carregar dados em SQL Managed Instance

Ao copiar dados em SQL Managed Instance, poderá necessitar de diferentes comportamentos de escrita:

- [Apêndice:](#append-data)Os meus dados de origem têm apenas novos registos.
- [Upsert](#upsert-data): Os meus dados de origem têm inserções e atualizações.
- [Overwrite](#overwrite-the-entire-table): Quero recarregar cada vez toda a tabela de dimensões.
- [Escreva com lógica personalizada](#write-data-with-custom-logic): Preciso de processamento extra antes da inserção final na tabela de destino. 

Consulte as respetivas secções para saber como configurar na Azure Data Factory e nas melhores práticas.

### <a name="append-data"></a>Dados do apêndice

Os dados appending são o comportamento padrão do conector de pia SQL Managed Instance. A Azure Data Factory faz uma inserção a granel para escrever na sua mesa de forma eficiente. Pode configurar a fonte e afundar-se em conformidade na atividade da cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção 1:** Quando tiver uma grande quantidade de dados para copiar, pode carregar todos os registos em massa numa tabela de encenação utilizando a atividade da cópia e, em seguida, executar uma atividade de procedimento armazenada para aplicar uma declaração [DE FUSÃO](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-mi-current) ou INSERT/UPDATE numa única tomada. 

Atualmente, a atividade de cópia não suporta o carregamento de dados numa tabela temporária de base de dados. Existe uma forma avançada de o configurar com uma combinação de múltiplas atividades, consulte os [cenários de Upsert de Base de Dados SQL Otimize.](https://github.com/scoriani/azuresqlbulkupsert) Abaixo mostra uma amostra de usar uma tabela permanente como encenação.

Como exemplo, na Azure Data Factory, pode criar um oleoduto com uma **atividade Copy** acorrentada com uma atividade de **Procedimento Armazenado.** Os dados anteriores copiam dados da sua loja de origem para uma tabela de encenação de Instância Gerida Azure SQL, por exemplo, **UpsertStagingTable,** como o nome da tabela no conjunto de dados. Em seguida, este último invoca um procedimento armazenado para fundir dados de origem da tabela de paragem na tabela alvo e limpar a tabela de preparação.

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

### <a name="overwrite-the-entire-table"></a>Sobrepor toda a mesa

Pode configurar a propriedade **pré-CopyScript** numa pia de atividade de cópia. Neste caso, para cada atividade de cópia que executa, a Azure Data Factory executa primeiro o script. Em seguida, executa a cópia para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, especifique um script para primeiro eliminar todos os registos antes de carregar em massa os novos dados da fonte.

### <a name="write-data-with-custom-logic"></a>Escreva dados com lógica personalizada

Os passos para escrever dados com lógica personalizada são semelhantes aos descritos na secção [de dados upsert.](#upsert-data) Quando tiver de aplicar um tratamento extra antes da inserção final dos dados de origem na tabela de destino, pode carregar para uma tabela de encenação e, em seguida, invocar a atividade do procedimento armazenado, ou invocar um procedimento armazenado na pia da atividade de cópia para aplicar dados.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Invocar um procedimento armazenado a partir de um lavatório SQL

Ao copiar dados em SQL Managed Instance, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais em cada lote da tabela de origem. A função de procedimento armazenado tira partido dos [parâmetros valorizados da tabela](https://msdn.microsoft.com/library/bb675163.aspx).

Pode utilizar um procedimento armazenado quando os mecanismos de cópia incorporados não servem o propósito. Um exemplo é quando pretende aplicar um processamento extra antes da inserção final dos dados de origem na tabela de destino. Alguns exemplos de processamento extra são quando se pretende fundir colunas, procurar valores adicionais e inserir em mais de uma tabela.

A amostra que se segue mostra como utilizar um procedimento armazenado para fazer um upsert numa tabela na base de dados do SQL Server. Assuma que os dados de entrada e a tabela **de marketing** do lavatório têm três colunas: **ProfileID,** **Estado**e **Categoria**. Faça o upsert com base na coluna **ProfileID,** e aplique-o apenas para uma categoria específica chamada "ProductA".

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

3. Na Azure Data Factory, defina a secção **de pia SQL MI** na atividade da cópia da seguinte forma:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-sql-managed-instance"></a>Mapeamento do tipo de dados para sql gestão instância

Quando os dados são copiados de e para a SQL Managed Instance, os seguintes mapeamentos são usados desde os tipos de dados de instância gerida sql para tipos de dados provisórios da Azure Data Factory. Para saber como a atividade da cópia mapeia do esquema de origem e do tipo de dados para a pia, consulte [o Schema e os mapeamentos do tipo de dados](copy-activity-schema-and-type-mapping.md).

| Tipo de dados de instância gerida SQL | Tipo de dados provisórios da Azure Data Factory |
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

Quando copiar dados de/para Azure SQL Gerenciado Instância com [Sempre Encriptado,](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current)utilize [o conector ODBC genérico](connector-odbc.md) e o controlador ODBC do Servidor SQL através do tempo de execução de integração auto-hospedado. Este conector Azure SQL Managed Instance não suporta sempre encriptado agora. 

Mais especificamente:

1. Crie um tempo de integração auto-hospedado se não tiver um. Consulte o artigo [de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.

2. Descarregue o controlador ODBC de 64 bits para o SQL Server a partir [daqui](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server?view=azuresqldb-mi-current)e instale na máquina de tempo de execução de integração. Saiba mais sobre como este condutor funciona a partir da [utilização sempre encriptada com o controlador ODBC para o SQL Server](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current#using-the-azure-key-vault-provider).

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
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte [lojas de dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
