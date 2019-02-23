---
title: Copiar dados de e para o Azure SQL Data Warehouse com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de arquivos de origem suportada para o Azure SQL Data Warehouse ou do SQL Data Warehouse para lojas de sink suportado com o Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: jingwang
ms.openlocfilehash: c6421ef5eda45002d8fa9daeee4789cbbc4daedd
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670263"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiar dados de ou para o Azure SQL Data Warehouse com o Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [Version1 ](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão atual](connector-azure-sql-data-warehouse.md)

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para copiar dados de ou para o Azure SQL Data Warehouse. Ele se baseia no [descrição geral da atividade de cópia](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do Azure SQL Data Warehouse para qualquer arquivo de dados de sink suportados. E pode copiar dados de qualquer arquivo de dados de origem suportada para o Azure SQL Data Warehouse. Para obter uma lista dos arquivos de dados que são suportados como origens ou sinks de atividade de cópia, consulte a [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Azure SQL Data Warehouse oferece suporte a essas funções:

- Copie dados utilizando autenticação do SQL e autenticação de token de aplicação do Azure Active Directory (Azure AD) com identidades de gerido ou principal de serviço para recursos do Azure.
- Como uma origem, obter dados através de uma consulta SQL ou o procedimento armazenado.
- Como um sink, inserir dados de carga com o PolyBase ou num volume. Recomendamos que o PolyBase para um melhor desempenho de cópia.

> [!IMPORTANT]
> Tenha em atenção que o PolyBase suporta apenas a autenticação do SQL mas a autenticação não do Azure AD.

> [!IMPORTANT]
> Se copiar dados com o Azure Data Factory Integration Runtime, configure uma [firewall do servidor SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para que os serviços do Azure podem aceder ao servidor.
> Se copiar dados com um runtime de integração autoalojado, configure a firewall do servidor SQL do Azure para permitir que o intervalo IP adequado. Este intervalo inclui o IP do computador que é utilizada para ligar à base de dados do Azure SQL.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter melhor desempenho, utilize o PolyBase para carregar dados para o Azure SQL Data Warehouse. O [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) secção tem detalhes. Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que definem as entidades do Data Factory específicas para um conector do Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para um serviço ligado do Azure SQL Data Warehouse:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo deve ser definida como **AzureSqlDW**. | Sim |
| connectionString | Especifique as informações necessárias para ligar à instância do armazém de dados SQL do Azure para o **connectionString** propriedade. <br/>Marca esse campo como uma SecureString armazena de forma segura no Data Factory. Também pode colocar a chave de principal de serviço/palavra-passe no Azure Key Vault, e se se trata de solicitação de autenticação de SQL a `password` configuração fora de cadeia de ligação. Veja o exemplo JSON abaixo da tabela e [Store credenciais no Azure Key Vault](store-credentials-in-key-vault.md) artigo com mais detalhes. | Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim, ao utilizar a autenticação do Azure AD com um principal de serviço. |
| servicePrincipalKey | Especifique a chave da aplicação. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim, ao utilizar a autenticação do Azure AD com um principal de serviço. |
| inquilino | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim, ao utilizar a autenticação do Azure AD com um principal de serviço. |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou um runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não |

Para tipos de autenticação diferentes, consulte as secções seguintes em pré-requisitos e exemplos de JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- Autenticação do Azure AD application token: [Principal de serviço](#service-principal-authentication)
- Autenticação do Azure AD application token: [Identidades geridas para os recursos do Azure](#managed-identity)

>[!TIP]
>Se atingir o erro com o código de erro como "UserErrorFailedToConnectToSqlServer" e a mensagem, como "o limite de sessão para a base de dados é XXX e foi atingido.", adicione `Pooling=false` para sua cadeia de ligação e tente novamente.

### <a name="sql-authentication"></a>Autenticação do SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemplo de serviço ligado que utilize a autenticação SQL

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Para utilizar a autenticação de token de aplicação de serviço baseada em principais do Azure AD, siga estes passos:

1. **[Criar uma aplicação do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  do portal do Azure. Tome nota do nome da aplicação e os valores seguintes que definem o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do inquilino

1. **[Aprovisionar um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  para o seu servidor SQL do Azure no portal do Azure, se ainda não o tiver feito. O administrador do Azure AD pode ser um utilizador do Azure AD ou o grupo do Azure AD. Se conceder ao grupo com a identidade gerida de uma função de administrador, ignore os passos 3 e 4. O administrador terá acesso total à base de dados.

1. **[Criar utilizadores de base de dados contido](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  para o principal de serviço. Ligar ao armazém de dados de ou para o qual pretende copiar dados com ferramentas como o SSMS, com uma identidade do Azure AD que tenha, pelo menos, permissão de alterar qualquer utilizador. Execute o seguinte T-SQL:
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. **Conceder as permissões necessárias do principal de serviço** como faria normalmente para utilizadores SQL ou outras pessoas. Execute o seguinte código:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. **Configurar um serviço ligado do Azure SQL Data Warehouse** no Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço ligado que utilize autenticação do principal de serviço

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Pode ser associada uma fábrica de dados com um [identidade de recursos do Azure gerida](data-factory-service-identity.md) que representa a fábrica específica. Pode utilizar esta identidade gerida para a autenticação do Azure SQL Data Warehouse. A fábrica designada pode aceder e do armazém de dados de cópia de ou para os seus dados ao utilizar esta identidade.

> [!IMPORTANT]
> Tenha em atenção que o PolyBase não é atualmente suportado para a autenticação de identidade gerida.

Para utilizar a autenticação de identidade gerida, siga estes passos:

1. **Crie um grupo no Azure AD.** Torne um membro do grupo de a identidade gerida.

    1. Encontre a identidade de geridos de fábrica de dados do portal do Azure. Vá para a fábrica de dados **propriedades**. Copie o ID de identidade de serviço.

    1. Instalar o [do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) módulo. Inicie sessão com o `Connect-AzureAD` comando. Execute os seguintes comandos para criar um grupo e adicionar a identidade gerida como um membro.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory managed identity object ID>"
    ```

1. **[Aprovisionar um administrador do Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  para o seu servidor SQL do Azure no portal do Azure, se ainda não o tiver feito.

1. **[Criar utilizadores de base de dados contido](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  para o grupo do Azure AD. Ligar ao armazém de dados de ou para o qual pretende copiar dados com ferramentas como o SSMS, com uma identidade do Azure AD que tenha, pelo menos, permissão de alterar qualquer utilizador. Execute o seguinte T-SQL. 
    
    ```sql
    CREATE USER [your Azure AD group name] FROM EXTERNAL PROVIDER;
    ```

1. **Conceder as permissões necessárias do grupo do Azure AD** como faria normalmente para utilizadores SQL e outras pessoas. Por exemplo, execute o seguinte código.

    ```sql
    EXEC sp_addrolemember [role name], [your Azure AD group name];
    ```

1. **Configurar um serviço ligado do Azure SQL Data Warehouse** no Azure Data Factory.

**Exemplo:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados do Azure SQL Data Warehouse.

Para copiar dados de ou para o Azure SQL Data Warehouse, defina o **tipo** propriedade do conjunto de dados para **AzureSqlDWTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade do conjunto de dados tem de ser definida como **AzureSqlDWTable**. | Sim |
| tableName | O nome da tabela ou vista na instância do Azure SQL Data Warehouse que o serviço ligado refere-se a. | Não para a origem, Sim para o sink |

#### <a name="dataset-properties-example"></a>Exemplo de propriedades do conjunto de dados

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o Azure SQL Data Warehouse origem e sink.

### <a name="azure-sql-data-warehouse-as-the-source"></a>O Azure SQL Data Warehouse como a origem

Para copiar dados do Azure SQL Data Warehouse, defina o **tipo** propriedade na origem de atividade de cópia para **SqlDWSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade da origem de atividade de cópia tem de ser definida como **SqlDWSource**. | Sim |
| sqlReaderQuery | Utilize a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. | Não |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê dados da tabela de origem. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. | Não |
| storedProcedureParameters | Parâmetros do procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. | Não |

### <a name="points-to-note"></a>Pontos a serem observados

- Se o **sqlReaderQuery** especificado para o **SqlSource**, a atividade de cópia executa esta consulta em relação à origem de armazém de dados SQL do Azure para obter os dados. Em alternativa, pode especificar um procedimento armazenado. Especifique a **sqlReaderStoredProcedureName** e **storedProcedureParameters** se o procedimento armazenado utiliza parâmetros.
- Se não especificar qualquer um **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, as colunas definidas no **estrutura** secção do conjunto de dados JSON está habituado Construa uma consulta. `select column1, column2 from mytable` é executado no armazém de dados SQL do Azure. Se não tiver a definição do conjunto de dados do **estrutura**, todas as colunas selecionadas da tabela.

#### <a name="sql-query-example"></a>Exemplo de consulta de SQL

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

#### <a name="stored-procedure-example"></a>Exemplo de procedimento armazenado

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

### <a name="azure-sql-data-warehouse-as-sink"></a> O Azure SQL Data Warehouse como sink

Para copiar dados para o Azure SQL Data Warehouse, defina o tipo de sink na atividade de cópia para **SqlDWSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | O **tipo** propriedade do coletor de atividade de cópia tem de ser definida como **SqlDWSink**. | Sim |
| allowPolyBase | Indica se deve utilizar o PolyBase, quando aplicável, em vez do mecanismo BULKINSERT. <br/><br/> Recomendamos que carrega dados para o SQL Data Warehouse com o PolyBase. Consulte a [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) secção para as restrições e detalhes.<br/><br/>Valores permitidos são **True** e **falso** (predefinição).  | Não |
| polyBaseSettings | Um grupo de propriedades que pode ser especificada quando o **allowPolybase** estiver definida como **verdadeiro**. | Não |
| rejectValue | Especifica o número ou porcentagem das linhas que pode ser rejeitada antes da consulta falha.<br/><br/>Saiba mais sobre opções de rejeição do PolyBase na seção argumentos [criar tabela externa (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Valores permitidos são 0 (predefinição), 1, 2, etc. |Não |
| rejectType | Especifica se o **rejectValue** opção é um valor literal ou uma percentagem.<br/><br/>Valores permitidos são **valor** (predefinição) e **percentagem**. | Não |
| rejectSampleValue | Determina o número de linhas para obter antes de PolyBase recalcula a porcentagem das linhas rejeitadas.<br/><br/>Valores permitidos são 1, 2, etc. | Sim, se o **rejectType** é **percentagem**. |
| useTypeDefault | Especifica como lidar com valores em falta nos ficheiros de texto delimitado quando PolyBase obtém dados a partir do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade da secção argumentos na [criar ficheiro de formato externo (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Valores permitidos são **True** e **falso** (predefinição). | Não |
| writeBatchSize | Insere dados na tabela SQL quando atinge o tamanho do buffer **writeBatchSize**. Aplica-se apenas quando não é utilizado o PolyBase.<br/><br/>O valor permitido é **número inteiro** (número de linhas). | Não. A predefinição é 10000. |
| writeBatchTimeout | Tempo para a operação de inserção de lote ser concluídas antes de atingir o tempo limite de espera. Aplica-se apenas quando não é utilizado o PolyBase.<br/><br/>O valor permitido é **timespan**. Exemplo: "00: 30:00" (30 minutos). | Não |
| preCopyScript | Especifica uma consulta SQL para a atividade de cópia a executar antes da escrita de dados para o Azure SQL Data Warehouse em cada execução. Use essa propriedade para limpar os dados pré-carregado. | Não | (#repeatability durante-cópia). | Uma instrução de consulta. | Não |

#### <a name="sql-data-warehouse-sink-example"></a>Exemplo de sink do SQL Data Warehouse

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

Saiba mais sobre como utilizar o PolyBase para carregar com eficiência o SQL Data Warehouse na próxima seção.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Utilize o PolyBase para carregar dados para o Azure SQL Data Warehouse

Usando [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) é uma maneira eficiente para carregar uma grande quantidade de dados para o Azure SQL Data Warehouse com um débito elevado. Verá um ganho de grandes dimensões no débito ao utilizar o PolyBase, em vez do mecanismo BULKINSERT predefinido. Ver [referência de desempenho](copy-activity-performance.md#performance-reference) para uma comparação detalhada. Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse](https://docs.microsoft.com/azure/data-factory/v1/data-factory-load-sql-data-warehouse).

* Se a sua origem de dados está no armazenamento de Blobs do Azure ou do Azure Data Lake Store e o formato é compatível com o PolyBase, direto de cópia para o Azure SQL Data Warehouse com o PolyBase. Para obter detalhes, consulte  **[direcionar cópia com o PolyBase](#direct-copy-by-using-polybase)**.
* Se seu arquivo de dados de origem e o formato originalmente não é suportada pelo PolyBase, utilize o **[cópia faseada através do PolyBase](#staged-copy-by-using-polybase)** em vez disso, a funcionalidade. A funcionalidade de cópia faseada também oferece melhor débito. Converte automaticamente os dados em formato compatível com o PolyBase. E ele armazena os dados no armazenamento de Blobs do Azure. Em seguida, carrega os dados para o SQL Data Warehouse.

> [!IMPORTANT]
> Tenha em atenção que o PolyBase não é atualmente suportado para a autenticação de token de aplicação baseada em MSI do Azure AD.

### <a name="direct-copy-by-using-polybase"></a>Copiar direto utilizando o PolyBase

SQL Data Warehouse PolyBase suporta diretamente o Blob do Azure e Azure Data Lake Store. Utiliza o principal de serviço como uma origem e tem requisitos de formato de ficheiro específico. Se a sua fonte de dados cumprir os critérios descritos nesta secção, utilize o PolyBase para copiar direta do arquivo de dados de origem para o Azure SQL Data Warehouse. Caso contrário, utilize [cópia faseada através do PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Para copiar dados com eficiência do Data Lake Store para o SQL Data Warehouse, saiba mais a partir da [do Azure Data Factory torna ainda mais fácil e conveniente para descobrir informações a partir dos dados ao utilizar o Data Lake Store com o SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se não forem cumpridos os requisitos, o Azure Data Factory verifica as definições e vai automaticamente para o mecanismo BULKINSERT para o movimento de dados.

1. O **serviço ligado de origem** é de tipo de armazenamento de Blobs do Azure (**AzureBLobStorage**/**AzureStorage**) com autenticação de chave de conta ou o Azure Data Lake Armazenamento Gen1 (**AzureDataLakeStore**) com a autenticação do principal de serviço.
2. O **conjunto de dados de entrada** é do tipo **AzureBlob** ou **AzureDataLakeStoreFile**. O tipo de formato sob `type` propriedades é **OrcFormat**, **ParquetFormat**, ou **TextFormat**, com as seguintes configurações:

   1. `fileName` não contém o filtro de carateres universais.
   2. `rowDelimiter` tem de ser **\n**.
   3. `nullValue` é definido **vazio a cadeia de caracteres** ("") ou à esquerda como padrão, e `treatEmptyAsNull` é deixado como predefinido ou definido como true.
   4. `encodingName` está definido como **utf-8**, que é o valor predefinido.
   5. `escapeChar`, `quoteChar` e `skipLineCount` não estão especificados. Suporte de PolyBase ignorar a linha de cabeçalho que pode ser configurada como `firstRowAsHeader` no ADF.
   6. `compression` pode ser **sem compressão**, **GZip**, ou **Deflate**.

    ```json
    "typeProperties": {
        "folderPath": "<blobpath>",
        "format": {
            "type": "TextFormat",
            "columnDelimiter": "<any delimiter>",
            "rowDelimiter": "\n",
            "nullValue": "",
            "encodingName": "utf-8",
            "firstRowAsHeader": <any>
        },
        "compression": {
            "type": "GZip",
            "level": "Optimal"
        }
    },
    ```

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
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
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Cópia faseada através do PolyBase

Quando os dados de origem não cumprem os critérios na secção anterior, ative a dados a copiar através de uma instância de armazenamento de Blobs do Azure teste provisória. Não pode ser armazenamento Premium do Azure. Neste caso, o Azure Data Factory executa automaticamente a transformações nos dados para cumprir os requisitos de formato de dados do PolyBase. Em seguida, ele utiliza o PolyBase para carregar dados para o SQL Data Warehouse. Por fim, ele limpa seus dados temporários do armazenamento de Blobs. Ver [cópia faseada](copy-activity-performance.md#staged-copy) para obter detalhes sobre a cópia de dados por meio de uma instância de armazenamento de Blobs do Azure teste.

Para utilizar esta funcionalidade, crie uma [serviço ligado do armazenamento do Azure](connector-azure-blob-storage.md#linked-service-properties) que se refere à conta de armazenamento do Azure com o armazenamento de BLOBs intermediárias. Em seguida, especifique a `enableStaging` e `stagingSettings` propriedades para a atividade de cópia conforme mostrado no seguinte código:

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

## <a name="best-practices-for-using-polybase"></a>Melhores práticas para utilizar o PolyBase

As secções seguintes fornecem as práticas recomendadas para além dos mencionados [melhores práticas do Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Permissão de base de dados necessária

Para utilizar o PolyBase, o utilizador que carrega dados para o SQL Data Warehouse tem de ter [permissão de "Controlo"](https://msdn.microsoft.com/library/ms191291.aspx) na base de dados de destino. Uma forma de conseguir isso é adicionar o utilizador como membro dos **db_owner** função. Saiba como fazer isso no [descrição geral do SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Limites de tipo de tamanho de linha e de dados

Carregamentos do PolyBase estão limitados a linhas mais pequenas do que 1 MB. Eles não é possível carregar a VARCHR(MAX), nvarchar (Max) ou varbinary (Max). Para obter mais informações, consulte [limites de capacidade de serviço do SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando os dados de origem tem linhas superiores a 1 MB, pode querer verticalmente dividir as tabelas de origem em vários pequenas. Certifique-se de que o maior tamanho de cada linha não excede o limite. As tabelas mais pequenas, em seguida, podem ser carregadas com o PolyBase e mescladas no Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do SQL Data Warehouse

Para obter o melhor débito possível, atribua uma classe de recursos maior para o usuário que carrega dados para o SQL Data Warehouse através do PolyBase.

### <a name="tablename-in-azure-sql-data-warehouse"></a>**tableName** no Azure SQL Data Warehouse

A tabela seguinte fornece exemplos de como especificar a **tableName** propriedade no conjunto de dados JSON. Ela mostra várias combinações de nomes de esquema e de tabela.

| Esquema da BD | Nome da tabela | **tableName** propriedade JSON |
| --- | --- | --- |
| dbo | MyTable | MyTable ou dbo. MyTable ou [dbo]. [MyTable] |
| dbo1 | MyTable | dbo1. MyTable ou [dbo1]. [MyTable] |
| dbo | My.Table | [My.Table] ou [dbo]. [My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

Se vir o seguinte erro, o problema poderá ser o valor especificado para o **tableName** propriedade. Consulte a tabela anterior para a maneira correta de especificar valores para o **tableName** propriedade JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colunas com valores predefinidos

Atualmente, a funcionalidade de PolyBase no Data Factory aceita apenas o mesmo número de colunas como na tabela de destino. Um exemplo é uma tabela com quatro colunas em que uma delas é definida com um valor predefinido. Os dados de entrada ainda tem de ter quatro colunas. Um conjunto de dados de entrada de três colunas gera um erro semelhante à mensagem seguinte:

```
All columns of the table must be specified in the INSERT BULK statement.
```

O valor NULL é uma forma especial do valor predefinido. Se a coluna pode ser nula, os dados de entrada no blob para essa coluna podem estar vazios. Mas não pode estar em falta no conjunto de dados de entrada. O PolyBase insere NULL para valores em falta no Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo de dados do Azure SQL Data Warehouse

Quando copia dados de ou para o Azure SQL Data Warehouse, os seguintes mapeamentos são utilizados entre tipos de dados do Azure SQL Data Warehouse para tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do armazém de dados SQL do Azure | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| bigint | Int64 |
| binário | Byte[] |
| bit | Booleano |
| char | Cadeia de caracteres, Char [] |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| decimal | decimal |
| Atributo FILESTREAM (varbinary(max)) | Byte[] |
| Flutuante | Valor de duplo |
| image | Byte[] |
| int | Int32 |
| dinheiro | decimal |
| nchar | Cadeia de caracteres, Char [] |
| ntext | Cadeia de caracteres, Char [] |
| numérico | decimal |
| nvarchar | Cadeia de caracteres, Char [] |
| real | Único |
| ROWVERSION | Byte[] |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | decimal |
| sql_variant | Object |
| texto | Cadeia de caracteres, Char [] |
| hora | Período de tempo |
| carimbo de data/hora | Byte[] |
| tinyint | Byte |
| uniqueidentifier | GUID |
| varbinary | Byte[] |
| varchar | Cadeia de caracteres, Char [] |
| xml | Xml |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, atividade de cópia no Azure Data Factory, veja [arquivos de dados e formatos suportados](copy-activity-overview.md##supported-data-stores-and-formats).
