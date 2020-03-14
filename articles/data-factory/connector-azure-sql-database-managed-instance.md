---
title: Copiar dados de e para a Base de Dados Azure SQL Caso Gerido
description: Saiba como mover dados de e para a Base de Dados Azure SQL Gerida sem parar utilizando a Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: cfa53d480120ec75623a6a372b258b63e6264f92
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136048"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Copiar dados de e para a Base de Dados Azure SQL Gerido sem utilizar a Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia na Azure Data Factory para copiar dados de e para a Instância Gerida pela Base de Dados Azure SQL. Baseia-se no artigo de [visão geral](copy-activity-overview.md) da atividade da Cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de instância gerido por base de dados Azure SQL é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)

Pode copiar dados da Base de Dados Azure SQL Managed Instance para qualquer loja de dados de sink suportado. Também pode copiar dados de qualquer loja de dados de origem suportada para a instância gerida. Para obter uma lista de lojas de dados que são suportadas como fontes e afunda-se pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector de instância gerido pela base de dados Azure SQL suporta:

- Copiar dados utilizando a autenticação SQL e o Azure Ative Directory (Azure AD) Autenticação simbólica com um diretor de serviço ou identidades geridas para recursos Azure.
- Como fonte, recuperar dados utilizando uma consulta SQL ou um procedimento armazenado.
- Como pia, anexando dados a uma tabela de destino ou invocando um procedimento armazenado com lógica personalizada durante a cópia.

>[!NOTE]
>A base de dados Azure SQL Gerida caso [sempre encriptado](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) não é suportado por este conector agora. Para trabalhar, pode utilizar um [conector ODBC genérico](connector-odbc.md) e um controlador ODBC sQL Server através de um tempo de funcionação de integração auto-hospedado. Siga [esta orientação](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) com configurações de descarregamento e cadeia de ligação do condutor DaOBC.

## <a name="prerequisites"></a>Pré-requisitos

Para aceder ao ponto final de [instância](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)de dados gerido pela Base de Dados Azure SQL, pode utilizar um prazo de execução de integração azure gerido pela Azure. Certifique-se de que ativa o ponto final público e também permite o tráfego de pontos finais públicos no grupo de segurança da rede para que a Azure Data Factory possa ligar-se à sua base de dados. Para mais informações, consulte [esta orientação.](../sql-database/sql-database-managed-instance-public-endpoint-configure.md)

Para aceder ao ponto final privado da Base de Dados Azure SQL, configurar um tempo de execução de [integração auto-hospedado](create-self-hosted-integration-runtime.md) que pode aceder à base de dados. Se fornecer o tempo de execução de integração auto-hospedado na mesma rede virtual que a sua instância gerida, certifique-se de que a sua máquina de tempo de execução de integração está numa sub-rede diferente da sua instância gerida. Se fornecer o seu tempo de execução de integração auto-hospedado numa rede virtual diferente da sua instância gerida, pode utilizar um epeering de rede virtual ou uma rede virtual para a ligação de rede virtual. Para mais informações, consulte Connect a sua aplicação à Instância Gerida pela Base de [Dados Azure SQL](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados Azure específicas do conector de instância gerida pela base de dados Azure SQL.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado à instância de instância gerida pela base de dados Azure SQL:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para **AzureSqlMI**. | Sim |
| connectionString |Esta propriedade especifica a **ligação Informação** String que é necessária para ligar à instância gerida usando a autenticação SQL. Para mais informações, consulte os seguintes exemplos. <br/>A porta predefinida é 1433. Se estiver a utilizar a Base de Dados Azure SQL Gerida com um ponto final público, especifique explicitamente a porta 3342.<br> Também pode colocar uma senha no Cofre de Chaves Azure. Se for autenticação SQL, retire a configuração `password` da cadeia de ligação. Para mais informações, consulte o exemplo jSON seguindo as credenciais da tabela e [da loja no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Sim |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim, quando se utiliza a autenticação Azure AD com um diretor de serviço |
| servicePrincipalKey | Especifique a chave da aplicação. Marque este campo como **SecureString** para o armazenar de forma segura na Azure Data Factory ou [referência a um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim, quando se utiliza a autenticação Azure AD com um diretor de serviço |
| tenant | Especifique as informações do inquilino, como o nome de domínio ou identificação do inquilino, segundo a qual reside a sua candidatura. Recupere-o pairando sobre o rato no canto superior direito do portal Azure. | Sim, quando se utiliza a autenticação Azure AD com um diretor de serviço |
| connectVia | Este tempo de execução de [integração](concepts-integration-runtime.md) é utilizado para se ligar à loja de dados. Você pode usar um tempo de funcionação de integração auto-hospedado ou um tempo de funcionação de integração Azure se a sua instância gerida tiver um ponto final público e permitir que a Azure Data Factory aceda ao mesmo. Se não for especificado, o tempo de execução de integração do Azure padrão é utilizado. |Sim |

Para tipos de autenticação diferentes, consulte as secções seguintes em pré-requisitos e exemplos de JSON, respectivamente:

- [Autenticação SQL](#sql-authentication)
- [Autenticação simbólica da aplicação Azure AD: Diretor de serviço](#service-principal-authentication)
- [Autenticação simbólica da aplicação Azure AD: Identidades geridas para recursos Azure](#managed-identity)

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

**Exemplo 2: utilize a autenticação SQL com uma palavra-passe no Cofre de Chaves Azure**

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

Para utilizar uma autenticação token de aplicação ad ida e volta do serviço, siga estas etapas:

1. Siga os passos para [fornecer um administrador de Diretório Ativo Azure para a sua Instância Gerida](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Crie uma aplicação azure Ative Diretório](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) a partir do portal Azure. Tome nota do nome da aplicação e os valores seguintes que definem o serviço ligado:

    - ID da aplicação
    - Chave da aplicação
    - ID do inquilino

3. [Crie logins](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) para a identidade gerida da Azure Data Factory. No Estúdio de Gestão de Servidores SQL (SSMS), ligue-se à sua Instância Gerida utilizando uma conta SQL Server que é uma **sisadmina**. Na base de dados **principal,** executar o seguinte T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Crie utilizadores de bases de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para a identidade gerida pela Azure Data Factory. Ligue-se à base de dados a partir ou à qual pretende copiar dados, executar o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Conceda a identidade gerida pela Data Factory necessárias permissões, como normalmente faz para utilizadores SQL e outros. Execute o seguinte código. Para mais opções, consulte [este documento.](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Configure um serviço de base de dados Azure SQL gerido por instância seleto na Fábrica de Dados Azure.

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

### <a name="managed-identity"></a>Identidades geridas para autenticação de recursos Azure

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos Azure](data-factory-service-identity.md) que representa a fábrica de dados específica. Pode utilizar esta identidade gerida para autenticação de instância gerida por dados de dados Azure SQL. A fábrica designada pode aceder e copiar dados de ou para a sua base de dados utilizando esta identidade.

Para utilizar a autenticação de identidade gerida, siga estes passos.

1. Siga os passos para [fornecer um administrador de Diretório Ativo Azure para a sua Instância Gerida](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Crie logins](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) para a identidade gerida da Azure Data Factory. No Estúdio de Gestão de Servidores SQL (SSMS), ligue-se à sua Instância Gerida utilizando uma conta SQL Server que é uma **sisadmina**. Na base de dados **principal,** executar o seguinte T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Crie utilizadores de bases de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para a identidade gerida pela Azure Data Factory. Ligue-se à base de dados a partir ou à qual pretende copiar dados, executar o seguinte T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Conceda a identidade gerida pela Data Factory necessárias permissões, como normalmente faz para utilizadores SQL e outros. Execute o seguinte código. Para mais opções, consulte [este documento.](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Configure um serviço de base de dados Azure SQL gerido por instância seleto na Fábrica de Dados Azure.

**Exemplo: utiliza autenticação de identidade gerida**

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de secções e propriedades disponíveis para utilização para definir conjuntos de dados, consulte o artigo datasets. Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados de instância gerida pela Base de Dados Azure SQL.

Para copiar dados de e para a Instância Gerida pela Base de Dados Azure SQL, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para **AzureSqlMITable**. | Sim |
| schema | Nome do esquema. |Não para a origem, Sim para o sink  |
| table | Nome da mesa/vista. |Não para a origem, Sim para o sink  |
| tableName | Nome da mesa/vista com esquema. Esta propriedade é suportada para retrocompatibilidade. Para uma nova carga de trabalho, utilize `schema` e `table`. | Não para a origem, Sim para o sink |

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

Para obter uma lista completa de secções e propriedades disponíveis para utilização para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte e pia geridas pela Base de Dados Azure SQL.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Caso gerido pela base de dados Azure SQL como fonte

Para copiar dados da Base de Dados Azure SQL Managed Instance, as seguintes propriedades são suportadas na secção de origem da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **SqlMISource**. | Sim |
| sqlReaderQuery |Esta propriedade utiliza a consulta SQL personalizada para ler dados. Um exemplo é `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Esta propriedade é o nome do procedimento armazenado que lê os dados da tabela de origem. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Estes parâmetros são para o procedimento armazenado.<br/>Valores permitidos são pares de nome ou valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |
| isolamentoN | Especifica o comportamento de bloqueio de transações para a fonte SQL. Os valores permitidos são: **ReadCommitted** (predefinido), **ReadUncommitted**, **RepeatableRead,** **Serializable**, **Snapshot**. Consulte [este doc](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para mais detalhes. | Não |

**Tenha em atenção os seguintes pontos:**

- Se o **sqlReaderQuery** for especificado para **o SqlMISource,** a atividade de cópia executa esta consulta contra a fonte de instância gerida para obter os dados. Também pode especificar um procedimento armazenado especificando **sqlReaderStoredProcedureName** e **storedProcedureParameters** se o procedimento armazenado levar parâmetros.
- Se não especificar a propriedade **sqlReaderQuery** ou **sqlReaderStoredProcedureName,** as colunas definidas na secção "estrutura" do conjunto de dados JSON são usadas para construir uma consulta. A consulta `select column1, column2 from mytable` vai contra a instância gerida. Se a definição de conjunto de dados não tiver "estrutura", todas as colunas são selecionadas a partir da tabela.

**Exemplo: Use uma consulta SQL**

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

**Exemplo: Utilize um procedimento armazenado**

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Base de Dados Azure SQL Gerida como um lavatório

> [!TIP]
> Saiba mais sobre os comportamentos de escrita suportados, configurações e boas práticas das melhores práticas para carregar dados em Caso gerido pela Base de [Dados Azure SQL](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Para copiar dados para a Instância Gerida pela Base de Dados Azure SQL, as seguintes propriedades são suportadas na secção de sink da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do sumidouro da atividade de cópia deve ser definida para **SqlMISink**. | Sim |
| writeBatchSize |Número de linhas para inserir na tabela SQL *por lote*.<br/>Os valores permitidos são inteiros para o número de linhas. Por padrão, a Azure Data Factory determina dinamicamente o tamanho adequado do lote com base no tamanho da linha.  |Não |
| writeBatchTimeout |Esta propriedade especifica o tempo de espera para que a operação de inserção do lote esteja concluída antes de sair.<br/>Os valores permitidos são para o tempo. Um exemplo é "00:30:00", que é 30 minutos. |Não |
| preCopyScript |Esta propriedade especifica uma consulta SQL para que a atividade de cópia seja executada antes de escrever dados na instância gerida. É invocado apenas uma vez por cópia. Você pode usar esta propriedade para limpar dados pré-carregados. |Não |
| sqlWriterStoredProcedureName | O nome do procedimento armazenado que define como aplicar dados de origem numa tabela-alvo. <br/>Este procedimento armazenado é *invocado por lote*. Para operações que funcionam apenas uma vez e não têm nada a ver com dados de origem, por exemplo, excluir ou truncar, utilizar a propriedade `preCopyScript`. | Não |
| storedProcedureTableTypeParâmetrometerName |O nome do parâmetro do tipo de mesa especificado no procedimento armazenado.  |Não |
| sqlWriterTableType |O nome do tipo de mesa a utilizar no procedimento armazenado. A atividade de cópia disponibiliza os dados numa tabela temporária com este tipo de tabela. O código de procedimento armazenado pode então fundir os dados que estão a ser copiados com os dados existentes. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado.<br/>Os valores permitidos são pares de nome e valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. | Não |
| tabelaOpção | Especifica se criar automaticamente a mesa do lavatório se não existir com base no esquema de origem. A criação de mesa automática não é suportada quando o lavatório especifica o procedimento armazenado ou a cópia encenada é configurada na atividade de cópia. Os valores permitidos são: `none` (padrão), `autoCreate`. |Não |

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

**Exemplo 2: Invocar um procedimento armazenado durante a cópia**

Saiba mais detalhes a partir de [Invoke um procedimento armazenado a partir de um lavatório SQL MI](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Boas práticas para carregar dados em Caso gerido pela Base de Dados Azure SQL

Quando copia dados para a Base de Dados Azure SQL Managed Instance, poderá necessitar de comportamentos de escrita diferentes:

- [Apêndice](#append-data): Os meus dados de origem têm apenas novos registos.
- [Upsert](#upsert-data): Os meus dados de origem têm inserções e atualizações.
- [Reescrever:](#overwrite-the-entire-table)Quero recarregar toda a tabela de dimensões de cada vez.
- [Escreva com lógica personalizada:](#write-data-with-custom-logic)Preciso de processamento extra antes da inserção final na tabela de destino. 

Consulte as respetivas secções para saber como configurar na Azure Data Factory e nas melhores práticas.

### <a name="append-data"></a>Dados anexados

Os dados de adesão são o comportamento padrão deste conector de instância gerido por dados de base de dados Azure SQL. A Azure Data Factory faz uma inserção a granel para escrever à sua mesa de forma eficiente. Pode configurar a fonte e afundar-se em conformidade na atividade da cópia.

### <a name="upsert-data"></a>Fazer upsert de dados

**Opção 1:** Quando tiver uma grande quantidade de dados para copiar, utilize a seguinte abordagem para fazer um upsert: 

- Em primeiro lugar, utilize uma [tabela temporária](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) para carregar a granel todos os registos utilizando a atividade de cópia. Como as operações contra mesas temporárias não estão registadas, podes carregar milhões de discos em segundos.
- Executar uma atividade de procedimento armazenado na Azure Data Factory para aplicar uma declaração [de FUSÃO](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) ou INSERÇÃO/ATUALIZAÇÃO. Utilize a tabela temporária como fonte para efetuar todas as atualizações ou inserções como uma única transação. Desta forma, o número de viagens de ida e volta e de operações de registo é reduzido. No final da atividade do procedimento armazenado, a tabela temporária pode ser truncada para estar pronta para o próximo ciclo de upsert.

Como exemplo, na Azure Data Factory, pode criar um pipeline com uma **atividade de Cópia** acorrentada a uma atividade de Procedimento **Armazenado**. Os dados anteriores copiam os dados da sua loja de origem numa tabela temporária, por exemplo, **##UpsertTempTable,** como o nome da tabela no conjunto de dados. Em seguida, este último invoca um procedimento armazenado para fundir os dados de origem da tabela temporária na tabela-alvo e limpar a tabela temporária.

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

**Opção 2:** Também pode optar por [invocar um procedimento armazenado dentro](#invoke-a-stored-procedure-from-a-sql-sink)de uma atividade de cópia . Esta abordagem executa cada linha na tabela de origem em vez de usar a inserção a granel como a abordagem padrão na atividade de cópia, o que não é apropriado para o upsert em larga escala.

### <a name="overwrite-the-entire-table"></a>Sobrepor toda a mesa

Pode configurar a propriedade **préCopyScript** num afundado de atividade de cópia. Neste caso, para cada atividade de cópia que executa, a Azure Data Factory executa primeiro o script. Em seguida, executa a cópia para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, especifique um script para primeiro apagar todos os registos antes de carregar em massa os novos dados a partir da fonte.

### <a name="write-data-with-custom-logic"></a>Escreva dados com lógica personalizada

Os passos para escrever dados com lógica personalizada são semelhantes aos descritos na secção de [dados upsert.](#upsert-data) Quando necessitar de aplicar um tratamento extra antes da inserção final de dados de origem na tabela de destino, em larga escala, pode fazer uma de duas coisas: 

- Carregue para uma mesa temporária e, em seguida, invoque um procedimento armazenado.
- Invoque um procedimento armazenado durante a cópia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Invoque um procedimento armazenado a partir de um lavatório SQL

Quando copia dados para a Instância Gerida pela Base de Dados Azure SQL, também pode configurar e invocar um procedimento armazenado especificado pelo utilizador com parâmetros adicionais. A função de procedimento armazenado tira partido dos [parâmetros de valor de tabela](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Invocar um procedimento armazenado processa os dados de linha a linha em vez de utilizar uma operação a granel, que não recomendamos para cópiaem em larga escala. Saiba mais sobre as melhores práticas para carregar dados em Caso gerido pela Base de [Dados Azure SQL](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Pode utilizar um procedimento armazenado quando os mecanismos de cópia incorporados não servem o propósito. Um exemplo é quando pretende aplicar um tratamento extra antes da inserção final de dados de origem na tabela de destino. Alguns exemplos de processamento extra são quando você quer fundir colunas, procurar valores adicionais e inserir dados em mais de uma tabela.

A amostra que se segue mostra como utilizar um procedimento armazenado para fazer um upsert numa tabela na base de dados do SQL Server. Assuma que os dados de entrada e a tabela de **marketing** do lavatório têm cada uma três colunas: **ProfileID,** **Estado**e **Categoria**. Faça o upsert com base na coluna **ProfileID** e aplique-o apenas para uma categoria específica chamada "ProductA".

1. Na sua base de dados, defina o tipo de tabela com o mesmo nome que **o sqlWriterTableType**. O esquema do tipo de mesa é o mesmo que o esquema devolvido pelos seus dados de entrada.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
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

3. Na Azure Data Factory, defina a secção **de sumidouro SQL MI** na atividade de cópia da seguinte forma:

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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapeamento de tipo de dados para instância gerida pela base de dados Azure SQL

Quando os dados são copiados de e para a Instância Gerida pela Base de Dados Azure SQL, os seguintes mapeamentos são utilizados desde os tipos de dados geridos pela Base de Dados Azure SQL para tipos de dados provisórios da Azure Data Factory. Para saber como a atividade de cópia mapeia do esquema de origem e do tipo de dados até à pia, consulte [schema e mapeamentos de tipo](copy-activity-schema-and-type-mapping.md)de dados .

| Azure SQL Base de Dados Gerida tipo de dados de instância | Tipo de dados provisórios da Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| decimal |decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Valor de duplo |
| image |Byte[] |
| int |Int32 |
| money |decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |decimal |
| nvarchar |String, Char[] |
| real |Único |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |decimal |
| sql_variant |Object |
| texto |String, Char[] |
| hora |TimeSpan |
| carimbo de data/hora |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Para tipos de dados que mapeiam para o tipo de decimal provisório, atualmente a Azure Data Factory suporta precisão até 28. Se tiver dados que requeiram precisão superior a 28, considere converter-se numa cadeia numa consulta SQL.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Obtenha propriedades de atividadede Metadados

Para saber mais detalhes sobre as propriedades, consulte [a atividade do GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
