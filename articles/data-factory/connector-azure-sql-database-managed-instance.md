---
title: Copiar dados de e para Instância Gerenciada do Banco de Dados SQL do Azure
description: Saiba como mover dados de e para Instância Gerenciada do Banco de Dados SQL do Azure usando Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 09/09/2019
ms.openlocfilehash: e8029b957fedc07ba571b61f1211c020b706bea3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929662"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Copiar dados de e para Instância Gerenciada do Banco de Dados SQL do Azure usando Azure Data Factory

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para Instância Gerenciada do Banco de Dados SQL do Azure. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de Instância Gerenciada do Banco de Dados SQL do Azure tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Você pode copiar dados de Instância Gerenciada do Banco de Dados SQL do Azure para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer armazenamento de dados de origem com suporte para a instância gerenciada. Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, esse conector de Instância Gerenciada do Banco de Dados SQL do Azure dá suporte a:

- Copiar dados usando a autenticação do SQL e a autenticação do token de aplicativo do Azure Active Directory (Azure AD) com uma entidade de serviço ou identidades gerenciadas para recursos do Azure.
- Como fonte, recuperar dados usando uma consulta SQL ou um procedimento armazenado.
- Como coletor, acrescentar dados a uma tabela de destino ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

>[!NOTE]
>O Instância Gerenciada do Banco de Dados SQL do Azure [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) não tem suporte neste conector agora. Para solucionar o trabalho, você pode usar um [conector ODBC genérico](connector-odbc.md) e um SQL Server driver ODBC por meio de um Integration Runtime auto-hospedado. Siga [este guia](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) com as configurações de cadeia de conexão e download do driver ODBC.

>[!NOTE]
>A entidade de serviço e as autenticações de identidade gerenciada atualmente não são compatíveis com este conector. Para contornar, escolha um conector do banco de dados SQL do Azure e especifique manualmente o servidor da instância gerenciada.

## <a name="prerequisites"></a>Pré-requisitos

Para acessar o [ponto de extremidade público](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)do instância gerenciada do banco de dados SQL do Azure, você pode usar um Azure data Factory tempo de execução de integração do Azure gerenciado. Certifique-se de habilitar o ponto de extremidade público e também permitir o tráfego de ponto de extremidade público no grupo de segurança de rede para que Azure Data Factory possa se conectar ao seu banco de dados. Para obter mais informações, consulte [este guia](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Para acessar o ponto de extremidade particular Instância Gerenciada do Banco de Dados SQL do Azure, configure um [tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) que pode acessar o banco de dados. Se você provisionar o tempo de execução de integração auto-hospedado na mesma rede virtual que sua instância gerenciada, certifique-se de que seu computador de tempo de execução de integração esteja em uma sub-rede diferente da instância gerenciada. Se você provisionar o tempo de execução de integração auto-hospedado em uma rede virtual diferente da instância gerenciada, você poderá usar um emparelhamento de rede virtual ou uma rede virtual para a conexão de rede virtual. Para obter mais informações, consulte [conectar seu aplicativo ao instância gerenciada do banco de dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Azure Data Factory entidades específicas para o conector de Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado Instância Gerenciada do Banco de Dados SQL do Azure:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como **AzureSqlMI**. | Sim |
| connectionString |Esta propriedade especifica as informações de **ConnectionString** necessárias para se conectar à instância gerenciada usando a autenticação do SQL. Para obter mais informações, consulte os exemplos a seguir. <br/>A porta predefinida é 1433. Se você estiver usando Instância Gerenciada do Banco de Dados SQL do Azure com um ponto de extremidade público, especifique explicitamente a porta 3342.<br>Marque este campo como **SecureString** para armazená-lo com segurança em Azure data Factory. Você também pode colocar uma senha em Azure Key Vault. Se for a autenticação do SQL, extraia a configuração de `password` da cadeia de conexão. Para obter mais informações, consulte o exemplo de JSON após a tabela e [armazenar as credenciais em Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como **SecureString** para armazená-lo com segurança em Azure data Factory ou [fazer referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço |
| tenant | Especifique as informações do locatário, como o nome de domínio ou a ID do locatário, sob a qual seu aplicativo reside. Recupere-o passando o mouse no canto superior direito do portal do Azure. | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço |
| connectVia | Esse [Integration Runtime](concepts-integration-runtime.md) é usado para se conectar ao armazenamento de dados. Você pode usar um tempo de execução de integração auto-hospedado ou um tempo de execução de integração do Azure se sua instância gerenciada tiver um ponto de extremidade público e permitir que Azure Data Factory o acesse. Se não for especificado, o tempo de execução de integração do Azure padrão será usado. |Sim |

Para tipos de autenticação diferentes, consulte as secções seguintes em pré-requisitos e exemplos de JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- [Autenticação de token de aplicativo do Azure AD: entidade de serviço](#service-principal-authentication)
- [Autenticação de token de aplicativo do Azure AD: identidades gerenciadas para recursos do Azure](#managed-identity)

### <a name="sql-authentication"></a>Autenticação do SQL

**Exemplo 1: usar a autenticação do SQL**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: usar a autenticação do SQL com uma senha no Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

Para usar uma autenticação de token de aplicativo do Azure AD baseada na entidade de serviço, siga estas etapas:

1. Siga as etapas para [provisionar um administrador de Azure Active Directory para seu instância gerenciada](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Crie um aplicativo Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) do portal do Azure. Tome nota do nome da aplicação e os valores seguintes que definem o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do inquilino

3. [Crie logons](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) para a identidade gerenciada Azure data Factory. No SQL Server Management Studio (SSMS), conecte-se ao seu Instância Gerenciada usando uma conta de SQL Server que seja um **sysadmin**. No banco de dados **mestre** , execute o seguinte T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Crie usuários de banco de dados independente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para a identidade gerenciada Azure data Factory. Conecte-se ao banco de dados a partir do ou para o qual você deseja copiar os dados, execute o T-SQL a seguir: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Conceda à identidade gerenciada Data Factory permissões necessárias como faria normalmente para usuários do SQL e outros. Execute o seguinte código. Para obter mais opções, consulte [este documento](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Configure um serviço vinculado Instância Gerenciada do Banco de Dados SQL do Azure no Azure Data Factory.

**Exemplo: usar a autenticação de entidade de serviço**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
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

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md) que representa o data Factory específico. Você pode usar essa identidade gerenciada para Instância Gerenciada do Banco de Dados SQL do Azure autenticação. A fábrica designada pode acessar e copiar dados de ou para seu banco de dado usando essa identidade.

Para usar a autenticação de identidade gerenciada, siga estas etapas.

1. Siga as etapas para [provisionar um administrador de Azure Active Directory para seu instância gerenciada](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Crie logons](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) para a identidade gerenciada Azure data Factory. No SQL Server Management Studio (SSMS), conecte-se ao seu Instância Gerenciada usando uma conta de SQL Server que seja um **sysadmin**. No banco de dados **mestre** , execute o seguinte T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Crie usuários de banco de dados independente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para a identidade gerenciada Azure data Factory. Conecte-se ao banco de dados a partir do ou para o qual você deseja copiar os dados, execute o T-SQL a seguir: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Conceda à identidade gerenciada Data Factory permissões necessárias como faria normalmente para usuários do SQL e outros. Execute o seguinte código. Para obter mais opções, consulte [este documento](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Configure um serviço vinculado Instância Gerenciada do Banco de Dados SQL do Azure no Azure Data Factory.

**Exemplo: usa a autenticação de identidade gerenciada**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
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

Para obter uma lista completa de seções e propriedades disponíveis para uso para definir conjuntos de valores, consulte o artigo conjuntos de valores. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de Instância Gerenciada do Banco de Dados SQL do Azure DataSet.

Para copiar dados de e para Instância Gerenciada do Banco de Dados SQL do Azure, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como **AzureSqlMITable**. | Sim |
| schema | Nome do esquema. |Não para a origem, Sim para o sink  |
| table | Nome da tabela/exibição. |Não para a origem, Sim para o sink  |
| tableName | Nome da tabela/exibição com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Para uma nova carga de trabalho, use `schema` e `table`. | Não para a origem, Sim para o sink |

**Exemplo**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

Para obter uma lista completa de seções e propriedades disponíveis para uso para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela fonte de Instância Gerenciada do Banco de Dados SQL do Azure e pelo coletor.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Instância Gerenciada do Banco de Dados SQL do Azure como uma fonte

Para copiar dados de Instância Gerenciada do Banco de Dados SQL do Azure, as propriedades a seguir têm suporte na seção origem da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como **SqlMISource**. | Sim |
| sqlReaderQuery |Essa propriedade usa a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Essa propriedade é o nome do procedimento armazenado que lê os dados da tabela de origem. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Esses parâmetros são para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

**Tenha em atenção os seguintes pontos:**

- Se **sqlReaderQuery** for especificado para **SqlMISource**, a atividade de cópia executará essa consulta na origem da instância gerenciada para obter os dados. Você também pode especificar um procedimento armazenado especificando **sqlReaderStoredProcedureName** e **storedprocedureparameters** se o procedimento armazenado usar parâmetros.
- Se você não especificar a propriedade **sqlReaderQuery** ou **sqlReaderStoredProcedureName** , as colunas definidas na seção "Structure" do conjunto de dados JSON serão usadas para construir uma consulta. O `select column1, column2 from mytable` de consulta é executado na instância gerenciada. Se a definição do conjunto de dados não tiver "Structure", todas as colunas serão selecionadas na tabela.

**Exemplo: usar uma consulta SQL**

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

**Exemplo: usar um procedimento armazenado**

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Instância Gerenciada do Banco de Dados SQL do Azure como um coletor

> [!TIP]
> Saiba mais sobre os comportamentos de gravação com suporte, as configurações e as práticas recomendadas da [prática recomendada para carregar dados em instância gerenciada do banco de dados SQL do Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Para copiar dados para Instância Gerenciada do Banco de Dados SQL do Azure, as propriedades a seguir têm suporte na seção coletor de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do coletor da atividade de cópia deve ser definida como **SqlMISink**. | Sim |
| writeBatchSize |Número de linhas a serem inseridas na tabela SQL *por lote*.<br/>Os valores permitidos são inteiros para o número de linhas. Por padrão, Azure Data Factory determina dinamicamente o tamanho do lote apropriado com base no tamanho da linha.  |Não |
| writeBatchTimeout |Esta propriedade especifica o tempo de espera para a operação de inserção em lote ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são para o TimeSpan. Um exemplo é "00:30:00", que é de 30 minutos. |Não |
| preCopyScript |Esta propriedade especifica uma consulta SQL para que a atividade de cópia seja executada antes de gravar dados na instância gerenciada. Ele é invocado apenas uma vez por execução de cópia. Você pode usar essa propriedade para limpar dados pré-carregados. |Não |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem em uma tabela de destino. <br/>Esse procedimento armazenado é *invocado por lote*. Para operações que são executadas apenas uma vez e não têm nada a ver com os dados de origem, por exemplo, excluir ou truncar, use a propriedade `preCopyScript`. | Não |
| storedProcedureTableTypeParameterName |O nome do parâmetro do tipo de tabela especificado no procedimento armazenado.  |Não |
| sqlWriterTableType |O nome do tipo de tabela a ser usado no procedimento armazenado. A atividade de cópia torna os dados que estão sendo movidos disponíveis em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados que estão sendo copiados com os dados existentes. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado.<br/>Os valores permitidos são pares de nome e valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. | Não |
| tableOption | Especifica se a tabela do coletor deve ser criada automaticamente se não existir com base no esquema de origem. Não há suporte para a criação automática de tabela quando o coletor especifica o procedimento armazenado ou a cópia preparada está configurada na atividade de cópia. Os valores permitidos são: `none` (padrão), `autoCreate`. |Não |

**Exemplo 1: acrescentar dados**

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
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Exemplo 2: invocar um procedimento armazenado durante a cópia**

Saiba mais detalhes em [invocar um procedimento armazenado de um coletor de Mi do SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Prática recomendada para carregar dados em Instância Gerenciada do Banco de Dados SQL do Azure

Ao copiar dados para o Instância Gerenciada do Banco de Dados SQL do Azure, você pode exigir um comportamento de gravação diferente:

- [Append](#append-data): meus dados de origem têm apenas registros novos.
- [Upsert](#upsert-data): meus dados de origem têm inserções e atualizações.
- [Substituir](#overwrite-the-entire-table): Eu quero recarregar toda a tabela de dimensões a cada vez.
- [Gravar com lógica personalizada](#write-data-with-custom-logic): preciso de processamento extra antes da inserção final na tabela de destino. 

Consulte as respectivas seções sobre como configurar o em Azure Data Factory e as práticas recomendadas.

### <a name="append-data"></a>Acrescentar dados

Acrescentar dados é o comportamento padrão desse conector do coletor de Instância Gerenciada do Banco de Dados SQL do Azure. Azure Data Factory faz uma inserção em massa para gravar em sua tabela com eficiência. Você pode configurar a origem e o coletor de acordo com a atividade de cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção 1:** Quando você tiver uma grande quantidade de dados a serem copiados, use a seguinte abordagem para fazer um Upsert: 

- Primeiro, use uma [tabela temporária](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) para carregar em massa todos os registros usando a atividade de cópia. Como as operações em tabelas temporárias não são registradas, você pode carregar milhões de registros em segundos.
- Execute uma atividade de procedimento armazenado no Azure Data Factory para aplicar uma instrução [Merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou Insert/Update. Use a tabela temporária como a origem para executar todas as atualizações ou inserções como uma única transação. Dessa forma, o número de viagens de ida e volta e operações de log são reduzidos. No final da atividade de procedimento armazenado, a tabela temporária pode ser truncada para estar pronta para o próximo ciclo de Upsert.

Por exemplo, em Azure Data Factory, você pode criar um pipeline com uma **atividade de cópia** encadeada com uma **atividade de procedimento armazenado**. O primeiro copia os dados do armazenamento de origem em uma tabela temporária, por exemplo, **# #UpsertTempTable**, como o nome da tabela no conjunto de dados. Em seguida, o último invoca um procedimento armazenado para mesclar dados de origem da tabela temporária na tabela de destino e limpar a tabela temporária.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

No banco de dados, defina um procedimento armazenado com lógica de MESCLAgem, como o exemplo a seguir, que é apontado da atividade de procedimento armazenado anterior. Suponha que o destino seja a tabela de **marketing** com três colunas: **ProfileId**, **estado**e **categoria**. Faça o Upsert com base na coluna **ProfileId** .

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

**Opção 2:** Você também pode optar por [invocar um procedimento armazenado em uma atividade de cópia](#invoke-a-stored-procedure-from-a-sql-sink). Essa abordagem executa cada linha na tabela de origem em vez de usar BULK INSERT como a abordagem padrão na atividade de cópia, que não é apropriada para Upsert de grande escala.

### <a name="overwrite-the-entire-table"></a>Substituir a tabela inteira

Você pode configurar a propriedade **preCopyScript** em um coletor de atividade de cópia. Nesse caso, para cada atividade de cópia executada, Azure Data Factory executa o script primeiro. Em seguida, ele executa a cópia para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, especifique um script para primeiro excluir todos os registros antes de carregar em massa os novos dados da origem.

### <a name="write-data-with-custom-logic"></a>Gravar dados com lógica personalizada

As etapas para gravar dados com lógica personalizada são semelhantes às descritas na seção de [dados Upsert](#upsert-data) . Quando você precisa aplicar processamento extra antes da inserção final dos dados de origem na tabela de destino, para grande escala, você pode fazer uma das duas coisas: 

- Carregar em uma tabela temporária e, em seguida, invocar um procedimento armazenado.
- Invoque um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Invocar um procedimento armazenado de um coletor SQL

Ao copiar dados para o Instância Gerenciada do Banco de Dados SQL do Azure, você também pode configurar e invocar um procedimento armazenado especificado pelo usuário com parâmetros adicionais. O recurso de procedimento armazenado aproveita os [parâmetros com valor de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Invocar um procedimento armazenado processa a linha de dados por linha em vez de usar uma operação em massa, que não é recomendável para uma cópia em larga escala. Saiba mais na [prática recomendada para carregar dados em instância gerenciada do banco de dados SQL do Azure](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Você pode usar um procedimento armazenado quando os mecanismos de cópia internos não atenderem ao propósito. Um exemplo é quando você deseja aplicar processamento extra antes da inserção final dos dados de origem na tabela de destino. Alguns exemplos de processamento extra são quando você deseja mesclar colunas, Pesquisar valores adicionais e inserir dados em mais de uma tabela.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um Upsert em uma tabela no banco de dados SQL Server. Suponha que os dados de entrada e a tabela de **marketing** do coletor tenham três colunas: **ProfileId**, **estado**e **categoria**. Faça o Upsert com base na coluna **ProfileId** e aplique-o somente para uma categoria específica chamada "produtoA".

1. No banco de dados, defina o tipo de tabela com o mesmo nome que **sqlWriterTableType**. O esquema do tipo de tabela é o mesmo que o esquema retornado pelos dados de entrada.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. No banco de dados, defina o procedimento armazenado com o mesmo nome que **sqlWriterStoredProcedureName**. Ele manipula os dados de entrada da fonte especificada e as mescla na tabela de saída. O nome do parâmetro do tipo de tabela no procedimento armazenado é o mesmo que **TableName** definido no DataSet.

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

3. Em Azure Data Factory, defina a seção **coletor de Mi do SQL** na atividade de cópia da seguinte maneira:

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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapeamento de tipo de dados para Instância Gerenciada do Banco de Dados SQL do Azure

Quando os dados são copiados para e de Instância Gerenciada do Banco de Dados SQL do Azure, os seguintes mapeamentos são usados de Instância Gerenciada do Banco de Dados SQL do Azure tipos de dados para Azure Data Factory tipos de dados provisórios. Para saber como a atividade de cópia é mapeada do esquema de origem e do tipo de dados para o coletor, consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados Instância Gerenciada do Banco de Dados SQL do Azure | Azure Data Factory tipo de dados provisório |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Flutuante |Double |
| imagem |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Único |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| texto |String, Char[] |
| hora |TimeSpan |
| carimbo de data/hora |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Para tipos de dados que são mapeados para o tipo provisório decimal, atualmente Azure Data Factory dá suporte à precisão de até 28. Se você tiver dados que exijam precisão maior que 28, considere converter para uma cadeia de caracteres em uma consulta SQL.

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades da atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
