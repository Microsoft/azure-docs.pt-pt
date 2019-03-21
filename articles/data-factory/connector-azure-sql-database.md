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
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: e9efe96490ea1c9351d87b5b2477474ef68fbda9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57875242"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copiar dados de ou para a base de dados do Azure SQL com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [Versão 1](v1/data-factory-azure-sql-connector.md)
> * [Versão atual](connector-azure-sql-database.md)

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para copiar dados de ou para a base de dados do Azure SQL. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo, que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de ou para a base de dados do Azure SQL para qualquer arquivo de dados de sink suportados. E pode copiar dados de qualquer arquivo de dados de origem suportada para a base de dados do Azure SQL. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks de atividade de cópia, consulte a [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector SQL Database do Azure oferece suporte a essas funções:

- Copie dados utilizando autenticação do SQL e autenticação de token de aplicação do Azure Active Directory (Azure AD) com identidades de gerido ou principal de serviço para recursos do Azure.
- Como uma origem, obter dados através de uma consulta SQL ou o procedimento armazenado.
- Como um sink, acrescentar dados a uma tabela de destino ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

Base de dados SQL do Azure [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) não é suportado agora. 

> [!IMPORTANT]
> Se copiar dados com o Azure Data Factory Integration Runtime, configure uma [firewall do servidor SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para que os serviços do Azure podem aceder ao servidor.
> Se copiar dados com um runtime de integração autoalojado, configure a firewall do servidor SQL do Azure para permitir que o intervalo IP adequado. Este intervalo inclui o IP do computador que é utilizada para ligar à base de dados do Azure SQL.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para um conector da base de dados do Azure SQL.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

Estas propriedades são suportadas para um serviço de base de dados do SQL do Azure ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade tem de ser definida como **AzureSqlDatabase**. | Sim |
| connectionString | Especificar as informações necessárias para ligar à instância de SQL Database do Azure para o **connectionString** propriedade. <br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também pode colocar a chave de principal de serviço/palavra-passe no Azure Key Vault, e se se trata de solicitação de autenticação de SQL a `password` configuração fora de cadeia de ligação. Veja o exemplo JSON abaixo da tabela e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. | Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim, ao utilizar a autenticação do Azure AD com um principal de serviço. |
| servicePrincipalKey | Especifique a chave da aplicação. Marcar esse campo como um **SecureString** armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim, ao utilizar a autenticação do Azure AD com um principal de serviço. |
| inquilino | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim, ao utilizar a autenticação do Azure AD com um principal de serviço. |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou um runtime de integração autoalojado se seu arquivo de dados está localizado numa rede privada. Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não |

Para tipos de autenticação diferentes, consulte as secções seguintes em pré-requisitos e exemplos de JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- [Autenticação do Azure AD application token: Principal de serviço](#service-principal-authentication)
- [Autenticação do Azure AD application token: identidades geridas para recursos do Azure](#managed-identity)

>[!TIP]
>Se atingir o erro com o código de erro como "UserErrorFailedToConnectToSqlServer" e a mensagem, como "o limite de sessão para a base de dados é XXX e foi atingido.", adicione `Pooling=false` para sua cadeia de ligação e tente novamente.

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

**Palavra-passe no Azure Key Vault:** 

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

1. **[Criar uma aplicação do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  do portal do Azure. Tome nota do nome da aplicação e os valores seguintes que definem o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do inquilino

1. **[Aprovisionar um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  para o seu servidor SQL do Azure no portal do Azure, se ainda não o tiver feito. O administrador do Azure AD tem de ser um utilizador do Azure AD ou o grupo do Azure AD, mas não pode ser um principal de serviço. Este passo é feito para que, no próximo passo, pode utilizar uma identidade do Azure AD para criar o principal de um utilizador de base de dados contido para o serviço.

1. **[Criar utilizadores de base de dados contido](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  para o principal de serviço. Ligar à base de dados de ou para o qual pretende copiar dados com ferramentas como o SSMS, com uma identidade do Azure AD que tenha, pelo menos, permissão de alterar qualquer utilizador. Execute o seguinte T-SQL: 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. **Conceder as permissões necessárias do principal de serviço** como faria normalmente para utilizadores SQL ou outras pessoas. Execute o seguinte código:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. **Configurar um serviço de base de dados do SQL Azure ligado** no Azure Data Factory.


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

Para utilizar a autenticação de identidade gerida, siga estes passos:

1. **Crie um grupo no Azure AD.** Torne um membro do grupo de a identidade gerida.
    
   1. Encontre a identidade de geridos de fábrica de dados do portal do Azure. Vá para a fábrica de dados **propriedades**. Copie o ID de identidade de serviço.
    
   1. Instalar o [do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) módulo. Inicie sessão com o `Connect-AzureAD` comando. Execute os seguintes comandos para criar um grupo e adicionar a identidade gerida como um membro.
      ```powershell
      $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
      Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory managed identity object ID>"
      ```
    
1. **[Aprovisionar um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  para o seu servidor SQL do Azure no portal do Azure, se ainda não o tiver feito. O administrador do Azure AD pode ser um utilizador do Azure AD ou o grupo do Azure AD. Se conceder ao grupo com a identidade gerida de uma função de administrador, ignore os passos 3 e 4. O administrador terá acesso total à base de dados.

1. **[Criar utilizadores de base de dados contido](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  para o grupo do Azure AD. Ligar à base de dados de ou para o qual pretende copiar dados com ferramentas como o SSMS, com uma identidade do Azure AD que tenha, pelo menos, permissão de alterar qualquer utilizador. Execute o seguinte T-SQL: 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

1. **Conceder as permissões necessárias do grupo do Azure AD** como faria normalmente para utilizadores SQL e outras pessoas. Por exemplo, execute o seguinte código:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

1. **Configurar um serviço de base de dados do SQL Azure ligado** no Azure Data Factory.

**Exemplo:**

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados de base de dados do Azure SQL.

Para copiar dados de ou para a base de dados do Azure SQL, defina o **tipo** propriedade do conjunto de dados para **AzureSqlTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade do conjunto de dados tem de ser definida como **AzureSqlTable**. | Sim |
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
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com a origem de base de dados do Azure SQL e de sink.

### <a name="azure-sql-database-as-the-source"></a>Base de dados de SQL do Azure como origem

Para copiar dados de base de dados do Azure SQL, defina o **tipo** propriedade na origem de atividade de cópia para **SqlSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade da origem de atividade de cópia tem de ser definida como **SqlSource**. | Sim |
| sqlReaderQuery | Utilize a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. | Não |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê dados da tabela de origem. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. | Não |
| storedProcedureParameters | Parâmetros do procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. | Não |

### <a name="points-to-note"></a>Pontos a serem observados

- Se o **sqlReaderQuery** especificado para o **SqlSource**, atividade de cópia executa esta consulta em relação à origem de SQL Database do Azure para obter os dados. Em alternativa, pode especificar um procedimento armazenado. Especifique **sqlReaderStoredProcedureName** e **storedProcedureParameters** se o procedimento armazenado utiliza parâmetros.
- Se não especificar qualquer um **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, as colunas definidas no **estrutura** secção do conjunto de dados JSON está habituado Construa uma consulta. `select column1, column2 from mytable` é executado na base de dados do Azure SQL. Se não tiver a definição do conjunto de dados do **estrutura**, todas as colunas selecionadas da tabela.

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

Para copiar dados para a base de dados do Azure SQL, defina o **tipo** propriedade da atividade de cópia de sink para **SqlSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade do coletor de atividade de cópia tem de ser definida como **SqlSink**. | Sim |
| writeBatchSize | Insere dados na tabela SQL quando atinge o tamanho do buffer **writeBatchSize**.<br/> O valor permitido é **número inteiro** (número de linhas). | Não. A predefinição é 10000. |
| writeBatchTimeout | O tempo de espera para o lote de inserção operação seja concluída antes de atingir o tempo limite.<br/> O valor permitido é **timespan**. Exemplo: "00: 30:00" (30 minutos). | Não |
| preCopyScript | Especifica uma consulta SQL para a atividade de cópia a executar antes da escrita de dados na base de dados do Azure SQL. Só é invocado uma vez por cópia executar. Use essa propriedade para limpar os dados pré-carregado. | Não |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem para uma tabela de destino. Um exemplo é fazer upserts ou transformar ao utilizar a sua própria lógica de negócios. <br/><br/>Este procedimento armazenado está **invocado por lote**. Para operações que apenas executam uma vez e não têm nada a ver com a origem de dados, utilize o `preCopyScript` propriedade. Operações de exemplo são delete e truncam. | Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado.<br/>Valores permitidos são pares nome / valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. | Não |
| sqlWriterTableType | Especifique um nome de tipo de tabela a ser utilizado no procedimento armazenado. Atividade de cópia torna os dados que está a ser movidos disponível numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados a ser copiados com os dados existentes. | Não |

> [!TIP]
> Quando copiar dados para a base de dados do Azure SQL, atividade de cópia acrescenta dados para a tabela do sink por predefinição. Para fazer uma upsert ou lógica de negócios adicionais, utilize o procedimento armazenado na **SqlSink**. Saiba mais detalhes a partir [invocar um procedimento armazenado de SQL Sink](#invoking-stored-procedure-for-sql-sink).

#### <a name="append-data-example"></a>Exemplo de dados de acréscimo

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

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>Invocar um procedimento armazenado durante a cópia de exemplo de upsert

Saiba mais detalhes a partir [invocar um procedimento armazenado de SQL Sink](#invoking-stored-procedure-for-sql-sink).

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

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade na base de dados de destino

Esta secção mostra como copiar dados de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade.

#### <a name="source-table"></a>Tabela de origem

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>Tabela de destino

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

> [!NOTE]
> A tabela de destino tem uma coluna de identidade.

#### <a name="source-dataset-json-definition"></a>Definição de JSON do conjunto de dados de origem

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

#### <a name="destination-dataset-json-definition"></a>Definição de JSON do conjunto de dados de destino

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

> [!NOTE]
> A tabela de origem e destino têm esquema diferente. 

O destino tem uma coluna adicional com uma identidade. Neste cenário, tem de especificar o **estrutura** propriedade na definição de conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Invocar um procedimento armazenado do sink do SQL

Quando copiar dados na base de dados do Azure SQL, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais.

Pode usar um procedimento armazenado quando os mecanismos de cópia interna não atendem a finalidade. Normalmente, são utilizados quando um upsert, insert e update ou processamento extra deve ser feito antes da inserção de final de origem de dados na tabela de destino. Alguns exemplos de processamento extra são colunas de intercalação, procura de valores adicionais e a inserção em mais de uma tabela.

O exemplo a seguir mostra como usar um procedimento armazenado para fazer um upsert numa tabela na base de dados do Azure SQL. Partem do princípio de que dados de entrada e o sink **Marketing** cada tabela têm três colunas: **ProfileID**, **estado**, e **categoria**. Fazer o upsert com base na **ProfileID** coluna e aplicá-la apenas para uma categoria específica.

#### <a name="output-dataset"></a>Conjunto de dados de saída

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

Definir o **SqlSink** secção na atividade de cópia:

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

Na sua base de dados, definir o tipo de tabela com o mesmo nome que o **sqlWriterTableType**. O esquema do tipo de tabela deve ser a mesmo que o esquema devolvido pelos seus dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

A funcionalidade de procedimento armazenado aproveita [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapeamento do tipo de dados de base de dados do Azure SQL

Quando copia dados de ou para a base de dados do Azure SQL, os seguintes mapeamentos são utilizados entre tipos de dados do Azure SQL Database para tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados de base de dados SQL do Azure | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| bigint |Int64 |
| binário |Byte[] |
| bit |Booleano |
| char |Cadeia de caracteres, Char [] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| decimal |decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Flutuante |Valor de duplo |
| image |Byte[] |
| int |Int32 |
| dinheiro |decimal |
| nchar |Cadeia de caracteres, Char [] |
| ntext |Cadeia de caracteres, Char [] |
| numérico |decimal |
| nvarchar |Cadeia de caracteres, Char [] |
| real |Único |
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |decimal |
| sql_variant |Object |
| texto |Cadeia de caracteres, Char [] |
| hora |Período de tempo |
| carimbo de data/hora |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |Cadeia de caracteres, Char [] |
| xml |Xml |

>[!NOTE]
> Para mapas de tipos de dados para o tipo Decimal de provisório, atualmente ADF suporta precisão até 28. Se tiver dados com precisão maior que 28, considere converter a cadeia de caracteres na consulta SQL.

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, atividade de cópia no Azure Data Factory, veja [arquivos de dados e formatos suportados](copy-activity-overview.md##supported-data-stores-and-formats).
