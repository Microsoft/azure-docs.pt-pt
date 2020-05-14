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
ms.openlocfilehash: 6ebf23720d1d323b66671c6770ab2121c9091920
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197691"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>Acesso à conta de armazenamento de controlo para SQL a pedido (pré-visualização)

Uma consulta a pedido da SQL lê ficheiros diretamente do Armazenamento Azure. As permissões para aceder aos ficheiros do armazenamento do Azure são controladas a dois níveis:
- **Nível de armazenamento** - O utilizador deve ter permissão para aceder a ficheiros de armazenamento subjacentes. O seu administrador de armazenamento deve permitir que o diretor da AD Azure leia/escreva ficheiros ou gere a chave SAS que será usada para aceder ao armazenamento.
- **Nível de serviço SQL** - O utilizador deve ter `ADMINISTER BULK ADMIN` permissão para executar `OPENROWSET` e também permissão para usar credenciais que serão usadas para aceder ao armazenamento.

Este artigo descreve os tipos de credenciais que pode utilizar e como a procura credencial é decretada para os utilizadores de AD SQL e Azure.

## <a name="supported-storage-authorization-types"></a>Tipos de autorização de armazenamento suportados

Um utilizador que tenha iniciado um sessão num recurso SQL a pedido deve ser autorizado a aceder e consultar os ficheiros no Armazenamento Azure. São apoiados três tipos de autorização:

- [Assinatura de acesso partilhado](#shared-access-signature)
- [Identidade Gerida](#managed-identity)
- [Identidade do Utilizador](#user-identity)

> [!NOTE]
> [A passagem da AD Azure](#force-azure-ad-pass-through) é o comportamento padrão quando se cria um espaço de trabalho. Se o utilizar, não precisa de criar credenciais para cada conta de armazenamento acedida através de logins Azure AD. Pode [desativar este comportamento.](#disable-forcing-azure-ad-pass-through)

Na tabela abaixo pode encontrar os tipos de autorização disponíveis:

| Tipo de autorização                    | *Utilizador SQL*    | *Utilizador da AD Azure*     |
| ------------------------------------- | ------------- | -----------    |
| [SAS](#shared-access-signature)       | Suportado     | Suportado      |
| [Identidade Gerida](#managed-identity) | Não suportado | Suportado      |
| [Identidade do Utilizador](#user-identity)       | Não suportado | Suportado      |

### <a name="shared-access-signature"></a>Assinatura de acesso partilhado

A assinatura de **acesso partilhado (SAS)** proporciona acesso delegado aos recursos numa conta de armazenamento. Com a SAS, um cliente pode conceder aos clientes acesso aos recursos numa conta de armazenamento sem partilhar chaves de conta. A SAS dá-lhe controlo granular sobre o tipo de acesso que concede a clientes que tenham um Intervalo de Validade, incluindo intervalo de validade, permissões concedidas, intervalo de endereçoip aceitável e o protocolo aceitável (https/http).

Você pode obter um token SAS navegando para o portal Azure -> Conta de Armazenamento - > assinatura de **acesso partilhado -> permissões Configuradas -> Gerar SAS e cadeia de conexão.**

> [!IMPORTANT]
> Quando uma token SAS é gerada, inclui um ponto de interrogação ('?') no início do símbolo. Para utilizar o símbolo no SQL a pedido, deve remover o ponto de interrogação ('?') ao criar uma credencial. Por exemplo:
>
> Ficha sas: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSMANd0ef9BrIPBNJ3VYEIq78%3D

### <a name="user-identity"></a>Identidade do Utilizador

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

### <a name="managed-identity"></a>Identidade Gerida

**A Identidade Gerida** também é conhecida como MSI. É uma característica do Azure Ative Directory (Azure AD) que fornece serviços Azure para a SQL on-demand. Além disso, implementa uma identidade gerida automaticamente em Azure AD. Esta identidade pode ser utilizada para autorizar o pedido de acesso de dados no Armazenamento Azure.

Antes de aceder aos dados, o administrador de Armazenamento Azure deve conceder permissões à Identidade Gerida para aceder aos dados. A concessão de permissões à Identidade Gerida é feita da mesma forma que a concessão de permissão a qualquer outro utilizador da AD Azure.

## <a name="credentials"></a>Credenciais

Para consultar um ficheiro localizado no Armazenamento Azure, o seu ponto final sQL on-demand precisa de uma credencial que contenha as informações de autenticação. São utilizados dois tipos de credenciais:
- O CREDENTIAL ao nível do servidor é utilizado para consultas ad-hoc executadas utilizando `OPENROWSET` a função. O nome credencial deve coincidir com o URL de armazenamento.
- BASE DE DADOS SCOPED CREDENTIAL é utilizado para tabelas externas. Referências externas à tabela `DATA SOURCE` com a credencial que deve ser utilizada para aceder ao armazenamento.

Uma credencial é adicionada executando [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Terá de fornecer um argumento de nome credentário. Deve coincidir com qualquer uma das partes do caminho ou todo o caminho para os dados em Armazenamento (ver abaixo).

> [!NOTE]
> O argumento do FORNECEDOR CRIPTOGRÁFICO não é suportado.

Para todos os tipos de autorização suportados, as credenciais podem apontar para uma conta ou um recipiente.

O nome CREDENTIAL ao nível do servidor deve coincidir com o caminho completo da conta de armazenamento (e opcionalmente recipiente) no seguinte formato:`<prefix>://<storage_account_path>/<storage_path>`

| Fonte de Dados Externos       | Prefixo | Caminho da conta de armazenamento                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Armazenamento de Blobs do Azure         | https  | <storage_account>.blob.core.windows.net             |
| Armazenamento do Azure Data Lake Ger1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Armazenamento do Azure Data Lake Ger2 | https  | <storage_account>.dfs.core.windows.net              |


> [!NOTE]
> Existe um CREDENTIAL especial de nível de servidor que força a `UserIdentity` [passagem da AD Azure](#force-azure-ad-pass-through).

Opcionalmente, para permitir que um utilizador crie ou deixe cair uma credencial, a administração pode CONCEDER/NEGAR ALTERAR qualquer permissão CREDENTIAL a um utilizador:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Armazenamentos e tipos de autorização suportados

Pode utilizar as seguintes combinações de tipos de autorização e armazenamento azure:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Suportado      | Não apoiado   | Suportado     |
| *Identidade Gerida* | Suportado      | Suportado        | Suportado     |
| *Identidade do Utilizador*    | Suportado      | Suportado        | Suportado     |


### <a name="grant-permissions-to-use-credential"></a>Conceder permissões para usar credenciais

Para utilizar a credencial, o utilizador deve ter permissão de REFERÊNCIAs numa credencial específica. Para conceder uma permissão de referências sobre um storage_credential para um specific_user, execute:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Para garantir uma experiência de passagem de AD Azure suave, todos os utilizadores terão, por padrão, o direito de utilizar a `UserIdentity` credencial. Isto é conseguido através de uma execução automática da seguinte declaração sobre o fornecimento de espaço de trabalho Azure Synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

### <a name="examples"></a>Exemplos

Dependendo do tipo de [autorização,](#supported-storage-authorization-types)pode criar credenciais utilizando a sintaxe T-SQL abaixo.
- As credenciais de aplicação do servidor são utilizadas quando as chamadas de login SQL `OPENROWSET` funcionam sem ler ficheiros em alguma conta de `DATA_SOURCE` armazenamento. O nome da credencial com mira do servidor **deve** coincidir com o URL do armazenamento Azure.
- As credenciais com um aplicativo de base de dados são usadas quando quaisquer chamadas principais `OPENROWSET` funcionam `DATA_SOURCE` ou selecionam dados de tabelaexterna que não acedem a ficheiros públicos. A credencial de base de dados não precisa de corresponder ao nome da conta de armazenamento porque será explicitamente utilizada na FONTE DE DADOS que define a localização do armazenamento.

**Credencial com mira de servidor com assinatura de acesso partilhado para armazenamento blob**

O seguinte script cria uma credencial de nível de servidor que pode ser usada para aceder a qualquer ficheiro no armazenamento Azure usando token SAS. Crie esta credencial para permitir que o principal Da SQL execute `OPENROWSET` a função de ler ficheiros protegidos com a chave SAS no armazenamento Azure que corresponda a URL em nome credencial.

Troque <nome da *minha conta de armazenamento*> com o seu nome real de conta de armazenamento e <nome de contentor de conta *mystorage*> com o nome real do recipiente:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Credencial com mira de servidor que permite o acesso ao armazenamento público**

O seguinte script cria uma credencial de nível de servidor que pode ser usada para aceder a qualquer ficheiro no armazenamento Azure disponível ao público. Crie esta credencial para permitir que o principal Da SQL que executa `OPENROWSET` a função de ler ficheiros publicamente disponíveis no armazenamento do Azure que corresponda a URL em nome credencial.

Você precisaria de ter Exchange <*mystorage accountname*> com o seu nome de conta de armazenamento real, e <nome de *contentor de conta de armazenamento>* com o nome real do recipiente:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = '';
GO
```

**Credencial com margem de dados com token SAS**

O seguinte script cria uma credencial que é usada para aceder a ficheiros no armazenamento usando token SAS especificado na credencial.

```sql
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Credencial com código de base de dados com identidade AD Azure**

O seguinte script cria uma credencial que é usada por tabelas e funções externas que utilizam fonte de dados com credencial para aceder a ficheiros de armazenamento usando a `OPENROWSET` sua própria identidade Azure AD.

```sql
CREATE DATABASE SCOPED CREDENTIAL [AzureAD]
WITH IDENTITY = 'User Identity';
GO
```

**Credencial com código de base de dados com identidade gerida**

O seguinte script cria uma credencial que pode ser usada para personificar o utilizador atual da AD Azure como Identidade Gerida de serviço. 

```sql
CREATE DATABASE SCOPED CREDENTIAL [SynapseIdentity]
WITH IDENTITY = 'Managed Identity';
GO
```

A credencial de base de dados não precisa de corresponder ao nome da conta de armazenamento porque será explicitamente utilizada na FONTE DE DADOS que define a localização do armazenamento.

## <a name="next-steps"></a>Próximos passos

Os artigos listados abaixo irão ajudá-lo a aprender como consulta diferentes tipos de pastas, tipos de ficheiros e criar e usar vistas:

- [Consulta single CSV arquivo](query-single-csv-file.md)
- [Questiões e vários ficheiros CSV](query-folders-multiple-csv-files.md)
- [Consulta de ficheiros específicos](query-specific-files.md)
- [Consultar ficheiros de Parquet](query-parquet-files.md)
- [Criar e utilizar vistas](create-use-views.md)
- [Consultar ficheiros JSON](query-json-files.md)
- [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md)
