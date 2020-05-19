---
title: Acesso à conta de armazenamento de controlo para SQL a pedido (pré-visualização)
description: Descreve como o SQL on-demand (pré-visualização) acede ao Armazenamento Azure e como pode controlar o acesso ao armazenamento para o SQL a pedido no Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2d5d508afe81975cbeda448b497a098e8a3bbcf3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589283"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>Acesso à conta de armazenamento de controlo para SQL a pedido (pré-visualização)

Uma consulta a pedido da SQL lê ficheiros diretamente do Armazenamento Azure. As permissões para aceder aos ficheiros do armazenamento do Azure são controladas a dois níveis:
- **Nível de armazenamento** - O utilizador deve ter permissão para aceder a ficheiros de armazenamento subjacentes. O seu administrador de armazenamento deve permitir que o diretor da AD Azure leia/escreva ficheiros ou gere a chave SAS que será usada para aceder ao armazenamento.
- **Nível de serviço SQL** - O utilizador deve ter `SELECT` permissão para ler dados de [tabela ou](develop-tables-external-tables.md) permissão externa `ADMINISTER BULK ADMIN` para executar e também `OPENROWSET` permissão para usar credenciais que serão usadas para aceder ao armazenamento.

Este artigo descreve os tipos de credenciais que pode utilizar e como a procura credencial é decretada para os utilizadores de AD SQL e Azure.

## <a name="supported-storage-authorization-types"></a>Tipos de autorização de armazenamento suportados

Um utilizador que tenha iniciado um sessão num recurso SQL a pedido deve ser autorizado a aceder e consultar os ficheiros no Armazenamento Azure se os ficheiros não estiverem disponíveis ao público. São apoiados três tipos de autorização:

- [Assinatura de acesso partilhado](?tabs=shared-access-signature)
- [Identidade do Utilizador](?tabs=user-identity)
- [Identidade Gerida](?tabs=managed-identity)

> [!NOTE]
> [A passagem da AD Azure](#force-azure-ad-pass-through) é o comportamento padrão quando se cria um espaço de trabalho. Se o utilizar, não precisa de criar credenciais para cada conta de armazenamento acedida através de logins Azure AD. Pode [desativar este comportamento.](#disable-forcing-azure-ad-pass-through)

### <a name="shared-access-signature"></a>[Assinatura de acesso partilhado](#tab/shared-access-signature)

A assinatura de **acesso partilhado (SAS)** proporciona acesso delegado aos recursos numa conta de armazenamento. Com a SAS, um cliente pode conceder aos clientes acesso aos recursos numa conta de armazenamento sem partilhar chaves de conta. A SAS dá-lhe controlo granular sobre o tipo de acesso que concede a clientes que tenham um Intervalo de Validade, incluindo intervalo de validade, permissões concedidas, intervalo de endereçoip aceitável e o protocolo aceitável (https/http).

Você pode obter um token SAS navegando para o portal Azure -> Conta de Armazenamento - > assinatura de **acesso partilhado -> permissões Configuradas -> Gerar SAS e cadeia de conexão.**

> [!IMPORTANT]
> Quando uma token SAS é gerada, inclui um ponto de interrogação ('?') no início do símbolo. Para utilizar o símbolo no SQL a pedido, deve remover o ponto de interrogação ('?') ao criar uma credencial. Por exemplo:
>
> Ficha sas: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSMANd0ef9BrIPBNJ3VYEIq78%3D

É necessário criar credenciais com um espaço de dados ou com um espaço de serviço para permitir o acesso utilizando token SAS.

### <a name="user-identity"></a>[Identidade do Utilizador](#tab/user-identity)

**Identidade do Utilizador**, também conhecido como "pass-through", é um tipo de autorização em que a identidade do utilizador da AD Azure que acedeu à SQL a pedido é utilizada para autorizar o acesso de dados. Antes de aceder aos dados, o administrador de Armazenamento Azure deve conceder permissões ao utilizador da AD Azure. Como indicado na tabela acima, não é suportado para o tipo de utilizador SQL.

> [!IMPORTANT]
> É necessário ter uma função de Proprietário/Colaborador/Leitor de Armazenamento Blob para utilizar a sua identidade para aceder aos dados.
> Mesmo que seja proprietário de uma Conta de Armazenamento, ainda precisa de se adicionar a uma das funções de Dados blob de armazenamento.
>
> Para saber mais sobre o controlo de acessos na Azure Data Lake Store Gen2, reveja o controlo de acesso no artigo [da Azure Data Lake Storage Gen2.](../../storage/blobs/data-lake-storage-access-control.md)
>

É necessário ativar explicitamente a autenticação pass-through da Azure AD para permitir aos utilizadores de AD Azure aceder em armazenamento utilizando as suas identidades.

#### <a name="force-azure-ad-pass-through"></a>Passe da Force Azure AD

Forçar um passe de AD Azure é um comportamento padrão alcançado por um NOME CREDENTIAL especial, que é criado automaticamente durante o fornecimento de espaço de `UserIdentity` trabalho Azure Synapse. Força o uso de uma passagem de AD Azure para cada consulta de cada login Azure AD, que ocorrerá apesar da existência de outras credenciais.

> [!NOTE]
> A passagem da AD Azure é um comportamento padrão. Não precisa de criar credenciais para cada conta de armazenamento acedida por logins ad.

No caso [de ter desativado forçando a passagem do Azure AD para cada consulta](#disable-forcing-azure-ad-pass-through), e quiser permitir novamente, execute:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Para permitir a imposição de um passe de AD Azure para um utilizador específico, pode conceder permissão de REFERÊNCIA na credencial `UserIdentity` a esse utilizador em particular. O exemplo seguinte permite forçar uma passagem da AD Azure para um user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

#### <a name="disable-forcing-azure-ad-pass-through"></a>Desativar forçando a passagem da AD Azure

Pode desativar [forçando a passagem do Azure AD para cada consulta](#force-azure-ad-pass-through). Para desativá-lo, deixe cair a `Userdentity` credencial utilizando:

```sql
DROP CREDENTIAL [UserIdentity];
```

Se quiser voltar a enactivar, consulte a secção de passagem da [Azure AD.](#force-azure-ad-pass-through)

### <a name="managed-identity"></a>[Identidade Gerida](#tab/managed-identity)

**A Identidade Gerida** também é conhecida como MSI. É uma característica do Azure Ative Directory (Azure AD) que fornece serviços Azure para a SQL on-demand. Além disso, implementa uma identidade gerida automaticamente em Azure AD. Esta identidade pode ser utilizada para autorizar o pedido de acesso de dados no Armazenamento Azure.

Antes de aceder aos dados, o administrador de Armazenamento Azure deve conceder permissões à Identidade Gerida para aceder aos dados. A concessão de permissões à Identidade Gerida é feita da mesma forma que a concessão de permissão a qualquer outro utilizador da AD Azure.

### <a name="anonymous-access"></a>[Acesso anónimo](#tab/public-access)

Pode aceder a ficheiros publicamente disponíveis colocados em contas de armazenamento do Azure que [permitem o acesso anónimo](/azure/storage/blobs/storage-manage-access-to-resources.md).

---

### <a name="supported-authorization-types-for-databases-users"></a>Tipos de autorização suportados para utilizadores de bases de dados

Na tabela abaixo pode encontrar os tipos de autorização disponíveis:

| Tipo de autorização                    | *Utilizador SQL*    | *Utilizador da AD Azure*     |
| ------------------------------------- | ------------- | -----------    |
| [Identidade do Utilizador](?tabs=user-identity#supported-storage-authorization-types)       | Não suportado | Suportado      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Suportado     | Suportado      |
| [Identidade Gerida](?tabs=managed-identity#supported-storage-authorization-types) | Não suportado | Suportado      |

### <a name="supported-storages-and-authorization-types"></a>Armazenamentos e tipos de autorização suportados

Pode utilizar as seguintes combinações de tipos de autorização e armazenamento azure:

|                     | Armazenamento de Blobs   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Suportado      | Não apoiado   | Suportado     |
| *Identidade Gerida* | Suportado      | Suportado        | Suportado     |
| *Identidade do Utilizador*    | Suportado      | Suportado        | Suportado     |

## <a name="credentials"></a>Credenciais

Para consultar um ficheiro localizado no Armazenamento Azure, o seu ponto final sQL on-demand precisa de uma credencial que contenha as informações de autenticação. São utilizados dois tipos de credenciais:
- O CREDENTIAL ao nível do servidor é utilizado para consultas ad-hoc executadas utilizando `OPENROWSET` a função. O nome credencial deve coincidir com o URL de armazenamento.
- BASE DE DADOS SCOPED CREDENTIAL é utilizado para tabelas externas. Referências externas à tabela `DATA SOURCE` com a credencial que deve ser utilizada para aceder ao armazenamento.

Para permitir que um utilizador crie ou deixe cair uma credencial, a administração pode conceder/NEGAR ALTERAR qualquer permissão credential a um utilizador:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Os utilizadores de bases de dados que acedam ao armazenamento externo devem ter permissão para utilizar credenciais.

### <a name="grant-permissions-to-use-credential"></a>Conceder permissões para usar credenciais

Para utilizar a credencial, o utilizador deve ter `REFERENCES` permissão numa credencial específica. Para conceder uma `REFERENCES` permissão on a storage_credential para um specific_user, execute:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Para garantir uma experiência de passagem de AD Azure suave, todos os utilizadores terão, por padrão, o direito de utilizar a `UserIdentity` credencial. Isto é conseguido através de uma execução automática da seguinte declaração sobre o fornecimento de espaço de trabalho Azure Synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="server-scoped-credential"></a>Credencial com mira de servidor

As credenciais de aplicação do servidor são utilizadas quando as chamadas de login SQL `OPENROWSET` funcionam sem ler ficheiros em alguma conta de `DATA_SOURCE` armazenamento. O nome da credencial com mira do servidor **deve** coincidir com o URL do armazenamento Azure. Uma credencial é adicionada executando [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Terá de fornecer um argumento de nome credentário. Deve coincidir com qualquer uma das partes do caminho ou todo o caminho para os dados em Armazenamento (ver abaixo).

> [!NOTE]
> O argumento do FORNECEDOR CRIPTOGRÁFICO não é suportado.

O nome CREDENTIAL ao nível do servidor deve coincidir com o caminho completo da conta de armazenamento (e opcionalmente recipiente) no seguinte formato: `<prefix>://<storage_account_path>/<storage_path>` . Os caminhos da conta de armazenamento são descritos no quadro seguinte:

| Fonte de Dados Externos       | Prefixo | Caminho da conta de armazenamento                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Armazenamento de Blobs do Azure         | https  | <storage_account>.blob.core.windows.net             |
| Armazenamento do Azure Data Lake Ger1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Armazenamento do Azure Data Lake Ger2 | https  | <storage_account>.dfs.core.windows.net              |

> [!NOTE]
> Existe um CREDENTIAL especial de nível de servidor que força a `UserIdentity` [passagem da AD Azure](?tabs=user-identity#force-azure-ad-pass-through).

As credenciais com o servidor permitem o acesso ao armazenamento Do Azure utilizando os seguintes tipos de autenticação:

### <a name="shared-access-signature"></a>[Assinatura de acesso partilhado](#tab/shared-access-signature)

O seguinte script cria uma credencial de nível de servidor que pode ser usada por `OPENROWSET` função para aceder a qualquer ficheiro no armazenamento Azure usando token SAS. Crie esta credencial para permitir que o principal Da SQL execute `OPENROWSET` a função de ler ficheiros protegidos com a chave SAS no armazenamento Azure que corresponda a URL em nome credencial.

Troque <nome da *minha conta de armazenamento*> com o seu nome real de conta de armazenamento e <nome de contentor de conta *mystorage*> com o nome real do recipiente:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="user-identity"></a>[Identidade do Utilizador](#tab/user-identity)

O seguinte script cria uma credencial ao nível do servidor que permite ao utilizador personificar usando a identidade Azure AD.

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

### <a name="managed-identity"></a>[Identidade Gerida](#tab/managed-identity)

O seguinte script cria uma credencial de nível de servidor que pode ser usada por `OPENROWSET` função para aceder a qualquer ficheiro no armazenamento Azure usando a identidade gerida pelo espaço de trabalho.

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[Acesso público](#tab/public-access)

O seguinte script cria uma credencial de nível de servidor que pode ser usada por `OPENROWSET` função para aceder a qualquer ficheiro no armazenamento Azure disponível ao público. Crie esta credencial para permitir que o principal Da SQL que executa `OPENROWSET` a função de ler ficheiros publicamente disponíveis no armazenamento do Azure que corresponda a URL em nome credencial.

Você precisaria de ter Exchange <*mystorage accountname*> com o seu nome de conta de armazenamento real, e <nome de *contentor de conta de armazenamento>* com o nome real do recipiente:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = '';
GO
```
---

## <a name="database-scoped-credential"></a>Credencial com âmbito de base de dados

As credenciais com um aplicativo de base de dados são usadas quando quaisquer chamadas principais `OPENROWSET` funcionam `DATA_SOURCE` ou selecionam dados de [tabelaexterna](develop-tables-external-tables.md) que não acedem a ficheiros públicos. A credencial de base de dados não precisa de corresponder ao nome da conta de armazenamento porque será explicitamente utilizada na FONTE DE DADOS que define a localização do armazenamento.

As credenciais com um espaço de dados permitem o acesso ao armazenamento do Azure utilizando os seguintes tipos de autenticação:

### <a name="shared-access-signature"></a>[Assinatura de acesso partilhado](#tab/shared-access-signature)

O seguinte script cria uma credencial que é usada para aceder a ficheiros no armazenamento usando token SAS especificado na credencial.

```sql
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="azure-ad-identity"></a>[Identidade Azure AD](#tab/user-identity)

O seguinte script cria uma credencial com um espaço de base de dados que é usada por [tabela externa](develop-tables-external-tables.md) e funções que utilizam fonte de dados com credencial para aceder a ficheiros de armazenamento usando a sua própria `OPENROWSET` identidade Azure AD.

```sql
CREATE DATABASE SCOPED CREDENTIAL [AzureAD]
WITH IDENTITY = 'User Identity';
GO
```

### <a name="managed-identity"></a>[Identidade Gerida](#tab/managed-identity)

O seguinte script cria uma credencial com um espaço de base de dados que pode ser usada para personificar o utilizador atual da AD Azure como Identidade Gerida de serviço. 

```sql
CREATE DATABASE SCOPED CREDENTIAL [SynapseIdentity]
WITH IDENTITY = 'Managed Identity';
GO
```

A credencial de base de dados não precisa de corresponder ao nome da conta de armazenamento porque será explicitamente utilizada na FONTE DE DADOS que define a localização do armazenamento.

### <a name="public-access"></a>[Acesso público](#tab/public-access)

A credencial de base de dados não é necessária para permitir o acesso a ficheiros disponíveis ao público. Criar fonte de [dados sem credenciais de](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) base de dados para aceder a ficheiros publicamente disponíveis no armazenamento do Azure.

---

As credenciais de aplicação da base de dados são utilizadas em fontes de dados externas para especificar qual o método de autenticação que será utilizado para aceder a este armazenamento:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://*******.blob.core.windows.net/samples',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Exemplos

**Acesso a fonte de dados publicamente disponível**

Utilize o seguinte script para criar uma tabela que aceda à fonte de dados publicamente disponível.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://****.blob.core.windows.net/public-access' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet', DATA_SOURCE = [publicData], FILE_FORMAT = [SynapseParquetFormat] )
```

O utilizador da base de dados pode ler o conteúdo dos ficheiros a partir da fonte de dados utilizando a tabela externa ou a função [OPENROWSET](develop-openrowset.md) que faz referência à fonte de dados:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT=PARQUET) as rows;
GO
```

**Aceder à fonte de dados utilizando credenciais**

Modifique o seguinte script para criar uma tabela externa que aceda ao armazenamento Azure utilizando token SAS, identidade Azure AD do utilizador ou identidade gerida do espaço de trabalho.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use User Identity, Managed Identity, or SAS. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL MyIdentity WITH IDENTITY = 'User Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://*******.blob.core.windows.net/samples'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = MyIdentity 
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FILE_FORMAT = [SynapseParquetFormat] )

```

O utilizador da base de dados pode ler o conteúdo dos ficheiros a partir da fonte de dados utilizando [a tabela externa](develop-tables-external-tables.md) ou a função [OPENROWSET](develop-openrowset.md) que faz referência à fonte de dados:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT=PARQUET) as rows;
GO
```

## <a name="next-steps"></a>Passos seguintes

Os artigos listados abaixo irão ajudá-lo a aprender como consulta diferentes tipos de pastas, tipos de ficheiros e criar e usar vistas:

- [Consulta single CSV arquivo](query-single-csv-file.md)
- [Questiões e vários ficheiros CSV](query-folders-multiple-csv-files.md)
- [Consulta de ficheiros específicos](query-specific-files.md)
- [Consultar ficheiros de Parquet](query-parquet-files.md)
- [Criar e utilizar vistas](create-use-views.md)
- [Consultar ficheiros JSON](query-json-files.md)
- [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md)
