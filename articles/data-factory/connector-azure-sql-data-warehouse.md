---
title: Copiar e transformar dados em Azure Synapse Analytics
description: Saiba como copiar dados de e para a Azure Synapse Analytics e transforme dados no Azure Synapse Analytics utilizando a Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/25/2020
ms.openlocfilehash: 7fb1560fb9be809d816dde7dd69f1ec8afe5649f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417565"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiar e transformar dados em Azure Synapse Analytics (anteriormente Azure SQL Data Warehouse) utilizando a Azure Data Factory 

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão atual](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para a Azure Synapse Analytics, e usar o Data Flow para transformar dados em Azure Data Lake Storage Gen2. Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para o Azure SQL Data Warehouse, e usar o Data Flow para transformar dados no Azure Data Lake Storage Gen2. Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de e para a Azure Synapse Analytics, e usar o Data Flow para transformar dados em Azure Data Lake Storage Gen2. Para conhecer a Azure Data Factory, leia o [artigo introdutório.](introduction.md)

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Azure Synapse Analytics é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com tabela [de matriz de origem/pia suportada](copy-activity-overview.md)
- [Mapeando o fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)

Para a atividade da Cópia, este conector Azure Synapse Analytics suporta estas funções:

- Copiar dados utilizando a autenticação SQL e o Azure Ative Directory (Azure AD) A autenticação simbólica com um diretor de serviço ou identidades geridas para recursos Azure.
- Como fonte, recupere os dados utilizando uma consulta SQL ou um procedimento armazenado.
- Como pia, carregue os dados utilizando a declaração [PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) ou [COPY](#use-copy-statement) (pré-visualização) ou a inserção a granel. Recomendamos a declaração PolyBase ou COPY (pré-visualização) para um melhor desempenho da cópia.

> [!IMPORTANT]
> Se copiar dados utilizando o Tempo de Execução de Integração da Fábrica de Dados Do Azure, configure uma [firewall de servidor Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para que os serviços Do Azure possam aceder ao servidor.
> Se copiar dados utilizando um tempo de execução de integração auto-hospedado, configure a firewall do servidor Azure SQL para permitir o intervalo IP apropriado. Esta gama inclui o IP da máquina que é usado para ligar ao Azure Synapse Analytics.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter o melhor desempenho, utilize a PolyBase para carregar dados para a Azure Synapse Analytics. A [Utilização polyBase para carregar dados na secção Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) tem detalhes. Para um passeio com uma caixa de utilização, consulte [a Carregue 1 TB no Azure Synapse Analytics em menos de 15 minutos com](load-azure-sql-data-warehouse.md)a Azure Data Factory .

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que definem entidades da Fábrica de Dados específicas de um conector Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para um serviço ligado à Azure Synapse Analytics:

| Propriedade            | Descrição                                                  | Necessário                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| tipo                | A propriedade tipo deve ser definida para **AzureSqlDW**.             | Sim                                                          |
| conexãoString    | Especifique as informações necessárias para ligar à instância Azure Synapse Analytics para a **propriedade de ligaçãoString.** <br/>Marque este campo como um SecureString para o armazenar de forma segura na Data Factory. Também pode colocar a chave principal de senha/serviço no Cofre de Chaves Azure, e se for autenticação SQL, retire a `password` configuração da cadeia de ligação. Veja o exemplo jSON abaixo da tabela e [guarde as credenciais no](store-credentials-in-key-vault.md) artigo Azure Key Vault com mais detalhes. | Sim                                                          |
| serviçoPrincipalId  | Especifique a identificação do cliente do pedido.                         | Sim, quando utilizar a autenticação Azure AD com um diretor de serviço. |
| serviçoPrincipalKey | Especifique a chave da aplicação. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim, quando utilizar a autenticação Azure AD com um diretor de serviço. |
| inquilino              | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Sim, quando utilizar a autenticação Azure AD com um diretor de serviço. |
| connectVia          | O tempo de [integração](concepts-integration-runtime.md) a ser utilizado para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou um tempo de execução de integração auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | Não                                                           |

Para diferentes tipos de autenticação, consulte as seguintes secções em pré-requisitos e amostras JSON, respectivamente:

- [Autenticação do SQL](#sql-authentication)
- Autenticação simbólica da aplicação Azure AD: [Diretor de serviço](#service-principal-authentication)
- Autenticação simbólica da aplicação Azure AD: [Identidades geridas para recursos Azure](#managed-identity)

>[!TIP]
>Se atingir um erro com código de erro como "UserErrorFailedToConnectToSqlServer" e mensagem como "O `Pooling=false` limite de sessão para a base de dados é XXX e foi atingido."", adicione à sua cadeia de ligação e tente novamente.

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

**Palavra-passe no Cofre de Chaves Azure:**

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

Para utilizar a autenticação token da aplicação ad ida e principal baseada no serviço, siga estas etapas:

1. **[Crie uma aplicação azure Ative Diretório](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** a partir do portal Azure. Tome nota do nome da candidatura e dos seguintes valores que definem o serviço ligado:

    - ID da aplicação
    - Chave de aplicação
    - ID do inquilino

2. **[Disponibilize um administrador de Diretório Ativo Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** para o seu servidor Azure SQL no portal Azure se ainda não o tiver feito. O administrador da AD Azure pode ser um utilizador de Anúncios Azure ou um grupo Azure AD. Se conceder ao grupo uma identidade gerida um papel de administrador, ignore os passos 3 e 4. O administrador terá acesso total à base de dados.

3. **[Crie utilizadores de bases de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** para o diretor de serviço. Ligue-se ao armazém de dados a partir ou ao qual pretende copiar dados utilizando ferramentas como SSMS, com uma identidade Azure AD que tenha pelo menos a alteração da permissão do UTILIZADOR. Executar o seguinte T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Conceda ao diretor de serviço permissões necessárias,** como normalmente faz para utilizadores SQL ou outros. Executar o seguinte código, ou consulte mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se pretender utilizar a PolyBase para carregar os dados, aprenda a [permissão de base de dados necessária](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Configure um serviço ligado à Azure Synapse Analytics** na Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exemplo de serviço ligado que usa autenticação principal de serviço

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

Uma fábrica de dados pode ser associada a uma [identidade gerida para os recursos Azure](data-factory-service-identity.md) que representa a fábrica específica. Pode utilizar esta identidade gerida para autenticação Azure Synapse Analytics. A fábrica designada pode aceder e copiar dados de ou para o seu armazém de dados utilizando esta identidade.

Para utilizar a autenticação de identidade gerida, siga estes passos:

1. **[Disponibilize um administrador de Diretório Ativo Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** para o seu servidor Azure SQL no portal Azure se ainda não o tiver feito. O administrador da AD Azure pode ser um utilizador de Anúncios Azure ou um grupo Azure AD. Se conceder ao grupo uma identidade gerida um papel de administrador, ignore os passos 3 e 4. O administrador terá acesso total à base de dados.

2. **[Crie utilizadores de bases de dados contidos](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** para a Identidade Gerida da Fábrica de Dados. Ligue-se ao armazém de dados a partir ou ao qual pretende copiar dados utilizando ferramentas como SSMS, com uma identidade Azure AD que tenha pelo menos a alteração da permissão do UTILIZADOR. Executar o seguinte T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Conceder a Identidade Gerida da Fábrica de **Dados necessária permissões** como normalmente faz para utilizadores SQL e outros. Executar o seguinte código, ou consulte mais opções [aqui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se pretender utilizar a PolyBase para carregar os dados, aprenda a [permissão de base de dados necessária](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Configure um serviço ligado à Azure Synapse Analytics** na Azure Data Factory.

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

| Propriedade  | Descrição                                                  | Necessário                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| tipo      | A propriedade do **tipo** do conjunto de dados deve ser definida para **AzureSqlDWTable**. | Sim                         |
| schema | Nome do esquema. |Não para a fonte, sim para afundar  |
| tabela | Nome da mesa/vista. |Não para a fonte, sim para afundar  |
| tableName | Nome da mesa/vista com esquema. Esta propriedade é suportada para retrocompatibilidade. Para uma nova `schema` `table`carga de trabalho, use e . | Não para a fonte, sim para afundar |

#### <a name="dataset-properties-example"></a>Exemplo de propriedades dataset

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

## <a name="copy-activity-properties"></a>Propriedades de Copy Activity

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte e pia azure Synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics como fonte

Para copiar dados do Azure Synapse Analytics, delineie a propriedade do **tipo** na fonte de Copy Activity para **SqlDWSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da Atividade de Cópia:

| Propriedade                     | Descrição                                                  | Necessário |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| tipo                         | A propriedade do **tipo** da fonte de atividade de cópia deve ser definida para **SqlDWSource**. | Sim      |
| sqlReaderQuery               | Utilize a consulta SQL personalizada para ler dados. Exemplo: `select * from MyTable`. | Não       |
| sqlReaderStoredProcedureName | O nome do procedimento armazenado que lê os dados da tabela de origem. A última declaração sQL deve ser uma declaração SELECT no procedimento armazenado. | Não       |
| parâmetros de procedimento saqueados    | Parâmetros para o procedimento armazenado.<br/>Os valores permitidos são pares de nome ou valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. | Não       |
| isolamentoN | Especifica o comportamento de bloqueio de transações para a fonte SQL. Os valores permitidos são: **ReadCommitted** (predefinido), **ReadUncommitted**, **RepeatableRead,** **Serializable**, **Snapshot**. Consulte [este doc](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) para mais detalhes. | Não |

**Exemplo: utilização de consulta SQL**

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

**Procedimento armazenado por amostras:**

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

![Opções de cópia de sumidouro SQL DW](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Utilizar polyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Utilizar a declaração COPY (pré-visualização)](#use-copy-statement)
- Use inserção a granel

A forma mais rápida e escalável de carregar os dados é através da [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) ou da [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (pré-visualização).

Para copiar dados para o Azure SQL Data Warehouse, delineie o tipo de pia em Copy Activity para **SqlDWSink**. As seguintes propriedades são suportadas na secção **de sink da** Copy Activity:

| Propriedade          | Descrição                                                  | Necessário                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| tipo              | A propriedade **tipo** do lavatório Copy Activity deve ser definida para **SqlDWSink**. | Sim                                           |
| permitir a PolyBase     | Indica se usar a PolyBase para carregar dados no Armazém de Dados SQL. `allowCopyCommand`e `allowPolyBase` não pode ser verdade. <br/><br/>Consulte [a Utilização da PolyBase para carregar dados na secção Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter constrangimentos e detalhes.<br/><br/>Os valores permitidos são **verdadeiros** e **falsos** (padrão). | Não.<br/>Aplicar quando utilizar a PolyBase.     |
| poliBaseSettings  | Um grupo de propriedades que podem `allowPolybase` ser especificadas quando a propriedade está definida para **verdade**. | Não.<br/>Aplicar quando utilizar a PolyBase. |
| permitirCopyCommand | Indica se deve utilizar a [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (pré-visualização) para carregar dados no Armazém de Dados SQL. `allowCopyCommand`e `allowPolyBase` não pode ser verdade. <br/><br/>Consulte a declaração do Use COPY para carregar dados na secção [Azure SQL Data Warehouse](#use-copy-statement) para obter constrangimentos e detalhes.<br/><br/>Os valores permitidos são **verdadeiros** e **falsos** (padrão). | Não.<br>Aplicar quando utilizar o COPY. |
| copiarDefinições de Comando | Um grupo de propriedades que `allowCopyCommand` podem ser especificadas quando a propriedade está definida para TRUE. | Não.<br/>Aplicar quando utilizar o COPY. |
| escreverBatchSize    | Número de linhas para inserções na tabela SQL **por lote**.<br/><br/>O valor permitido é **inteiro** (número de linhas). Por padrão, a Data Factory determina dinamicamente o tamanho adequado do lote com base no tamanho da linha. | Não.<br/>Aplicar quando utilizar a inserção a granel.     |
| escreverBatchTimeout | Aguarde o tempo para que a operação de inserção do lote termine antes de sair.<br/><br/>O valor permitido é **tempo de tempo.** Exemplo: "00:30:00" (30 minutos). | Não.<br/>Aplicar quando utilizar a inserção a granel.        |
| preCopyScript     | Especifique uma consulta SQL para a Copy Activity executar antes de escrever dados no Azure SQL Data Warehouse em cada execução. Utilize esta propriedade para limpar os dados pré-carregados. | Não                                            |
| tabelaOpção | Especifica se criar automaticamente a mesa do lavatório se não existir com base no esquema de origem. A criação de mesa automática não é suportada quando a cópia encenada é configurada na atividade da cópia. Os valores `none` permitidos `autoCreate`são: (padrão), . |Não |
| desativarAColeção Métrica | Data Factory recolhe métricas como SQL Data Warehouse DWUs para otimização de desempenho de cópias e recomendações. Se estiver preocupado com este `true` comportamento, especifique para desligá-lo. | Não (padrão `false`é) |

#### <a name="sql-data-warehouse-sink-example"></a>Exemplo de pia do Armazém de Dados SQL

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

A utilização [da PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) é uma forma eficiente de carregar uma grande quantidade de dados no Azure Synapse Analytics com alta saída. Verá um grande ganho na entrada utilizando a PolyBase em vez do mecanismo de inserção por grosso. Para um walkthrough com uma caixa de utilização, consulte [a Carregar 1 TB no Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

* Se os seus dados de origem estiverem em **Azure Blob, Azure Data Lake Storage Gen1 ou Azure Data Lake Storage Gen2**, e o **formato é compatível com a PolyBase,** pode utilizar a atividade de cópia para invocar diretamente a PolyBase para permitir que o Azure SQL Data Warehouse retire os dados da fonte. Para mais detalhes, consulte **[a cópia direta utilizando o PolyBase](#direct-copy-by-using-polybase)**.
* Se a sua loja de dados de origem e formato não for originalmente suportado pela PolyBase, utilize a cópia Encenada utilizando a função **[PolyBase.](#staged-copy-by-using-polybase)** A função de cópia encenada também lhe proporciona uma melhor entrada. Converte automaticamente os dados em formato compatível com a PolyBase, armazena os dados no armazenamento Do Blob.e, em seguida, chama a PolyBase para carregar dados em SQL Data Warehouse.

>[!TIP]
>Saiba mais sobre [as melhores práticas para utilizar o PolyBase](#best-practices-for-using-polybase).

As seguintes definições polyBase são suportadas em atividade `polyBaseSettings` de cópia:

| Propriedade          | Descrição                                                  | Necessário                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejeitarValue       | Especifica o número ou percentagem de linhas que podem ser rejeitadas antes da consulta falhar.<br/><br/>Saiba mais sobre as opções de rejeição da PolyBase na secção Argumentos DA [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Os valores permitidos são 0 (padrão), 1, 2, etc. | Não                                            |
| rejeitarType        | Especifica se a opção **rejectValue** é um valor literal ou uma percentagem.<br/><br/>Os valores permitidos são **Valor** (padrão) e **Percentagem**. | Não                                            |
| rejeitarSampleValue | Determina o número de linhas a recuperar antes que a PolyBase recalcule a percentagem de linhas rejeitadas.<br/><br/>Os valores permitidos são 1, 2, etc. | Sim, se o **rejeitarType** for **percentual**. |
| useTypeDefault    | Especifica como lidar com valores em falta em ficheiros de texto delimitados quando a PolyBase recupera dados do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade a partir da secção Argumentos em FORMATO DE [ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Os valores permitidos são **verdadeiros** e **falsos** (padrão).<br><br> | Não                                            |

### <a name="direct-copy-by-using-polybase"></a>Cópia direta utilizando o PolyBase

SQL Data Warehouse PolyBase suporta diretamente Azure Blob, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2. Se os seus dados de origem cumprirem os critérios descritos nesta secção, utilize a PolyBase para copiar diretamente da loja de dados de origem para a Azure Synapse Analytics. Caso contrário, utilize [a cópia encenada utilizando a PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Para copiar dados de forma eficiente para o SQL Data Warehouse, saiba mais com [a Azure Data Factory torna ainda mais fácil e conveniente descobrir insights a partir de dados ao utilizar data lake store com SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se os requisitos não forem cumpridos, a Azure Data Factory verifica as definições e volta automaticamente ao mecanismo BULKINSERT para o movimento de dados.

1. O serviço ligado à **fonte está com** os seguintes tipos e métodos de autenticação:

    | Tipo de loja de dados de origem suportada                             | Tipo de autenticação de origem suportada                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | Autenticação chave da conta, autenticação de identidade gerida |
    | [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md) | Autenticação do principal de serviço                            |
    | [Armazenamento do Azure Data Lake Ger2](connector-azure-data-lake-storage.md) | Autenticação chave da conta, autenticação de identidade gerida |

    >[!IMPORTANT]
    >Se o seu Armazenamento Azure estiver configurado com ponto final de serviço VNet, deve utilizar a autenticação de identidade gerida - consulte o [Impacto da utilização de pontos finais de serviço VNet com armazenamento Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Conheça as configurações necessárias na Fábrica de Dados da [Azure Blob - autenticação de identidade gerida](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2 - gerido sitia](connector-azure-data-lake-storage.md#managed-identity) de autenticação de identidade respectivamente.

2. O **formato** de dados de origem é de **Parquet,** **ORC,** ou **texto delimitado,** com as seguintes configurações:

   1. O caminho das pastas não contém filtro wildcard.
   2. O nome do ficheiro está vazio, ou aponta para um único ficheiro. Se especificar o nome do ficheiro wildcard `*` na `*.*`atividade da cópia, só pode ser ou .
   3. `rowDelimiter`é **predefinido**, **\n,** **\r\n,** ou **\r**.
   4. `nullValue`é deixado como padrão ou definido para `treatEmptyAsNull` a corda **vazia** (""), e é deixado como padrão ou definido como verdadeiro.
   5. `encodingName`é deixado como padrão ou definido para **utf-8**.
   6. `quoteChar`E `escapeChar`não `skipLineCount` estão especificados. Linha de cabeçalho de salto de `firstRowAsHeader` suporte polyBase, que pode ser configurada como em ADF.
   7. `compression`pode não ser **compressão,** **GZip,** ou **Esvaziar**.

3. Se a sua fonte `recursive` for uma pasta, a atividade da cópia deve ser definida como verdadeira.

4. `wildcardFolderPath`E não `additionalColumns` estão especificados. `wildcardFilename` `modifiedDateTimeStart` `modifiedDateTimeEnd`

>[!NOTE]
>Se a sua fonte for uma pasta, note que polyBase recupera ficheiros da pasta e de todas as suas subpastas, e não recupera dados de ficheiros para os quais o nome do ficheiro começa com um sublinhado (_) ou um período (.), como documentado [aqui - ARGUMENTO LOCALIZAÇÃO](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

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

### <a name="staged-copy-by-using-polybase"></a>Cópia encenada usando polyBase

Quando os seus dados de origem não forem compatíveis com a PolyBase, ative a cópia de dados através de uma instância provisória de armazenamento Azure Blob (não pode ser o Armazenamento Premium Azure). Neste caso, a Azure Data Factory converte automaticamente os dados para satisfazer os requisitos de formato de dados da PolyBase. Em seguida, invoca a PolyBase para carregar dados no Armazém de Dados SQL. Finalmente, limpa os seus dados temporários a partir do armazenamento de bolhas. Consulte [a cópia encenada](copy-activity-performance-features.md#staged-copy) para obter detalhes sobre a cópia dos dados através de uma instância de armazenamento de Azure Blob.

Para utilizar esta funcionalidade, crie um serviço ligado ao [Armazenamento Azure Blob](connector-azure-blob-storage.md#linked-service-properties) que se refira à conta de armazenamento Azure com o armazenamento de blob provisório. Em seguida, especifique as `enableStaging` propriedades e propriedades `stagingSettings` para a Atividade de Cópia, conforme mostrado no seguinte código.

>[!IMPORTANT]
>Se a sua encenação o Armazenamento Azure estiver configurado com o ponto final do serviço VNet, deve utilizar a autenticação de identidade gerida - consulte o [Impacto da utilização de pontos finais de serviço VNet com armazenamento Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Aprenda as configurações necessárias na Fábrica de Dados a partir de [Azure Blob - autenticação de identidade gerida](connector-azure-blob-storage.md#managed-identity).

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

### <a name="best-practices-for-using-polybase"></a>Boas práticas para usar a PolyBase

As seguintes secções proporcionam as melhores práticas, para além das mencionadas nas [Melhores Práticas para a Azure Synapse Analytics.](../synapse-analytics/sql/best-practices-sql-pool.md)

#### <a name="required-database-permission"></a>Autorização de base de dados necessária

Para utilizar a PolyBase, o utilizador que carrega dados no SQL Data Warehouse deve ter [permissão "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) na base de dados do alvo. Uma forma de o conseguir é adicionar o utilizador como membro do papel **db_owner.** Saiba como fazê-lo na visão geral do [SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Tamanho da linha e limites do tipo de dados

As cargas polyBase limitam-se a linhas inferiores a 1 MB. Não pode ser utilizado para carregar para VARCHR (MAX), NVARCHAR (MAX) ou VARBINARY (MAX). Para mais informações, consulte os limites de capacidade de serviço do [SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando os seus dados de origem têm linhas superiores a 1 MB, é melhor dividir verticalmente as tabelas de origem em várias pequenas. Certifique-se de que o maior tamanho de cada linha não exceda o limite. As tabelas mais pequenas podem então ser carregadas utilizando a PolyBase e fundidas em Azure Synapse Analytics.

Em alternativa, para dados com colunas tão largas, pode utilizar a não-PolyBase para carregar os dados utilizando a ADF, desligando a definição de "permitir a PolyBase".

#### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do Armazém de Dados SQL

Para obter a melhor entrada possível, atribua uma classe de recursos maior ao utilizador que carrega dados para o Armazém de Dados SQL via PolyBase.

#### <a name="polybase-troubleshooting"></a>Resolução de problemas da PolyBase

**Carregar para a coluna Decimal**

Se os seus dados de origem estiverem em formato de texto ou em outras lojas não compatíveis com a PolyBase (utilizando cópia encenada e PolyBase), e contiver valor vazio a ser carregado na coluna Decimal do Armazém de Dados SQL, poderá atingir o seguinte erro:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

A solução é desseleccionar a opção **"Utilizar**o tipo padrão " (como falso) na sumidouro da atividade da cópia - > definições polyBase. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" é uma configuração nativa da PolyBase, que especifica como lidar com valores em falta em ficheiros de texto delimitados quando a PolyBase recupera dados do ficheiro de texto. 

**`tableName`em Azure Synapse Analytics**

A tabela que se segue dá exemplos de como especificar a propriedade **tableName** no conjunto de dados JSON. Mostra várias combinações de esquemas e nomes de mesa.

| DB Schema | Nome da tabela | **nome da tabela** Propriedade JSON               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable ou dbo. MyTable ou [dbo]. [MyTable] |
| dbo1      | MyTable    | dbo1. MyTable ou [dbo1]. [MyTable]          |
| dbo       | A minha.mesa   | [My.Table] ou [dbo]. [My.Table]            |
| dbo1      | A minha.mesa   | [dbo1]. [My.Table]                         |

Se vir o seguinte erro, o problema pode ser o valor especificado para a propriedade **tableName.** Consulte a tabela anterior para especificar os valores da propriedade JSON com nome de **quadro.**

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Colunas com valores predefinidos**

Atualmente, a funcionalidade PolyBase na Data Factory aceita apenas o mesmo número de colunas que na tabela-alvo. Um exemplo é uma tabela com quatro colunas onde uma delas é definida com um valor padrão. Os dados de entrada ainda precisam de ter quatro colunas. Um conjunto de dados de entrada de três colunas produz um erro semelhante ao seguinte mensagem:

```
All columns of the table must be specified in the INSERT BULK statement.
```

O valor NULO é uma forma especial do valor predefinido. Se a coluna for anulada, os dados de entrada na bolha para essa coluna podem estar vazios. Mas não pode faltar no conjunto de dados de entrada. PolyBase insere NULO para valores em falta no Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>Utilize a declaração copy para carregar dados no Armazém de Dados Azure SQL (pré-visualização)

A [declaração](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) de cópia do Armazém de Dados SQL (pré-visualização) suporta diretamente os dados de carregamento do **Azure Blob e do Azure Data Lake Storage Gen2**. Se os seus dados de origem cumprirem os critérios descritos nesta secção, pode optar por utilizar a declaração copy na ADF para carregar dados no Armazém de Dados Azure SQL. A Azure Data Factory verifica as definições e falha a execução da atividade de cópia se os critérios não forem cumpridos.

>[!NOTE]
>Atualmente, a Data Factory apenas suporta cópia a partir de fontes compatíveis com a declaração de CÓPIA mencionadas abaixo.

A utilização da declaração COPY suporta a seguinte configuração:

1. O **serviço e formato ligados à fonte** estão com os seguintes tipos e métodos de autenticação:

    | Tipo de loja de dados de origem suportada                             | Formato suportado           | Tipo de autenticação de origem suportada                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Blob do Azure](connector-azure-blob-storage.md)                | [Texto delimitado](format-delimited-text.md)             | Autenticação chave de conta, autenticação de assinatura de acesso partilhado, autenticação do principal de serviço, autenticação de identidade gerida |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Autenticação da chave de conta, autenticação de assinatura de acesso partilhado |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Autenticação da chave de conta, autenticação de assinatura de acesso partilhado |
    | [Armazenamento do Azure Data Lake Ger2](connector-azure-data-lake-storage.md) | [Texto delimitado](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Autenticação chave de conta, autenticação do principal de serviço, autenticação de identidade gerida |

    >[!IMPORTANT]
    >Se o seu Armazenamento Azure estiver configurado com ponto final de serviço VNet, deve utilizar a autenticação de identidade gerida - consulte o [Impacto da utilização de pontos finais de serviço VNet com armazenamento Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Conheça as configurações necessárias na Fábrica de Dados da [Azure Blob - autenticação de identidade gerida](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2 - gerido sitia](connector-azure-data-lake-storage.md#managed-identity) de autenticação de identidade respectivamente.

2. As definições de formato são com as seguintes:

   1. Para o `compression` **Parquet:** não pode haver **compressão,** **Snappy**ou **GZip**.
   2. Para **ORC:** `compression` não pode **```zlib```** haver **compressão,** ou **Snappy**.
   3. Para **texto delimitado:**
      1. `rowDelimiter`é explicitamente definido como **um único personagem** ou "**\r\n**", o valor padrão não é suportado.
      2. `nullValue`é deixado como padrão ou definido para **cadeia vazia** ("").
      3. `encodingName`é deixado como padrão ou definido para **utf-8 ou utf-16**.
      4. `escapeChar`deve ser `quoteChar`o mesmo que , e não está vazio.
      5. `skipLineCount`é deixado como padrão ou definido para 0.
      6. `compression`não pode ser **compressão** ou **GZip**.

3. Se a sua fonte `recursive` for uma pasta, a atividade da cópia deve ser definida como verdadeira.

4. `wildcardFolderPath`E não `additionalColumns` estão especificados. `wildcardFilename` `modifiedDateTimeStart` `modifiedDateTimeEnd`

As seguintes definições de `allowCopyCommand` declaração COPY são suportadas em atividade seleção:

| Propriedade          | Descrição                                                  | Necessário                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Especifica os valores predefinidos para cada coluna-alvo em DW SQL.  Os valores predefinidos na propriedade sobressaem o conjunto de restrições PADRÃO definidos no armazém de dados, e a coluna de identidade não pode ter um valor predefinido. | Não |
| opções adicionais | Opções adicionais que serão passadas para a declaração sQL DW COPY diretamente na cláusula "With" na [declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Cite o valor conforme necessário para alinhar com os requisitos de declaração copy. | Não |

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


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Obtenha propriedades de atividadede Metadados

Para saber mais detalhes sobre as propriedades, consulte [a atividade do GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo de dados para Armazém de Dados Azure SQL

## <a name="mapping-data-flow-properties"></a>Mapeando propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, pode ler e escrever para tabelas da Azure Synapse Analytics. Para obter mais informações, consulte a [transformação](data-flow-source.md) de origem e a transformação do [sumidouro](data-flow-sink.md) nos fluxos de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

As definições específicas do Azure Synapse Analytics estão disponíveis no separador **Opções De Origem** da transformação da fonte. 

**Entrada:** Selecione se aponta a sua fonte ```Select * from <table-name>```para uma tabela (equivalente a ) ou introduza uma consulta SQL personalizada.

**Consulta**: Se selecionar Consulta no campo de entrada, introduza uma consulta SQL para a sua fonte. Esta definição substitui qualquer tabela que tenha escolhido no conjunto de dados. **Encomenda Por** cláusulas não são suportadas aqui, mas pode definir uma declaração completa SELECT FROM. Também pode utilizar funções de tabela definidas pelo utilizador. **selecionar * a partir do udfGetData é** uma UDF em SQL que devolve uma tabela. Esta consulta produzirá uma tabela de origem que pode utilizar no fluxo de dados. A utilização de consultas também é uma ótima maneira de reduzir as linhas para testes ou para procurações. 

* Exemplo SQL:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Tamanho do lote**: Introduza um tamanho de lote para encaixar grandes dados em leituras. Nos fluxos de dados, a ADF utilizará esta definição para definir o cachede colunar Spark. Este é um campo de opção que utilizará os defeitos da Spark se ficar em branco.

**Nível de isolamento**: O padrão para fontes SQL no fluxo de dados de mapeamento é lido sem compromisso. Pode alterar o nível de isolamento aqui para um destes valores:
* Ler Comprometido
* Ler não comprometido
* Leitura repetível
* Serializável
* Nenhum (ignorar o nível de isolamento)

![Nível de Isolamento](media/data-flow/isolationlevel.png "Nível de Isolamento")

### <a name="sink-transformation"></a>Transformação de pia

Definições específicas para O Anapse Azure estão disponíveis no separador **Definições** da transformação do lavatório.

Método de **atualização:** Determina quais as operações permitidas no seu destino de base de dados. O padrão é apenas permitir inserções. Para atualizar, atualizar ou eliminar linhas, é necessária uma transformação alter-row para marcar linhas para essas ações. Para atualizações, upserts e eliminações, deve ser definida uma coluna ou colunas chave para determinar qual a linha a alterar.

**Ação de mesa:** Determina se recriar ou remover todas as linhas da tabela de destino antes de escrever.
* Nenhuma: nenhuma ação será feita à mesa.
* Recriar: A mesa será largada e recriada. Necessário se criar uma nova tabela dinamicamente.
* Todas as filas da mesa-alvo serão removidas.

**Ativar a encenação:** Determina se deve ou não utilizar a [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) ao escrever para a Azure Synapse Analytics

**Tamanho do lote**: Controla quantas filas estão a ser escritas em cada balde. Tamanhos maiores do lote melhoram a compressão e a otimização da memória, mas arriscam-se a partir de exceções de memória ao cortar dados.

**Scripts Pré e Post SQL**: Introduza scripts SQL multi-line que serão executados antes (pré-processamento) e depois de (pós-processamento) os dados forem escritos na sua base de dados sink

![scripts de processamento pré e pós SQL](media/data-flow/prepost1.png "Scripts de processamento SQL")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapeamento de tipo de dados para Azure Synapse Analytics

Quando copia dados de ou para o Azure Synapse Analytics, os seguintes mapeamentos são utilizados desde tipos de dados da Azure Synapse Analytics para tipos de dados provisórios da Azure Data Factory. Consulte [os mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber como a Atividade de Cópia mapeia o esquema de origem e o tipo de dados para a pia.

>[!TIP]
>Consulte os tipos de dados de tabela no artigo [da Azure Synapse Analytics](../synapse-analytics/sql/develop-tables-data-types.md) sobre os tipos de dados suportados pela SQL DW e as sobras para os não suportados.

| Tipo de dados azure Synapse Analytics    | Data Factory tipo de dados provisórios |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Booleano                        |
| char                                  | String, Char[]                 |
| date                                  | DateTime                       |
| Datetime                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datatimeoffset                        | DataTimeOffset                 |
| Decimal                               | Decimal                        |
| Atributo FILESTREAM (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| dinheiro                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Único                         |
| linhaversão                            | Byte[]                         |
| tempo de data pequena                         | DateTime                       |
| smallint                              | Int16                          |
| dinheiro pequeno                            | Decimal                        |
| hora                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | GUID                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela Copy Activity na Azure Data Factory, consulte [lojas e formatos de dados suportados.](copy-activity-overview.md#supported-data-stores-and-formats)
