---
title: Aceder a ficheiros de armazenamento na piscina SQL sem servidor (pré-visualização)
description: Descreve ficheiros de armazenamento de consulta usando piscina SQL sem servidor (pré-visualização) em Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 73a5414a979742c4a7df16dcd2a5edda3748abef
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315928"
---
# <a name="access-external-storage-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Aceda ao armazenamento externo utilizando piscina SQL sem servidor (pré-visualização) no Azure Synapse Analytics

Este documento descreve como os utilizadores podem ler dados dos ficheiros armazenados no Azure Storage na piscina SQL sem servidor. Os utilizadores têm as seguintes opções de acesso ao armazenamento:

- [Função OPENROWSET](develop-openrowset.md) que permite consultas ad-hoc sobre os ficheiros no Azure Storage.
- [Tabela externa](develop-tables-external-tables.md) que é uma estrutura de dados predefinida construída em cima de conjunto de ficheiros externos.

O utilizador pode utilizar [diferentes métodos de autenticação,](develop-storage-files-storage-access-control.md) tais como a autenticação passthrough Azure AD (predefinição para os principais AD Azure) e a autenticação SAS (predefinição para os principais SQL).

## <a name="query-files-using-openrowset"></a>Ficheiros de consulta utilizando OPENROWSET

O OPENROWSET permite que os utilizadores consultam ficheiros externos sobre o armazenamento do Azure se tiverem acesso ao armazenamento. Um utilizador que esteja ligado ao pool SQL sem servidor deve utilizar a seguinte consulta para ler o conteúdo dos ficheiros no armazenamento Azure:

```sql
SELECT * FROM
 OPENROWSET(BULK 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/*.parquet', format= 'parquet') as rows
```

O utilizador pode aceder ao armazenamento utilizando as seguintes regras de acesso:

- Utilizador Azure AD - `OPENROWSET` utilizará a identidade Azure AD do chamador para aceder ao Armazenamento Azure ou ao armazenamento de acesso com acesso anónimo.
- Utilizador SQL – `OPENROWSET` acederá ao armazenamento com acesso anónimo ou pode ser personificado usando o token SAS ou identidade gerida do espaço de trabalho.

### <a name="impersonation"></a>[Personificação](#tab/impersonation)

Os principais SQL também podem usar OPENROWSET para consultar diretamente ficheiros protegidos com fichas SAS ou Identidade Gerida do espaço de trabalho. Se um utilizador SQL executar esta função, um utilizador de energia com `ALTER ANY CREDENTIAL` permissão deve criar uma credencial com âmbito de servidor que corresponda a URL na função (utilizando o nome de armazenamento e o recipiente) e concedeu permissão references para esta credencial ao chamador da função OPENROWSET:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[https://<storage_account>.dfs.core.windows.net/<container>] TO sqluser
```

Se não houver credencial de nível de servidor que corresponda ao URL, ou se o utilizador SQL não tiver permissão de referências para esta credencial, o erro será devolvido. Os diretores da SQL não podem fazer-se passar por uma identidade AD da Azure.

### <a name="direct-access"></a>[Acesso direto](#tab/direct-access)

Não é necessária nenhuma configuração adicional para permitir que os utilizadores de Azure AD acedam aos ficheiros usando as suas identidades.
Qualquer utilizador pode aceder ao armazenamento Azure que permite o acesso anónimo (não é necessária uma configuração adicional).

---

> [!NOTE]
> Esta versão do OPENROWSET foi concebida para a exploração rápida e fácil de dados utilizando a autenticação predefinida. Para alavancar a personificação ou identidade gerida, utilize OPENROWSET com DATASOURCE descrito na secção seguinte.

## <a name="query-data-sources-using-openrowset"></a>Fontes de dados de consulta usando OPENROWSET

O OPENROWSET permite ao utilizador consultar os ficheiros colocados em alguma fonte de dados externa:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

O utilizador que executa esta consulta deve poder aceder aos ficheiros. Os utilizadores devem ser personificados usando [o token SAS](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) ou [identidade gerida do espaço de trabalho](develop-storage-files-storage-access-control.md?tabs=managed-identity) se não puderem aceder diretamente aos ficheiros usando a sua identidade [AD Azure](develop-storage-files-storage-access-control.md?tabs=user-identity) ou [acesso anónimo](develop-storage-files-storage-access-control.md?tabs=public-access).

### <a name="impersonation"></a>[Personificação](#tab/impersonation)

`DATABASE SCOPED CREDENTIAL` especifica como aceder a ficheiros na fonte de dados referenciada (atualmente SAS e Identidade Gerida). O utilizador de energia com `CONTROL DATABASE` permissão teria de criar `DATABASE SCOPED CREDENTIAL` que fosse usado para aceder ao armazenamento e que especifica o URL da fonte de `EXTERNAL DATA SOURCE` dados e da credencial que deve ser usado:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

O chamador deve ter uma das seguintes permissões para executar a função OPENROWSET:

- Uma das permissões para executar OPENROWSET:
  - `ADMINISTER BULK OPERATIONS` permite o login executar a função OPENROWSET.
  - `ADMINISTER DATABASE BULK OPERATIONS` permite que o utilizador de base de dados executa a função OPENROWSET.
- `REFERENCES DATABASE SCOPED CREDENTIAL` à credencial referida em `EXTERNAL DATA SOURCE` .

### <a name="direct-access"></a>[Acesso direto](#tab/direct-access)

O utilizador pode criar FONTE DE DADOS EXTERNA sem CREDENCIAL que irá fazer referência ao armazenamento de acesso público ou utilizar a azure AD passthrough a autenticação:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---
## <a name="external-table"></a>TABELA EXTERNA

O utilizador com as permissões para ler a tabela pode aceder a ficheiros externos utilizando uma TABELA EXTERNA criada em cima do conjunto de pastas e ficheiros de armazenamento Azure.

O utilizador que tem [permissões para criar tabela externa](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions&preserve-view=true) (por exemplo, CRIAR TABELA e ALTERAR QUALQUER CREDENCIAL ou REFERÊNCIAS BASE DE DADOS SCOPED CREDENCIAL) pode utilizar o seguinte script para criar uma tabela em cima da fonte de dados de armazenamento Azure:

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

O utilizador que lê os dados desta tabela deve poder aceder aos ficheiros. Os utilizadores devem ser personificados utilizando [o token SAS](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) ou [identidade gerida do espaço de trabalho](develop-storage-files-storage-access-control.md?tabs=managed-identity) se não puderem aceder diretamente aos ficheiros utilizando a sua identidade [AD Azure](develop-storage-files-storage-access-control.md?tabs=user-identity) ou [acesso anónimo.](develop-storage-files-storage-access-control.md?tabs=public-access)

### <a name="impersonation"></a>[Personificação](#tab/impersonation)

BASE DE DADOS SCOPED CREDENTIAL especifica como aceder a ficheiros na fonte de dados referenciada. O utilizador com permissão de BASE DE DADOS DE CONTROLO teria de criar credencial SCOPED DE BASE DE DADOS QUE será utilizada para aceder ao armazenamento e fonte de dados externa que especifica o URL da fonte de dados e da credencial que deve ser utilizada:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>' ,
 CREDENTIAL = cred
 ) ;
```

### <a name="direct-access"></a>[Acesso direto](#tab/direct-access)

O utilizador pode criar FONTE DE DADOS EXTERNA sem CREDENCIAL que irá fazer referência ao armazenamento de acesso público ou utilizar a azure AD passthrough a autenticação:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---

### <a name="read-external-files-with-external-table"></a>Ler ficheiros externos com TABELA EXTERNA

TABELA EXTERNA permite-lhe ler dados a partir dos ficheiros que são referenciados através de fonte de dados usando a declaração padrão SQL SELECT:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

O chamador deve ter as seguintes permissões para ler dados:
- `SELECT` permissão ON tabela externa
- `REFERENCES DATABASE SCOPED CREDENTIAL` permissão se `DATA SOURCE` tiver `CREDENTIAL`

## <a name="permissions"></a>Permissões

As seguintes listas de tabelas requeriam permissões para as operações acima enumeradas.

| Consulta | Permissões obrigatórias|
| --- | --- |
| OPENROWSET (BULK) sem fonte de dados | `ADMINISTER BULK OPERATIONS`, `ADMINISTER DATABASE BULK OPERATIONS` ou o login SQL deve ter REFERÊNCIAS CREDENTIAL:: para armazenamento protegido por \<URL> SAS |
| OPENROWSET (GRANEL) com fonte de dados sem credencial | `ADMINISTER BULK OPERATIONS``ADMINISTER DATABASE BULK OPERATIONS`ou, |
| OPENROWSET (GRANEL) com fonte de dados com credencial | `REFERENCES DATABASE SCOPED CREDENTIAL` e um dos `ADMINISTER BULK OPERATIONS` ou `ADMINISTER DATABASE BULK OPERATIONS` |
| CRIAR FONTE DE DADOS EXTERNA | `ALTER ANY EXTERNAL DATA SOURCE` e `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CRIAR TABELA EXTERNA | `CREATE TABLE`, `ALTER ANY SCHEMA` `ALTER ANY EXTERNAL FILE FORMAT` e `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECIONE A PARTIR DA TABELA EXTERNA | `SELECT TABLE` e `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | Para criar mesa - `CREATE TABLE` , , , e `ALTER ANY SCHEMA` `ALTER ANY DATA SOURCE` `ALTER ANY EXTERNAL FILE FORMAT` . Para ler dados: `ADMINISTER BULK OPERATIONS` ou `REFERENCES CREDENTIAL` por cada `SELECT TABLE` tabela/visualização/função em consulta + permissão R/W no armazenamento |

## <a name="next-steps"></a>Passos seguintes

Está agora pronto para continuar com os seguintes artigos:

- [Dados de consulta sobre o armazenamento](query-data-storage.md)

- [Consultar ficheiro CSV](query-single-csv-file.md)

- [Consultar ficheiros de Parquet](query-parquet-files.md)

- [Consultar ficheiros JSON](query-json-files.md)

- [Consultar pastas e vários ficheiros](query-folders-multiple-csv-files.md)

- [Utilizar funções de partição e metadados](query-specific-files.md)

- [Consultar tipos aninhados](query-parquet-nested-types.md)

- [Criar e usar vistas](create-use-views.md)
