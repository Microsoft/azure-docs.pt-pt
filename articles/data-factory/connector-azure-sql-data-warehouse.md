---
title: Copiar dados de e para o Azure SQL Data Warehouse usando Azure Data Factory
description: Saiba como copiar dados de armazenamentos de origem com suporte para o Azure SQL Data Warehouse ou de SQL Data Warehouse para repositórios de coletor com suporte usando Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: b64bfd046a42a630e7913c45213053e84377a037
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681153"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiar dados de ou para o Azure SQL Data Warehouse usando Azure Data Factory 
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão atual](connector-azure-sql-data-warehouse.md)

Este artigo descreve como copiar dados de e para o Azure SQL Data Warehouse. Para saber mais sobre Azure Data Factory, leia o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector de SQL Data Warehouse do Azure tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com tabela de [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)

Especificamente, esse conector de SQL Data Warehouse do Azure dá suporte a essas funções:

- Copie dados usando a autenticação do SQL e a autenticação do token de aplicativo do Azure Active Directory (Azure AD) com uma entidade de serviço ou identidades gerenciadas para recursos do Azure.
- Como origem, recupere dados usando uma consulta SQL ou um procedimento armazenado.
- Como um coletor, carregue dados usando o polybase ou uma inserção em massa. Recomendamos o polybase para melhor desempenho de cópia.

> [!IMPORTANT]
> Se você copiar dados usando Azure Data Factory Integration Runtime, configure um [Firewall do Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para que os serviços do Azure possam acessar o servidor.
> Se você copiar dados usando um tempo de execução de integração auto-hospedado, configure o Firewall do SQL Server do Azure para permitir o intervalo de IP apropriado. Esse intervalo inclui o IP do computador que é usado para se conectar ao banco de dados SQL do Azure.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter o melhor desempenho, use o polybase para carregar dados no Azure SQL Data Warehouse. A seção [usar o polybase para carregar dados no Azure SQL data warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) tem detalhes. Para obter instruções com um caso de uso, consulte [carregar 1 TB no Azure SQL data warehouse menos de 15 minutos com Azure data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que definem Data Factory entidades específicas para um conector de SQL Data Warehouse do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para um serviço vinculado do Azure SQL Data Warehouse:

| Propriedade            | Descrição                                                  | Necessário                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| tipo                | A propriedade Type deve ser definida como **AzureSqlDW**.             | Sim                                                          |
| connectionString    | Especifique as informações necessárias para se conectar à instância de SQL Data Warehouse do Azure para a propriedade **ConnectionString** . <br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar a chave de entidade de serviço/senha em Azure Key Vault e, se a autenticação do SQL, extrair a configuração de `password` da cadeia de conexão. Consulte o exemplo de JSON abaixo da tabela e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim                                                          |
| servicePrincipalId  | Especifique a ID do cliente do aplicativo.                         | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| vários              | Especifique as informações do locatário (nome de domínio ou ID do locatário) em que seu aplicativo reside. Você pode recuperá-lo passando o mouse sobre o canto superior direito da portal do Azure. | Sim, quando você usa a autenticação do Azure AD com uma entidade de serviço. |
| connectVia          | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar Azure Integration Runtime ou um tempo de execução de integração auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usará o Azure Integration Runtime padrão. | Não                                                           |

Para tipos de autenticação diferentes, consulte as seguintes seções sobre pré-requisitos e exemplos de JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- Autenticação de token de aplicativo do Azure AD: [entidade de serviço](#service-principal-authentication)
- Autenticação de token de aplicativo do Azure AD: [identidades gerenciadas para recursos do Azure](#managed-identity)

>[!TIP]
>Se você clicar em erro com código de erro como "UserErrorFailedToConnectToSqlServer" e mensagem como "o limite de sessão para o banco de dados é XXX e foi atingido.", adicione `Pooling=false` à sua cadeia de conexão e tente novamente.

### <a name="sql-authentication"></a>Autenticação do SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exemplo de serviço vinculado que usa a autenticação SQL

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

**Senha no Azure Key Vault:**

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

Para usar a autenticação de token de aplicativo do Azure AD baseada na entidade de serviço, siga estas etapas:

1. **[Crie um aplicativo Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** do portal do Azure. Anote o nome do aplicativo e os seguintes valores que definem o serviço vinculado:

    - ID da aplicação
    - Chave do aplicativo
    - ID do inquilino

2. **[Provisione um administrador de Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** para o SQL Server do Azure no portal do Azure se ainda não tiver feito isso. O administrador do Azure AD pode ser um usuário do Azure AD ou um grupo do Azure AD. Se você conceder ao grupo com identidade gerenciada uma função de administrador, pule as etapas 3 e 4. O administrador terá acesso completo ao banco de dados.

3. **[Crie usuários de banco de dados independente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** para a entidade de serviço. Conecte-se ao data warehouse de ou para o qual você deseja copiar dados usando ferramentas como o SSMS, com uma identidade do Azure AD que tenha pelo menos a permissão ALTER ANY USER. Execute o T-SQL a seguir:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Conceda à entidade de serviço as permissões necessárias** como faria normalmente para usuários do SQL ou outros. Execute o código a seguir ou consulte mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se você quiser usar o polybase para carregar os dados, aprenda a [permissão de banco](#required-database-permission)de dado necessária.

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Configure um serviço vinculado do Azure SQL data warehouse** no Azure data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço vinculado que usa a autenticação de entidade de serviço

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

### <a name="managed-identity"></a>Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md) que representa a fábrica específica. Você pode usar essa identidade gerenciada para a autenticação de SQL Data Warehouse do Azure. A fábrica designada pode acessar e copiar dados de ou para seu data warehouse usando essa identidade.

Para usar a autenticação de identidade gerenciada, siga estas etapas:

1. **[Provisione um administrador de Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** para o SQL Server do Azure no portal do Azure se ainda não tiver feito isso. O administrador do Azure AD pode ser um usuário do Azure AD ou um grupo do Azure AD. Se você conceder ao grupo com identidade gerenciada uma função de administrador, pule as etapas 3 e 4. O administrador terá acesso completo ao banco de dados.

2. **[Crie usuários de banco de dados independente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** para a identidade gerenciada data Factory. Conecte-se ao data warehouse de ou para o qual você deseja copiar dados usando ferramentas como o SSMS, com uma identidade do Azure AD que tenha pelo menos a permissão ALTER ANY USER. Execute o T-SQL a seguir. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Conceda à identidade gerenciada data Factory permissões necessárias** como faria normalmente para usuários do SQL e outros. Execute o código a seguir ou consulte mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se você quiser usar o polybase para carregar os dados, aprenda a [permissão de banco](#required-database-permission)de dado necessária.

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Configure um serviço vinculado do Azure SQL data warehouse** no Azure data Factory.

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de SQL Data Warehouse do Azure.

Para copiar dados de ou para o Azure SQL Data Warehouse, há suporte para as seguintes propriedades:

| Propriedade  | Descrição                                                  | Necessário                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| tipo      | A propriedade **Type** do conjunto de conjuntos deve ser definida como **AzureSqlDWTable**. | Sim                         |
| schema | Nome do esquema. |Não para fonte, sim para coletor  |
| tabela | Nome da tabela/exibição. |Não para fonte, sim para coletor  |
| tableName | Nome da tabela/exibição com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Para uma nova carga de trabalho, use `schema` e `table`. | Não para fonte, sim para coletor |

#### <a name="dataset-properties-example"></a>Exemplo de propriedades de DataSet

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela fonte de SQL Data Warehouse do Azure e pelo coletor.

### <a name="azure-sql-data-warehouse-as-the-source"></a>SQL Data Warehouse do Azure como a origem

Para copiar dados do Azure SQL Data Warehouse, defina a propriedade **Type** na origem da atividade de cópia para **SqlDWSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade                     | Descrição                                                  | Necessário |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| tipo                         | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **SqlDWSource**. | Sim      |
| sqlReaderQuery               | Use a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. | Não       |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê os dados da tabela de origem. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. | Não       |
| storedProcedureParameters    | Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são pares de nome ou valor. Os nomes e maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não       |

### <a name="points-to-note"></a>Pontos a serem observados

- Se o **sqlReaderQuery** for especificado para **sqlsource**, a atividade de cópia executará essa consulta na fonte de SQL data warehouse do Azure para obter os dados. Ou você pode especificar um procedimento armazenado. Especifique **sqlReaderStoredProcedureName** e **storedprocedureparameters** se o procedimento armazenado usar parâmetros.
- Se você não especificar **sqlReaderQuery** ou **sqlReaderStoredProcedureName**, as colunas definidas na seção **Structure** do conjunto de dados JSON serão usadas para construir uma consulta. `select column1, column2 from mytable` é executado no SQL Data Warehouse do Azure. Se a definição do conjunto de dados não tiver a **estrutura**, todas as colunas serão selecionadas da tabela.

#### <a name="sql-query-example"></a>Exemplo de consulta SQL

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

### <a name="azure-sql-data-warehouse-as-sink"></a>SQL Data Warehouse do Azure como coletor

Para copiar dados para o Azure SQL Data Warehouse, defina o tipo de coletor na atividade de cópia como **SqlDWSink**. As propriedades a seguir têm suporte na seção **coletor** de atividade de cópia:

| Propriedade          | Descrição                                                  | Necessário                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| tipo              | A propriedade **Type** do coletor da atividade de cópia deve ser definida como **SqlDWSink**. | Sim                                           |
| allowPolyBase     | Indica se o polybase deve ser usado, quando aplicável, em vez do mecanismo BULKINSERT. <br/><br/> Recomendamos que você carregue dados em SQL Data Warehouse usando o polybase. Consulte a seção [usar o polybase para carregar dados no Azure SQL data warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter restrições e detalhes.<br/><br/>Os valores permitidos são **true** e **false** (padrão). | Não                                            |
| polyBaseSettings  | Um grupo de propriedades que pode ser especificado quando a propriedade **allowPolybase** é definida como **true**. | Não                                            |
| rejeitarvalue       | Especifica o número ou o percentual de linhas que podem ser rejeitadas antes da falha da consulta.<br/><br/>Saiba mais sobre as opções de rejeição do polybase na seção argumentos de [criar tabela externa (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Os valores permitidos são 0 (padrão), 1, 2, etc. | Não                                            |
| rejectType        | Especifica se a opção **rejeiçãovalue** é um valor literal ou um percentual.<br/><br/>Os valores permitidos são **Value** (padrão) e **Percentage**. | Não                                            |
| rejectSampleValue | Determina o número de linhas a serem recuperadas antes que o polybase recalcule a porcentagem de linhas rejeitadas.<br/><br/>Os valores permitidos são 1, 2, etc. | Sim, se **rejeitátype** for **percentual**. |
| useTypeDefault    | Especifica como tratar valores ausentes em arquivos de texto delimitados quando o polybase recupera dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção argumentos em [criar formato de arquivo externo (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Os valores permitidos são **true** e **false** (padrão).<br><br> | Não                                            |
| writeBatchSize    | Número de linhas a serem inseridas na tabela SQL **por lote**. Aplica-se somente quando o polybase não é usado.<br/><br/>O valor permitido é **inteiro** (número de linhas). Por padrão, Data Factory determina dinamicamente o tamanho do lote apropriado com base no tamanho da linha. | Não                                            |
| writeBatchTimeout | Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite. Aplica-se somente quando o polybase não é usado.<br/><br/>O valor permitido é **TimeSpan**. Exemplo: "00:30:00" (30 minutos). | Não                                            |
| preCopyScript     | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar dados no Azure SQL Data Warehouse em cada execução. Use essa propriedade para limpar os dados pré-carregados. | Não                                            |
| tableOption | Especifica se a tabela do coletor deve ser criada automaticamente se não existir com base no esquema de origem. Não há suporte para a criação de tabela automática quando a cópia preparada está configurada na atividade de cópia. Os valores permitidos são: `none` (padrão), `autoCreate`. |Não |
| disableMetricsCollection | Data Factory coleta métricas como SQL Data Warehouse DWUs para otimização e recomendações de desempenho de cópia. Se você estiver preocupado com esse comportamento, especifique `true` para desativá-lo. | Não (o padrão é `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>Exemplo de coletor de SQL Data Warehouse

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

Saiba mais sobre como usar o polybase para carregar com eficiência SQL Data Warehouse na próxima seção.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Use o polybase para carregar dados no Azure SQL Data Warehouse

Usar o [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) é uma maneira eficiente de carregar uma grande quantidade de dados no Azure SQL data warehouse com alta taxa de transferência. Você verá um grande lucro na taxa de transferência usando o polybase em vez do mecanismo de BULKINSERT padrão. Para obter instruções com um caso de uso, consulte [carregar 1 TB no Azure SQL data warehouse](v1/data-factory-load-sql-data-warehouse.md).

* Se os dados de origem estiverem no **blob do Azure, Azure data Lake Storage Gen1 ou Azure data Lake Storage Gen2**e o **formato for compatível com o polybase**, você poderá usar a atividade de cópia para invocar diretamente o polybase para permitir que o Azure SQL data warehouse receba os dados da origem. Para obter detalhes, consulte **[cópia direta usando o polybase](#direct-copy-by-using-polybase)** .
* Se o repositório de dados de origem e o formato não tiverem suporte originalmente no polybase, use a **[cópia em etapas usando](#staged-copy-by-using-polybase)** o recurso polybase. O recurso de cópia em etapas também oferece uma melhor taxa de transferência. Ele converte automaticamente os dados em formato compatível com o polybase. E armazena os dados no armazenamento de BLOBs do Azure. Em seguida, ele carrega os dados em SQL Data Warehouse.

>[!TIP]
>Saiba mais sobre [as práticas recomendadas para usar o polybase](#best-practices-for-using-polybase).

### <a name="direct-copy-by-using-polybase"></a>Cópia direta usando o polybase

SQL Data Warehouse o polybase oferece suporte direto ao blob do Azure, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2. Se os dados de origem atenderem aos critérios descritos nesta seção, use o polybase para copiar diretamente do armazenamento de dados de origem para o Azure SQL Data Warehouse. Caso contrário, use [a cópia em etapas usando o polybase](#staged-copy-by-using-polybase).

> [!TIP]
> Para copiar dados com eficiência para SQL Data Warehouse, saiba mais em [Azure data Factory torna ainda mais fácil e conveniente descobrir informações de dados ao usar data Lake Store com SQL data warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se os requisitos não forem atendidos, Azure Data Factory verificará as configurações e voltará automaticamente para o mecanismo de BULKINSERT para a movimentação de dados.

1. O **serviço vinculado de origem** é com os seguintes tipos e métodos de autenticação:

    | Tipo de armazenamento de dados de origem com suporte                             | Tipo de autenticação de origem com suporte                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | Autenticação de chave de conta, autenticação de identidade gerenciada |
    | [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md) | Autenticação do principal de serviço                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autenticação de chave de conta, autenticação de identidade gerenciada |

    >[!IMPORTANT]
    >Se o armazenamento do Azure estiver configurado com o ponto de extremidade de serviço de VNet, você deverá usar a autenticação de identidade gerenciada-consulte o [impacto de usar pontos de extremidade de serviço de vnet com o armazenamento do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Conheça as configurações necessárias em Data Factory da seção [autenticação de identidade gerenciada pelo blob do Azure](connector-azure-blob-storage.md#managed-identity) e [autenticação de identidade gerenciada por Azure data Lake Storage Gen2,](connector-azure-data-lake-storage.md#managed-identity) respectivamente.

2. O **formato de dados de origem** é de **parquet**, **Orc**ou **texto delimitado**, com as seguintes configurações:

   1. O caminho da pasta não contém o filtro curinga.
   2. O nome do arquivo está vazio ou aponta para um único arquivo. Se você especificar o nome de arquivo curinga na atividade de cópia, ele só poderá ser `*` ou `*.*`.
   3. `rowDelimiter` é **Default**, **\n**, **\r\n**ou **\r**.
   4. `nullValue` é deixado como padrão ou definida como **cadeia de caracteres vazia** (""), e `treatEmptyAsNull` é deixado como padrão ou definido como true.
   5. `encodingName` é deixado como padrão ou definido como **UTF-8**.
   6. `quoteChar`, `escapeChar`e `skipLineCount` não estão especificados. O polybase oferece suporte à linha de cabeçalho Skip, que pode ser configurada como `firstRowAsHeader` no ADF.
   7. `compression` não pode ser **nenhuma compactação**, **gzip**ou **deflate**.

3. Se sua origem for uma pasta, `recursive` na atividade de cópia deverá ser definida como true.

>[!NOTE]
>Se sua origem for uma pasta, o polybase recuperará arquivos da pasta e de todas as suas subpastas e não recuperará dados de arquivos para os quais o nome do arquivo começa com um sublinhado (_) ou um ponto final (.), conforme documentado [aqui – argumento de local](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

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
                    "type": "AzureBlobStorageReadSetting",
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

### <a name="staged-copy-by-using-polybase"></a>Cópia em etapas usando o polybase

Quando os dados de origem não atendem aos critérios na seção anterior, habilite a cópia de dados por meio de uma instância intermediária de armazenamento de BLOBs do Azure de preparo. Não pode ser o armazenamento Premium do Azure. Nesse caso, Azure Data Factory executa automaticamente as transformações nos dados para atender aos requisitos de formato de dados do polybase. Em seguida, ele usa o polybase para carregar dados em SQL Data Warehouse. Por fim, ele limpa os dados temporários do armazenamento de BLOBs. Ver [cópia faseada](copy-activity-performance.md#staged-copy) para obter detalhes sobre a cópia de dados por meio de uma instância de armazenamento de Azure Blob teste.

Para usar esse recurso, crie um [serviço vinculado do armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#linked-service-properties) que se refere à conta de armazenamento do Azure com o armazenamento de BLOBs provisório. Em seguida, especifique as propriedades `enableStaging` e `stagingSettings` para a atividade de cópia, conforme mostrado no código a seguir.

>[!IMPORTANT]
>Se o armazenamento do Azure de preparo estiver configurado com o ponto de extremidade do serviço VNet, você deverá usar a autenticação de identidade gerenciada-consulte o [impacto de usar pontos de extremidade do serviço vnet com o armazenamento do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Conheça as configurações necessárias em Data Factory da [autenticação de identidade gerenciada pelo blob do Azure](connector-azure-blob-storage.md#managed-identity).

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

## <a name="best-practices-for-using-polybase"></a>Práticas recomendadas para usar o polybase

As seções a seguir fornecem as práticas recomendadas além das mencionadas nas [práticas recomendadas para o Azure SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Permissão de banco de dados necessária

Para usar o polybase, o usuário que carrega dados em SQL Data Warehouse deve ter [permissão de "controle"](https://msdn.microsoft.com/library/ms191291.aspx) no banco de dados de destino. Uma maneira de conseguir isso é adicionar o usuário como um membro da função **db_owner** . Saiba como fazer isso na [SQL data warehouse visão geral](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Limites de tamanho de linha e de tipo de dados

As cargas do polybase são limitadas a linhas menores que 1 MB. Ele não pode ser usado para carregar para VARCHR (MAX), NVARCHAR (MAX) ou VARBINARY (MAX). Para obter mais informações, consulte [SQL data warehouse limites de capacidade de serviço](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando os dados de origem tiverem linhas maiores que 1 MB, talvez você queira dividir verticalmente as tabelas de origem em várias pequenas. Certifique-se de que o maior tamanho de cada linha não exceda o limite. As tabelas menores podem então ser carregadas usando o polybase e mescladas em conjunto no Azure SQL Data Warehouse.

Como alternativa, para dados com colunas largas, você pode usar não polybase para carregar os dados usando o ADF, desativando a configuração "permitir polybase".

### <a name="sql-data-warehouse-resource-class"></a>Classe de recurso SQL Data Warehouse

Para obter a melhor taxa de transferência possível, atribua uma classe de recurso maior ao usuário que carrega dados no SQL Data Warehouse por meio do polybase.

### <a name="polybase-troubleshooting"></a>Solução de problemas do polybase

**Carregando na coluna decimal**

Se os dados de origem estiverem em formato de texto ou em outros repositórios compatíveis com polybase (usando cópia em etapas e polybase) e contiverem um valor vazio a ser carregado em SQL Data Warehouse coluna decimal, você poderá encontrar o seguinte erro:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A solução é desmarcar a opção "**usar o padrão do tipo**" (como false) nas configurações do polybase do coletor da atividade de cópia->. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" é uma configuração nativa do polybase, que especifica como tratar valores ausentes em arquivos de texto delimitados quando o polybase recupera dados do arquivo de texto. 

**`tableName` no Azure SQL Data Warehouse**

A tabela a seguir fornece exemplos de como especificar a propriedade **TableName** no DataSet JSON. Ele mostra várias combinações de nomes de esquema e tabela.

| Esquema do BD | Nome da tabela | **TableName** Propriedade JSON               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable ou dbo. MyTable ou [dbo]. MyTable |
| dbo1      | MyTable    | dbo1. MyTable ou [dbo1]. MyTable          |
| dbo       | Minha. tabela   | [My. Table] ou [dbo]. [Minha. tabela]            |
| dbo1      | Minha. tabela   | [dbo1]. [Minha. tabela]                         |

Se você vir o erro a seguir, o problema poderá ser o valor especificado para a propriedade **TableName** . Consulte a tabela anterior para obter a maneira correta de especificar valores para a propriedade JSON **TableName** .

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Colunas com valores padrão**

Atualmente, o recurso polybase no Data Factory aceita apenas o mesmo número de colunas que na tabela de destino. Um exemplo é uma tabela com quatro colunas em que uma delas é definida com um valor padrão. Os dados de entrada ainda precisam ter quatro colunas. Um conjunto de dados de entrada de três colunas produz um erro semelhante à seguinte mensagem:

```
All columns of the table must be specified in the INSERT BULK statement.
```

O valor nulo é uma forma especial do valor padrão. Se a coluna for anulável, os dados de entrada no blob para essa coluna poderão estar vazios. Mas não pode estar faltando no conjunto de dados de entrada. O polybase insere nulo para valores ausentes no Azure SQL Data Warehouse.

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Aprenda detalhes da [transformação de origem](data-flow-source.md) e da [transformação de coletor](data-flow-sink.md) no fluxo de dados de mapeamento.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo de dados para o Azure SQL Data Warehouse

Quando você copia dados de ou para o Azure SQL Data Warehouse, os seguintes mapeamentos são usados de tipos de dados do Azure SQL Data Warehouse para Azure Data Factory tipos de dados provisórios. Consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber como a atividade de cópia mapeia o tipo de dados e o esquema de origem para o coletor.

>[!TIP]
>Consulte os [tipos de dados de tabela no Azure SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) artigo sobre tipos de dados com suporte do SQL DW e as soluções alternativas para os que não têm suporte.

| Tipo de dados SQL Data Warehouse do Azure    | Data Factory tipo de dados provisório |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binário                                | Byte []                         |
| parte                                   | Booleano                        |
| º                                  | Cadeia de caracteres, Char []                 |
| date                                  | DateTime                       |
| Horário                              | DateTime                       |
| datetime2                             | DateTime                       |
| DateTimeOffset                        | DateTimeOffset                 |
| Vírgula                               | Vírgula                        |
| Atributo FILESTREAM (varbinary (max)) | Byte []                         |
| Barra                                 | Clique                         |
| image                                 | Byte []                         |
| int                                   | Int32                          |
| gastar                                 | Vírgula                        |
| nchar                                 | Cadeia de caracteres, Char []                 |
| numeric                               | Vírgula                        |
| nvarchar                              | Cadeia de caracteres, Char []                 |
| foto                                  | Único                         |
| rowversion                            | Byte []                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Vírgula                        |
| hora                                  | Período                       |
| tinyint                               | Minuciosa                           |
| uniqueidentifier                      | GUID                           |
| varbinary                             | Byte []                         |
| varchar                               | Cadeia de caracteres, Char []                 |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades da atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores por atividade de cópia em Azure Data Factory, consulte [armazenamentos de dados e formatos com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
