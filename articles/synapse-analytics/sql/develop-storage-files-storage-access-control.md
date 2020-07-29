---
title: Acesso à conta de armazenamento de controlo para SQL a pedido (pré-visualização)
description: Descreve como o SQL on-demand (pré-visualização) acede ao Azure Storage e como pode controlar o acesso ao armazenamento para SQL on-demand em Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d60eeb279f9faa469c98d3d0578d0e4c1cdf0bd2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283457"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>Acesso à conta de armazenamento de controlo para SQL a pedido (pré-visualização)

Uma consulta a pedido do SQL lê ficheiros diretamente do Azure Storage. As permissões de acesso aos ficheiros do armazenamento Azure são controladas a dois níveis:
- **Nível de armazenamento** - O utilizador deve ter permissão para aceder a ficheiros de armazenamento subjacentes. O seu administrador de armazenamento deve permitir que o diretor da AD AD Azure leia/escreva ficheiros ou gere a chave SAS que será usada para aceder ao armazenamento.
- **Nível de serviço SQL** - O utilizador deve ter `SELECT` permissão para ler dados a partir de [tabela externa](develop-tables-external-tables.md) ou `ADMINISTER BULK ADMIN` permissão para executar e também `OPENROWSET` permissão para usar credenciais que serão usadas para aceder ao armazenamento.

Este artigo descreve os tipos de credenciais que pode usar e como a procura credencial é decretada para os utilizadores de AD SQL e Azure.

## <a name="supported-storage-authorization-types"></a>Tipos de autorização de armazenamento suportados

Um utilizador que tenha iniciado sessão num recurso a pedido do SQL deve ser autorizado a aceder e consultar os ficheiros no Azure Storage se os ficheiros não estiverem disponíveis ao público. Pode utilizar três tipos de autorização para aceder ao armazenamento não público - [Identidade do Utilizador,](?tabs=user-identity) [Assinatura de acesso Partilhado](?tabs=shared-access-signature)e Identidade [Gerida.](?tabs=managed-identity)

> [!NOTE]
> **A azure AD pass-through** é o comportamento padrão quando cria um espaço de trabalho.

### <a name="user-identity"></a>[Identidade do Utilizador](#tab/user-identity)

**Identidade do Utilizador**, também conhecida como "Azure AD pass-through", é um tipo de autorização onde a identidade do utilizador Azure AD que acedeu ao SQL on-demand é usada para autorizar o acesso aos dados. Antes de aceder aos dados, o administrador do Azure Storage deve conceder permissões ao utilizador Azure AD. Como indicado na tabela abaixo, não é suportado para o tipo de utilizador SQL.

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
> Quando um token SAS é gerado, inclui um ponto de interrogação ('?') no início do token. Para utilizar o token em SQL on demand, deve remover o ponto de interrogação ('?') ao criar uma credencial. Por exemplo:
>
> Sas token: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&&&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

É necessário criar credenciais de âmbito de base de dados ou de âmbito de servidor para permitir o acesso através do token SAS.

### <a name="managed-identity"></a>[Identidade Gerida](#tab/managed-identity)

**A Identidade Gerida** também é conhecida como MSI. É uma característica do Azure Ative Directory (Azure AD) que fornece serviços Azure para SQL on-demand. Além disso, implementa uma identidade gerida automaticamente no Azure AD. Esta identidade pode ser utilizada para autorizar o pedido de acesso aos dados no Azure Storage.

Antes de aceder aos dados, o administrador do Azure Storage deve conceder permissões à Identidade Gerida para aceder aos dados. A concessão de permissões à Identidade Gerida é feita da mesma forma que a concessão de permissão a qualquer outro utilizador da AD Azure.

### <a name="anonymous-access"></a>[Acesso anónimo](#tab/public-access)

Pode aceder a ficheiros publicamente disponíveis colocados em contas de armazenamento Azure que [permitem o acesso anónimo](/azure/storage/blobs/storage-manage-access-to-resources).

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

|                     | Armazenamento de Blobs   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Suportado      | Não suportado   | Suportado     |
| *Identidade gerida* | Suportado      | Suportado        | Suportado     |
| *Identidade do Utilizador*    | Suportado      | Suportado        | Suportado     |


> [!IMPORTANT]
> Ao aceder ao armazenamento protegido com a firewall, apenas a Identidade Gerida pode ser utilizada. Precisa de [permitir serviços de confiança da Microsoft... definição](../../storage/common/storage-network-security.md#trusted-microsoft-services) e atribuição explicitamente [de um papel RBAC](../../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights) à [identidade gerida atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/overview.md) para essa instância de recursos. Neste caso, o âmbito de acesso, por exemplo, corresponde à função RBAC atribuída à identidade gerida.
>

## <a name="credentials"></a>Credenciais

Para consultar um ficheiro localizado no Azure Storage, o seu ponto final a pedido do SQL necessita de uma credencial que contenha as informações de autenticação. São utilizados dois tipos de credenciais:
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

Para garantir uma experiência de passagem AD Azure suave, todos os utilizadores terão, por padrão, o direito de usar a `UserIdentity` credencial.

## <a name="server-scoped-credential"></a>Credencial de âmbito do servidor

As credenciais de âmbito do servidor são utilizadas quando as chamadas de login do SQL `OPENROWSET` funcionam sem `DATA_SOURCE` ler ficheiros em alguma conta de armazenamento. O nome da credencial com âmbito de servidor **deve** coincidir com o URL do armazenamento Azure. Uma credencial é adicionada executando [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Terá de fornecer um argumento DE NOME CREDENTIAL. Deve coincidir com qualquer parte do caminho ou todo o caminho para os dados no Armazenamento (ver abaixo).

> [!NOTE]
> O `FOR CRYPTOGRAPHIC PROVIDER` argumento não é apoiado.

O nome CREDENCIAL ao nível do servidor deve corresponder ao caminho completo da conta de armazenamento (e opcionalmente do recipiente) no seguinte formato: `<prefix>://<storage_account_path>/<storage_path>` . Os caminhos da conta de armazenamento são descritos no quadro seguinte:

| Fonte de Dados Externos       | Prefixo | Caminho da conta de armazenamento                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Armazenamento de Blobs do Azure         | https  | <storage_account>.blob.core.windows.net             |
| Armazenamento do Azure Data Lake Ger1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Armazenamento do Azure Data Lake Ger2 | https  | <storage_account>.dfs.core.windows.net              |

Credenciais de âmbito do servidor permitem o acesso ao armazenamento Azure utilizando os seguintes tipos de autenticação:

### <a name="user-identity"></a>[Identidade do Utilizador](#tab/user-identity)

Os utilizadores de AZure AD podem aceder a qualquer ficheiro no armazenamento Azure se `Storage Blob Data Owner` `Storage Blob Data Contributor` tiverem, ou `Storage Blob Data Reader` função. Os utilizadores de AD Azure não precisam de credenciais para aceder ao armazenamento. 

Os utilizadores do SQL não podem utilizar a autenticação AZURE AD para aceder ao armazenamento.

### <a name="shared-access-signature"></a>[Assinatura de acesso partilhado](#tab/shared-access-signature)

O seguinte script cria uma credencial de nível de servidor que pode ser usada por `OPENROWSET` função para aceder a qualquer ficheiro no armazenamento Azure usando o token SAS. Crie esta credencial para permitir que o principal sql que executa `OPENROWSET` a função de ler ficheiros protegidos com a chave SAS no armazenamento Azure que corresponda a URL em nome credencial.

Troque <*mystorageaccountname*> com o seu nome de conta de armazenamento real, e <*mystorageaccountcontainername*> com o nome real do recipiente:

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="managed-identity"></a>[Identidade Gerida](#tab/managed-identity)

O seguinte script cria uma credencial de nível de servidor que pode ser usada por `OPENROWSET` função para aceder a qualquer ficheiro no armazenamento Azure usando identidade gerida pelo espaço de trabalho.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[Acesso público](#tab/public-access)

A credencial de âmbito de dados não é necessária para permitir o acesso a ficheiros disponíveis ao público. Crie [fonte de dados sem credencial](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) de âmbito de base de dados para aceder a ficheiros disponíveis publicamente no armazenamento Azure.

---

## <a name="database-scoped-credential"></a>Credencial de âmbito de base de dados

As credenciais de âmbito de base de dados são utilizadas quando qualquer função de chamadas principais `OPENROWSET` ou `DATA_SOURCE` seleciona dados de [tabelas externas](develop-tables-external-tables.md) que não acedam a ficheiros públicos. A credencial de base de dados não precisa de corresponder ao nome da conta de armazenamento porque será explicitamente utilizada na DATA SOURCE que define a localização do armazenamento.

As credenciais de âmbito de base de dados permitem o acesso ao armazenamento Azure utilizando os seguintes tipos de autenticação:

### <a name="azure-ad-identity"></a>[Identidade Azure AD](#tab/user-identity)

Os utilizadores de AD Azure podem aceder a qualquer ficheiro no armazenamento Azure se tiverem, pelo `Storage Blob Data Owner` `Storage Blob Data Contributor` menos, ou `Storage Blob Data Reader` papel. Os utilizadores de AD Azure não precisam de credenciais para aceder ao armazenamento.

Os utilizadores do SQL não podem utilizar a autenticação AZURE AD para aceder ao armazenamento.

### <a name="shared-access-signature"></a>[Assinatura de acesso partilhado](#tab/shared-access-signature)

O seguinte script cria uma credencial que é usada para aceder a ficheiros no armazenamento usando o token SAS especificado na credencial.

```sql
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="managed-identity"></a>[Identidade Gerida](#tab/managed-identity)

O seguinte script cria uma credencial de âmbito de base de dados que pode ser usada para personificar o atual utilizador Azure AD como Identidade Gerida de Serviço. 

```sql
CREATE DATABASE SCOPED CREDENTIAL [SynapseIdentity]
WITH IDENTITY = 'Managed Identity';
GO
```

A credencial de base de dados não precisa de corresponder ao nome da conta de armazenamento porque será explicitamente utilizada na DATA SOURCE que define a localização do armazenamento.

### <a name="public-access"></a>[Acesso público](#tab/public-access)

A credencial de âmbito de dados não é necessária para permitir o acesso a ficheiros disponíveis ao público. Crie [fonte de dados sem credencial](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) de âmbito de base de dados para aceder a ficheiros disponíveis publicamente no armazenamento Azure.

---

As credenciais de rastreio da base de dados são utilizadas em fontes de dados externas para especificar que método de autenticação será utilizado para aceder a este armazenamento:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Exemplos

**Aceder a fonte de dados publicamente disponível**

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
                                DATA_SOURCE = [mysample],
                                FORMAT='PARQUET') as rows;
GO
```

**Aceder à fonte de dados utilizando a credencial**

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

O utilizador da base de dados pode ler o conteúdo dos ficheiros a partir da fonte de dados utilizando [a tabela externa](develop-tables-external-tables.md) ou a função [OPENROWSET](develop-openrowset.md) que faz referência à fonte de dados:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT='PARQUET') as rows;
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
