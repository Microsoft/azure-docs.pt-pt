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
ms.openlocfilehash: 0d2683091898e9c84457b3b538776f0e6b0469d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424035"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Acesso à conta de armazenamento de controlo para SQL on-demand (pré-visualização) em Azure Synapse Analytics

Uma consulta sql on-demand (pré-visualização) lê ficheiros diretamente do Armazenamento Azure. Uma vez que a conta de armazenamento é um objeto externo ao recurso sql on-demand, são necessárias credenciais adequadas. Um utilizador necessita das permissões aplicáveis concedidas para utilizar a credencial necessária. Este artigo descreve os tipos de credenciais que pode utilizar e como a procura credencial é decretada para os utilizadores de AD SQL e Azure.

## <a name="supported-storage-authorization-types"></a>Tipos de autorização de armazenamento suportados

Um utilizador que tenha iniciado um sessão num recurso SQL a pedido deve ser autorizado a aceder e consultar os ficheiros no Armazenamento Azure. São apoiados três tipos de autorização:

- [Assinatura de acesso partilhado](#shared-access-signature)
- [Identidade Gerida](#managed-identity)
- [Identidade do Utilizador](#user-identity)

> [!NOTE]
> [A passagem da AD Azure](#force-azure-ad-pass-through) é o comportamento padrão quando se cria um espaço de trabalho. Se o utilizar, não precisa de criar credenciais para cada conta de armazenamento acedida através de logins ad. Pode [desativar este comportamento.](#disable-forcing-azure-ad-pass-through)

Na tabela abaixo encontrará os diferentes tipos de autorização que são suportados ou serão suportados em breve.

| Tipo de autorização                    | *Utilizador SQL*    | *Utilizador da AD Azure*     |
| ------------------------------------- | ------------- | -----------    |
| [SAS](#shared-access-signature)       | Suportado     | Suportado      |
| [Identidade Gerida](#managed-identity) | Não suportado | Não suportado  |
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

> [!NOTE]
> Se utilizar o [passe Azure AD,](#force-azure-ad-pass-through) não precisa de criar credenciais para cada conta de armazenamento acedida através de logins ad.

> [!IMPORTANT]
> É necessário ter uma função de Proprietário/Colaborador/Leitor de Armazenamento Blob para utilizar a sua identidade para aceder aos dados.
> Mesmo que seja proprietário de uma Conta de Armazenamento, ainda precisa de se adicionar a uma das funções de Dados blob de armazenamento.
>
> Para saber mais sobre o controlo de acessos na Azure Data Lake Store Gen2, reveja o controlo de acesso no artigo [da Azure Data Lake Storage Gen2.](../../storage/blobs/data-lake-storage-access-control.md)
>

### <a name="managed-identity"></a>Identidade Gerida

**A Identidade Gerida** também é conhecida como MSI. É uma característica do Azure Ative Directory (Azure AD) que fornece serviços Azure para a SQL on-demand. Além disso, implementa uma identidade gerida automaticamente em Azure AD. Esta identidade pode ser utilizada para autorizar o pedido de acesso de dados no Armazenamento Azure.

Antes de aceder aos dados, o administrador de Armazenamento Azure deve conceder permissões à Identidade Gerida para aceder aos dados. A concessão de permissões à Identidade Gerida é feita da mesma forma que a concessão de permissão a qualquer outro utilizador da AD Azure.

## <a name="create-credentials"></a>Criar credenciais

Para consultar um ficheiro localizado no Armazenamento Azure, o seu ponto final sQL on-demand precisa de um CREDENTIAL de nível de servidor que contenha as informações de autenticação. Uma credencial é adicionada executando [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Terá de fornecer um argumento de nome credentário. Deve coincidir com qualquer uma das partes do caminho ou todo o caminho para os dados em Armazenamento (ver abaixo).

> [!NOTE]
> O argumento do FORNECEDOR CRIPTOGRÁFICO não é suportado.

Para todos os tipos de autorização suportados, as credenciais podem apontar para uma conta, um recipiente, qualquer diretório (não raiz) ou um único ficheiro.

O NOME CREDENTIAL deve coincidir o caminho completo com o recipiente, pasta ou ficheiro, no seguinte formato:`<prefix>://<storage_account_path>/<storage_path>`

| Fonte de Dados Externos       | Prefixo | Caminho da conta de armazenamento                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Armazenamento de Blobs do Azure         | https  | <storage_account>.blob.core.windows.net             |
| Armazenamento do Azure Data Lake Ger1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Armazenamento do Azure Data Lake Ger2 | https  | <storage_account>.dfs.core.windows.net              |

 '<storage_path>' é um caminho dentro do seu armazenamento que aponta para a pasta ou ficheiro que pretende ler.

> [!NOTE]
> Existe um nome `UserIdentity` credentário especial que força a passagem da [AD Azure](#force-azure-ad-pass-through). Por favor, leia sobre o efeito que tem na [procura de credenciais](#credential-lookup) enquanto executa consultas.

Opcionalmente, para permitir que um utilizador crie ou deixe cair uma credencial, a administração pode CONCEDER/NEGAR ALTERAR qualquer permissão CREDENTIAL a um utilizador:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

### <a name="supported-storages-and-authorization-types"></a>Armazenamentos e tipos de autorização suportados

Pode utilizar as seguintes combinações de tipos de autorização e armazenamento azure:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Suportado      | Não apoiado   | Suportado     |
| *Identidade Gerida* | Não suportado  | Não suportado    | Não suportado |
| *Identidade do Utilizador*    | Suportado      | Suportado        | Suportado     |

### <a name="examples"></a>Exemplos

Dependendo do tipo de [autorização,](#supported-storage-authorization-types)pode criar credenciais utilizando a sintaxe T-SQL abaixo.

**Assinatura de acesso partilhado e armazenamento de blob**

Troque <nome da *minha conta de armazenamento*> com o seu nome real de conta de armazenamento e <nome de contentor de conta *mystorage*> com o nome real do recipiente:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

**Identidade do utilizador e armazenamento de lagos de dados azure Gen1**

Troque <nome da *minha conta de armazenamento*> com o seu nome real de conta de armazenamento e <nome de contentor de conta *mystorage*> com o nome real do recipiente:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.azuredatalakestore.net/webhdfs/v1/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

**Identidade do utilizador e armazenamento de lagos de dados azure Gen2**

Troque <nome da *minha conta de armazenamento*> com o seu nome real de conta de armazenamento e <nome de contentor de conta *mystorage*> com o nome real do recipiente:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='User Identity';
GO
```

## <a name="force-azure-ad-pass-through"></a>Passe da Force Azure AD

Forçar um passe de AD Azure é um comportamento padrão alcançado `UserIdentity`por um NOME CREDENTIAL especial, que é criado automaticamente durante o fornecimento de espaço de trabalho Azure Synapse. Força o uso de uma passagem de AD Azure para cada consulta de cada login Azure AD, que ocorrerá apesar da existência de outras credenciais.

> [!NOTE]
> A passagem da AD Azure é um comportamento padrão. Não precisa de criar credenciais para cada conta de armazenamento acedida por logins ad.

No caso [de ter desativado forçando a passagem do Azure AD para cada consulta](#disable-forcing-azure-ad-pass-through), e quiser permitir novamente, execute:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Para permitir a imposição de um passe de AD Azure para `UserIdentity` um utilizador específico, pode conceder permissão de REFERÊNCIA na credencial a esse utilizador em particular. O exemplo seguinte permite forçar uma passagem da AD Azure para um user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Para obter mais informações sobre como a SQL a pedido encontra credenciais para usar, consulte a [procura de credenciais](#credential-lookup).

## <a name="disable-forcing-azure-ad-pass-through"></a>Desativar forçando a passagem da AD Azure

Pode desativar [forçando a passagem do Azure AD para cada consulta](#force-azure-ad-pass-through). Para desativá-lo, deixe cair a `Userdentity` credencial utilizando:

```sql
DROP CREDENTIAL [UserIdentity];
```

Se quiser voltar a enactivar, consulte a secção de passagem da [Azure AD.](#force-azure-ad-pass-through)

Para desativar a imperdoação do Azure AD para um `UserIdentity` utilizador específico, pode negar uma permissão de REFERÊNCIA na credencial para um determinado utilizador. O exemplo seguinte desativa uma passagem de AD Azure para um user_name:

```sql
DENY REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

Para obter mais informações sobre como a SQL a pedido encontra credenciais para usar, consulte a [procura de credenciais](#credential-lookup).

## <a name="grant-permissions-to-use-credential"></a>Conceder permissões para usar credenciais

Para utilizar a credencial, o utilizador deve ter permissão de REFERÊNCIAs numa credencial específica. Para conceder uma permissão de referências sobre um storage_credential para um specific_user, execute:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Para garantir uma experiência de passagem de AD Azure suave, todos os `UserIdentity` utilizadores terão, por padrão, o direito de utilizar a credencial. Isto é conseguido através de uma execução automática da seguinte declaração sobre o fornecimento de espaço de trabalho Azure Synapse:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="credential-lookup"></a>Procura credencial

Ao autorizar consultas, a procura de credenciais é utilizada para aceder a uma conta de armazenamento e baseia-se nas seguintes regras:

- O utilizador é iniciado como um login azure ad

  - Se existir uma credencial de Identidade de Utilizador, e o utilizador tiver permissões de referência, será utilizado o passe da AD Azure, caso contrário, [procure credenciais por caminho](#lookup-credential-by-path)

- O utilizador é iniciado como um login SQL

  - Use [credenciais de procura por caminho](#lookup-credential-by-path)

### <a name="lookup-credential-by-path"></a>Credencial de procura por caminho

Se forçar a passagem da AD Azure for desativada, a procura de credenciais basear-se-á no caminho de armazenamento (profundidade em primeiro lugar) e na existência de uma permissão de REFERÊNCIAs nessa credencial específica. Quando existem várias credenciais que podem ser usadas para aceder ao mesmo ficheiro, a SQL on-demand utilizará a mais específica.  

Abaixo está um exemplo de uma consulta sobre o seguinte caminho de arquivo: *account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX.ext*

A procura credencial será concluída por esta ordem:

```
account.dfs.core.windows.net/filesystem/folder1/.../folderN/fileX
account.dfs.core.windows.net/filesystem/folder1/.../folderN
account.dfs.core.windows.net/filesystem/folder1
account.dfs.core.windows.net/filesystem
account.dfs.core.windows.net
```

Se um utilizador não tiver permissão de referências no número 5 da credencial, a SQL a pedido verificará se o utilizador tem permissão de referências numa credencial que é um nível mais alto até localizar as credenciais em que o utilizador tem permissão de REFERÊNCIAs. Se não for encontrada tal permissão, uma mensagem de erro será devolvida.

### <a name="credential-and-path-level"></a>Credencial e nível de caminho

Dependendo da forma do caminho que deseja, estão em vigor os seguintes requisitos para a execução de consultas:

- Se a consulta estiver direcionada para vários ficheiros (pastas, com ou sem cartões selvagens), o utilizador precisa de ter acesso a uma credencial pelo menos no nível de diretório raiz (nível de recipiente). Este nível de acesso é necessário uma vez que os ficheiros de listagem são relativos ao diretório raiz (limitações de armazenamento azure)
- Se a consulta estiver direcionada para um único ficheiro, o utilizador precisa de ter acesso a uma credencial em qualquer nível, uma vez que a SQL on-demand acede diretamente ao ficheiro, ou seja, sem listar pastas.

|                  | *Conta* | *Diretório de raiz* | *Qualquer outro diretório* | *Ficheiro*        |
| ---------------- | --------- | ---------------- | --------------------- | ------------- |
| *Ficheiro único*    | Suportado | Suportado        | Suportado             | Suportado     |
| *Vários ficheiros* | Suportado | Suportado        | Não suportado         | Não suportado |

## <a name="next-steps"></a>Passos seguintes

Os artigos listados abaixo irão ajudá-lo a aprender como consulta diferentes tipos de pastas, tipos de ficheiros e criar e usar vistas:

- [Consulta single CSV arquivo](query-single-csv-file.md)
- [Questiões e vários ficheiros CSV](query-folders-multiple-csv-files.md)
- [Consulta de ficheiros específicos](query-specific-files.md)
- [Arquivos De Consulta Parquet](query-parquet-files.md)
- [Criar e usar vistas](create-use-views.md)
- [Consulta de ficheiros JSON](query-json-files.md)
- [Tipos aninhados de Parquet de consulta](query-parquet-nested-types.md)
