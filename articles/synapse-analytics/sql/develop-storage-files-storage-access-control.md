---
title: Controle o acesso à conta de armazenamento para piscina SQL sem servidor
description: Descreve como a piscina SQL sem servidor acede ao Azure Storage e como pode controlar o acesso ao armazenamento para piscina SQL sem servidor em Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 545331fdea56aef3d7b9dac8062d4fc2d6891254
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501576"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Acesso de conta de armazenamento de controlo para piscina SQL sem servidor em Azure Synapse Analytics

Uma consulta de piscina SQL sem servidor lê ficheiros diretamente do Azure Storage. As permissões de acesso aos ficheiros do armazenamento Azure são controladas a dois níveis:
- **Nível de armazenamento** - O utilizador deve ter permissão para aceder a ficheiros de armazenamento subjacentes. O seu administrador de armazenamento deve permitir que o diretor da AD AD Azure leia/escreva ficheiros ou gere a chave SAS que será usada para aceder ao armazenamento.
- **Nível de serviço SQL** - O utilizador deveria ter dado permissão para ler dados utilizando [tabela externa](develop-tables-external-tables.md) ou para executar a `OPENROWSET` função. Leia mais sobre [as permissões necessárias nesta secção.](develop-storage-files-overview.md#permissions)

Este artigo descreve os tipos de credenciais que pode usar e como a procura credencial é decretada para os utilizadores de AD SQL e Azure.

## <a name="supported-storage-authorization-types"></a>Tipos de autorização de armazenamento suportados

Um utilizador que tenha iniciado sessão num pool SQL sem servidor deve ser autorizado a aceder e consultar os ficheiros no Azure Storage se os ficheiros não estiverem disponíveis ao público. Pode utilizar três tipos de autorização para aceder ao armazenamento não público - [Identidade do Utilizador,](?tabs=user-identity) [Assinatura de acesso Partilhado](?tabs=shared-access-signature)e Identidade [Gerida.](?tabs=managed-identity)

> [!NOTE]
> **A azure AD pass-through** é o comportamento padrão quando cria um espaço de trabalho.

### <a name="user-identity"></a>[Identidade do Utilizador](#tab/user-identity)

**Identidade do Utilizador**, também conhecida como "Azure AD pass-through", é um tipo de autorização onde a identidade do utilizador Azure AD que iniciou sessão no pool SQL sem servidor é usada para autorizar o acesso aos dados. Antes de aceder aos dados, o administrador do Azure Storage deve conceder permissões ao utilizador Azure AD. Como indicado na tabela abaixo, não é suportado para o tipo de utilizador SQL.

> [!IMPORTANT]
> Precisa de ter uma função de Proprietário/Contribuinte/Leitor de Armazenamento para utilizar a sua identidade para aceder aos dados.
> Mesmo que você é proprietário de uma conta de armazenamento, você ainda precisa adicionar-se a uma das funções de Armazenamento Blob Data.
>
> Para saber mais sobre o controlo de acessos na Azure Data Lake Store Gen2, reveja o [controlo access in Azure Data Lake Storage Gen2.](../../storage/blobs/data-lake-storage-access-control.md)
>

### <a name="shared-access-signature"></a>[Assinatura de acesso partilhado](#tab/shared-access-signature)

**A assinatura de acesso partilhado (SAS)** proporciona acesso delegado aos recursos numa conta de armazenamento. Com o SAS, um cliente pode conceder aos clientes acesso a recursos numa conta de armazenamento sem partilhar chaves de conta. A SAS dá-lhe controlo granular sobre o tipo de acesso que concede a clientes que tenham um SAS, incluindo intervalo de validade, permissões concedidas, intervalo de endereço IP aceitável e o protocolo aceitável (https/http).

Pode obter um token SAS navegando para o **portal Azure -> Storage Account -> Assinatura de acesso partilhado -> configurar permissões -> Gerar SAS e cadeia de conexão.**

> [!IMPORTANT]
> Quando um token SAS é gerado, inclui um ponto de interrogação ('?') no início do token. Para utilizar o token na piscina SQL sem servidor, deve remover o ponto de interrogação ('?') ao criar uma credencial. Por exemplo:
>
> Sas token: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&&&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

Para ativar o acesso através de um token SAS, é necessário criar uma credencial de âmbito de base de dados ou de âmbito de servidor 

### <a name="managed-identity"></a>[Identidade Gerida](#tab/managed-identity)

**A Identidade Gerida** também é conhecida como MSI. É uma característica do Azure Ative Directory (Azure AD) que fornece serviços Azure para piscina SQL sem servidor. Além disso, implementa uma identidade gerida automaticamente no Azure AD. Esta identidade pode ser utilizada para autorizar o pedido de acesso aos dados no Azure Storage.

Antes de aceder aos dados, o administrador do Azure Storage deve conceder permissões à Identidade Gerida para aceder aos dados. A concessão de permissões à Identidade Gerida é feita da mesma forma que a concessão de permissão a qualquer outro utilizador da AD Azure.

### <a name="anonymous-access"></a>[Acesso anónimo](#tab/public-access)

Pode aceder a ficheiros publicamente disponíveis colocados em contas de armazenamento Azure que [permitem o acesso anónimo](../../storage/blobs/anonymous-read-access-configure.md).

---

### <a name="supported-authorization-types-for-databases-users"></a>Tipos de autorização suportados para utilizadores de bases de dados

Na tabela abaixo pode encontrar os tipos de autorização disponíveis:

| Tipo de autorização                    | *Utilizador de SQL*    | *Utilizador do Azure Active Directory*     |
| ------------------------------------- | ------------- | -----------    |
| [Identidade do Utilizador](?tabs=user-identity#supported-storage-authorization-types)       | Não suportado | Suportado      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Suportado     | Suportado      |
| [Identidade Gerida](?tabs=managed-identity#supported-storage-authorization-types) | Não suportado | Suportado      |

### <a name="supported-storages-and-authorization-types"></a>Armazenamentos e tipos de autorização apoiados

Pode utilizar as seguintes combinações de autorizações e tipos de armazenamento Azure:

| Tipo de autorização  | Armazenamento de Blobs   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | Suportado\*      | Não suportado   | Suportado\*     |
| [Identidade Gerida](?tabs=managed-identity#supported-storage-authorization-types) | Suportado      | Suportado        | Suportado     |
| [Identidade do Utilizador](?tabs=user-identity#supported-storage-authorization-types)    | Suportado\*      | Suportado\*        | Suportado\*     |

\* O token SAS e o AD Identity Azure podem ser usados para aceder a armazenamento que não esteja protegido com firewall.


### <a name="querying-firewall-protected-storage"></a>Consultar o armazenamento protegido por firewall

Ao aceder ao armazenamento protegido com a firewall, pode utilizar a **Identidade do Utilizador** ou identidade **gerida.**

> [!NOTE]
> A funcionalidade de firewall no Armazenamento está em pré-visualização pública e está disponível em todas as regiões de nuvem pública. 

#### <a name="user-identity"></a>Identidade do Utilizador

Para aceder ao armazenamento que está protegido com a firewall através da Identidade do Utilizador, pode utilizar o módulo PowerShell Az.Storage.
#### <a name="configuration-via-powershell"></a>Configuração via PowerShell

Siga estes passos para configurar a firewall da sua conta de armazenamento e adicione uma exceção para o espaço de trabalho da Synapse.

1. Abrir powerShell ou [instalar PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)
2. Instale o módulo Az.Storage 3.4.0 e Az.Synapse 0.7.0: 
    ```powershell
    Install-Module -Name Az.Storage -RequiredVersion 3.4.0
    Install-Module -Name Az.Synapse -RequiredVersion 0.7.0
    ```
    > [!IMPORTANT]
    > Certifique-se de que utiliza a **versão 3.4.0**. Pode consultar a sua versão Az.Storage executando este comando:  
    > ```powershell 
    > Get-Module -ListAvailable -Name  Az.Storage | select Version
    > ```
    > 

3. Ligue-se ao seu Inquilino Azure: 
    ```powershell
    Connect-AzAccount
    ```
4. Defina variáveis em PowerShell: 
    - Nome do grupo de recursos - pode encontrá-lo no portal Azure na visão geral da conta de Armazenamento.
    - Nome da conta - nome da conta de armazenamento que está protegida pelas regras de firewall.
    - ID do inquilino - você pode encontrar isso no portal Azure em Azure Ative Diretório em informações de inquilino.
    - Nome do espaço de trabalho - Nome do espaço de trabalho synapse.

    ```powershell
        $resourceGroupName = "<resource group name>"
        $accountName = "<storage account name>"
        $tenantId = "<tenant id>"
        $workspaceName = "<synapse workspace name>"
        
        $workspace = Get-AzSynapseWorkspace -Name $workspaceName
        $resourceId = $workspace.Id
        $index = $resourceId.IndexOf("/resourceGroups/", 0)
        # Replace G with g - /resourceGroups/ to /resourcegroups/
        $resourceId = $resourceId.Substring(0,$index) + "/resourcegroups/" + $resourceId.Substring($index + "/resourceGroups/".Length)
        $resourceId
    ```
    > [!IMPORTANT]
    > Certifique-se de que o id do recurso corresponde a este modelo na impressão da variável resourceId.
    >
    > É importante escrever **grupos de recursos** em minúsculas.
    > Exemplo de um id de recurso: 
    > ```
    > /subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Synapse/workspaces/{name-of-workspace}
    > ```
    > 
5. Adicionar regra da Rede de Armazenamento: 
    ```powershell
        Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId
    ```
6. Verifique se a regra foi aplicada na sua conta de armazenamento: 
    ```powershell
        $rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
        $rule.ResourceAccessRules | ForEach-Object { 
            if ($_.ResourceId -cmatch "\/subscriptions\/(\w\-*)+\/resourcegroups\/(.)+") { 
                Write-Host "Storage account network rule is successfully configured." -ForegroundColor Green
                $rule.ResourceAccessRules
            } else {
                Write-Host "Storage account network rule is not configured correctly. Remove this rule and follow the steps in detail." -ForegroundColor Red
                $rule.ResourceAccessRules
            }
        }
    ```

#### <a name="managed-identity"></a>Identidade Gerida
Precisa de [permitir serviços de confiança da Microsoft... definição](../../storage/common/storage-network-security.md#trusted-microsoft-services) e atribuição explicitamente [de um papel Azure](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights) à [identidade gerida atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/overview.md) para essa instância de recursos. Neste caso, o âmbito de acesso, por exemplo, corresponde à função Azure atribuída à identidade gerida.

## <a name="credentials"></a>Credenciais

Para consultar um ficheiro localizado no Azure Storage, o ponto final da piscina SQL sem servidor necessita de uma credencial que contenha as informações de autenticação. São utilizados dois tipos de credenciais:
- O CREDENCIAL de nível do servidor é utilizado para consultas ad-hoc executadas utilizando `OPENROWSET` a função. O nome credencial deve coincidir com o URL de armazenamento.
- BASE BASE DE DADOS O CREDENCIAL SCOPED é utilizado para tabelas externas. Referências de tabela externa `DATA SOURCE` com a credencial que deve ser usada para aceder ao armazenamento.

Para permitir que um utilizador crie ou largue uma credencial, o administrador pode conceder/negar qualquer permissão credencial a um utilizador:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Os utilizadores de bases de dados que acedam ao armazenamento externo devem ter permissão para usar credenciais.

### <a name="grant-permissions-to-use-credential"></a>Conceder permissões para usar credenciais

Para utilizar a credencial, o utilizador deve ter `REFERENCES` permissão numa credencial específica. Para conceder uma `REFERENCES` permissão on a storage_credential para um specific_user, execute:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

## <a name="server-scoped-credential"></a>Credencial de âmbito do servidor

As credenciais de âmbito do servidor são utilizadas quando as chamadas de login do SQL `OPENROWSET` funcionam sem `DATA_SOURCE` ler ficheiros em alguma conta de armazenamento. O nome da credencial com âmbito de servidor **deve** coincidir com o URL base do armazenamento Azure (opcionalmente seguido de um nome de recipiente). Uma credencial é adicionada executando [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true). Terá de fornecer um argumento DE NOME CREDENTIAL.

> [!NOTE]
> O `FOR CRYPTOGRAPHIC PROVIDER` argumento não é apoiado.

O nome CREDENCIAL ao nível do servidor deve corresponder ao caminho completo da conta de armazenamento (e opcionalmente do recipiente) no seguinte formato: `<prefix>://<storage_account_path>[/<container_name>]` . Os caminhos da conta de armazenamento são descritos no quadro seguinte:

| Fonte de Dados Externos       | Prefixo | Caminho da conta de armazenamento                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Armazenamento de Blobs do Azure         | https  | <storage_account>.blob.core.windows.net             |
| Armazenamento do Azure Data Lake Ger1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Armazenamento do Azure Data Lake Ger2 | https  | <storage_account>.dfs.core.windows.net              |

Credenciais de âmbito do servidor permitem o acesso ao armazenamento Azure utilizando os seguintes tipos de autenticação:

### <a name="user-identity"></a>[Identidade do Utilizador](#tab/user-identity)

Os utilizadores de AZure AD podem aceder a qualquer ficheiro no armazenamento Azure se `Storage Blob Data Owner` `Storage Blob Data Contributor` tiverem, ou `Storage Blob Data Reader` função. Os utilizadores de AD Azure não precisam de credenciais para aceder ao armazenamento. 

Os utilizadores do SQL não podem utilizar a autenticação AZure AD para aceder ao armazenamento.

### <a name="shared-access-signature"></a>[Assinatura de acesso partilhado](#tab/shared-access-signature)

O seguinte script cria uma credencial de nível de servidor que pode ser usada por `OPENROWSET` função para aceder a qualquer ficheiro no armazenamento Azure usando o token SAS. Crie esta credencial para permitir que o principal sql que executa `OPENROWSET` a função de ler ficheiros protegidos com a chave SAS no armazenamento Azure que corresponda a URL em nome credencial.

Troque <*mystorageaccountname*> com o seu nome de conta de armazenamento real, e <*mystorageaccountcontainername*> com o nome real do recipiente:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

Opcionalmente, pode utilizar apenas o URL base da conta de armazenamento, sem o nome do recipiente.

### <a name="managed-identity"></a>[Identidade Gerida](#tab/managed-identity)

O seguinte script cria uma credencial de nível de servidor que pode ser usada por `OPENROWSET` função para aceder a qualquer ficheiro no armazenamento Azure usando identidade gerida pelo espaço de trabalho.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

Opcionalmente, pode utilizar apenas o URL base da conta de armazenamento, sem o nome do recipiente.

### <a name="public-access"></a>[Acesso público](#tab/public-access)

A credencial de âmbito de dados não é necessária para permitir o acesso a ficheiros disponíveis ao público. Crie [fonte de dados sem credencial](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) de âmbito de base de dados para aceder a ficheiros disponíveis publicamente no armazenamento Azure.

---

## <a name="database-scoped-credential"></a>Credencial de âmbito de base de dados

As credenciais de âmbito de base de dados são utilizadas quando qualquer função de chamadas principais `OPENROWSET` ou `DATA_SOURCE` seleciona dados de [tabelas externas](develop-tables-external-tables.md) que não acedam a ficheiros públicos. A credencial de base de dados não precisa de corresponder ao nome da conta de armazenamento. Será explicitamente utilizado na DATA SOURCE que define a localização do armazenamento.

As credenciais de âmbito de base de dados permitem o acesso ao armazenamento Azure utilizando os seguintes tipos de autenticação:

### <a name="azure-ad-identity"></a>[Identidade Azure AD](#tab/user-identity)

Os utilizadores de AD Azure podem aceder a qualquer ficheiro no armazenamento Azure se tiverem, pelo `Storage Blob Data Owner` `Storage Blob Data Contributor` menos, ou `Storage Blob Data Reader` papel. Os utilizadores de AD Azure não precisam de credenciais para aceder ao armazenamento.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

Os utilizadores do SQL não podem utilizar a autenticação AZure AD para aceder ao armazenamento.

### <a name="shared-access-signature"></a>[Assinatura de acesso partilhado](#tab/shared-access-signature)

O seguinte script cria uma credencial que é usada para aceder a ficheiros no armazenamento usando o token SAS especificado na credencial. O script criará uma amostra de fonte de dados externa que utiliza este token SAS para aceder ao armazenamento.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Identidade Gerida](#tab/managed-identity)

O seguinte script cria uma credencial de âmbito de base de dados que pode ser usada para personificar o atual utilizador Azure AD como Identidade Gerida de Serviço. O script criará uma amostra de fonte de dados externa que utiliza a identidade do espaço de trabalho para aceder ao armazenamento.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

A credencial de base de dados não precisa de corresponder ao nome da conta de armazenamento porque será explicitamente utilizada na DATA SOURCE que define a localização do armazenamento.

### <a name="public-access"></a>[Acesso público](#tab/public-access)

A credencial de âmbito de dados não é necessária para permitir o acesso a ficheiros disponíveis ao público. Crie [fonte de dados sem credencial](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) de âmbito de base de dados para aceder a ficheiros disponíveis publicamente no armazenamento Azure.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

As credenciais de rastreio da base de dados são utilizadas em fontes de dados externas para especificar que método de autenticação será utilizado para aceder a este armazenamento:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Exemplos

### <a name="access-a-publicly-available-data-source"></a>**Aceda a uma fonte de dados publicamente disponível**

Utilize o seguinte script para criar uma tabela que aceda a fonte de dados publicamente disponível.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

O utilizador da base de dados pode ler o conteúdo dos ficheiros a partir da fonte de dados utilizando a tabela externa ou a função [OPENROWSET](develop-openrowset.md) que faz referência à fonte de dados:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Aceda a uma fonte de dados usando credenciais**

Modifique o seguinte script para criar uma tabela externa que aceda ao armazenamento Azure usando o token SAS, identidade AD AD do utilizador ou identidade gerida do espaço de trabalho.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

O utilizador da base de dados pode ler o conteúdo dos ficheiros a partir da fonte de dados utilizando [a tabela externa](develop-tables-external-tables.md) ou a função [OPENROWSET](develop-openrowset.md)  que faz referência à fonte de dados:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Passos seguintes

Os artigos listados abaixo irão ajudá-lo a aprender como consultar diferentes tipos de pastas, tipos de ficheiros e criar e utilizar vistas:

- [Consulta único ficheiro CSV](query-single-csv-file.md)
- [Pastas de consulta e vários ficheiros CSV](query-folders-multiple-csv-files.md)
- [Consulta de ficheiros específicos](query-specific-files.md)
- [Consultar ficheiros de Parquet](query-parquet-files.md)
- [Criar e utilizar vistas](create-use-views.md)
- [Consultar ficheiros JSON](query-json-files.md)
- [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md)
