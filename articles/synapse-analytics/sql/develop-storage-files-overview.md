---
title: Aceder a ficheiros de armazenamento utilizando SQL on-demand (pré-visualização) dentro do SQL synapse
description: Descreve ficheiros de armazenamento de consulta usando recursos SQL on-demand (pré-visualização) dentro do SQL synapse.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: c251b70d1988be82821f1e133151dae1ac6d1bc9
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921293"
---
# <a name="accessing-external-storage-in-synapse-sql-on-demand"></a>Acesso ao armazenamento externo em Synapse SQL (a pedido)

Este documento descreve como pode o utilizador ler dados dos ficheiros armazenados no Azure Storage em Synapse SQL (a pedido). Os utilizadores têm as seguintes opções de acesso ao armazenamento:

- [Função OPENROWSET](develop-openrowset.md) que permite consultas ad-hoc sobre os ficheiros no Azure Storage.
- [Tabela externa](develop-tables-external-tables.md) que é uma estrutura de dados predefinida construída em cima de conjunto de ficheiros externos.

O utilizador pode utilizar [diferentes métodos de autenticação,](develop-storage-files-storage-access-control.md) tais como a autenticação passthrough Azure AD (predefinição para os principais AD Azure) e a autenticação SAS (predefinição para os principais SQL).

## <a name="openrowset"></a>OPENROWSET

A função [OPENROWSET](develop-openrowset.md) permite ao utilizador ler os ficheiros a partir do armazenamento do Azure.

### <a name="query-files-using-openrowset"></a>Ficheiros de consulta utilizando OPENROWSET

O OPENROWSET permite que os utilizadores consultam ficheiros externos sobre o armazenamento do Azure se tiverem acesso ao armazenamento. O utilizador que esteja ligado ao ponto final a pedido do Synapse SQL deve utilizar a seguinte consulta para ler o conteúdo dos ficheiros no armazenamento Azure:

```sql
SELECT * FROM
 OPENROWSET(BULK 'http://storage...com/container/file/path/*.csv', format= 'parquet') as rows
```

O utilizador pode aceder ao armazenamento utilizando as seguintes regras de acesso:

- Utilizador Azure AD - OPENROWSET utilizará a identidade AD do chamador Azure para aceder ao Armazenamento Azure ou ao armazenamento de acesso com acesso anónimo.
- Utilizador SQL – OPENROWSET acederá ao armazenamento com acesso anónimo.

Os principais SQL também podem usar OPENROWSET para consultar diretamente ficheiros protegidos com fichas SAS ou Identidade Gerida do espaço de trabalho. Se um utilizador SQL executar esta função, um utilizador de energia com permissão DE CREDENCIAL ALTER QUALQUER DEVE CRIAR uma credencial com âmbito de servidor que corresponda a URL na função (utilizando o nome de armazenamento e o recipiente) e concedeu permissão references para esta credencial ao autor da função OPENROWSET:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [http://storage.dfs.com/container]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[http://storage.dfs.com/container] TO sqluser
```

Se não houver credencial ao nível do servidor que corresponda a URL ou SQL o utilizador não tem permissão de referências para esta credencial, o erro será devolvido. Os principais da SQL não podem fazer-se passar por alguma identidade AD Azure.

> [!NOTE]
> Esta versão do OPENROWSET foi concebida para a exploração rápida e fácil de dados utilizando a autenticação predefinida. Para alavancar a personificação ou identidade gerida, utilize OPENROWSET com DATASOURCE descrito na secção seguinte.

### <a name="querying-data-sources-using-openrowset"></a>Consulta de fontes de dados utilizando OPENROWSET

O OPENROWSET permite ao utilizador consultar os ficheiros colocados em alguma fonte de dados externa:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

O utilizador de energia com permissão de BASE DE DADOS de CONTROLO teria de criar credencial SCOPED DE BASE DE DADOS que será utilizada para aceder ao armazenamento e fonte de dados externa que especifica o URL da fonte de dados e da credencial que deve ser utilizada:

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

BASE DE DADOS SCOPED CREDENTIAL especifica como aceder a ficheiros na fonte de dados referenciada (atualmente SAS e Identidade Gerida).

O chamador deve ter uma das seguintes permissões para executar a função OPENROWSET:

- Uma das permissões para executar OPENROWSET:
  - ADMINISTRAR OPERAÇÃO A GRANEL permite que o login execute a função OPENROWSET.
  - ADMINISTRAR A OPERAÇÃO A GRANEL DA BASE DE DADOS permite ao utilizador com uma base de dados executar a função OPENROWSET.
- REFERÊNCIAS BASE DE DADOS SCOPED CREDENCIAL À credencial que é referenciada em FONTE DE DADOS EXTERNOS

#### <a name="accessing-anonymous-data-sources"></a>Aceder a fontes de dados anónimas

O utilizador pode criar FONTE DE DADOS EXTERNA sem CREDENCIAL que irá fazer referência ao armazenamento de acesso público ou utilizar a azure AD passthrough a autenticação:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3') ;
```

## <a name="external-table"></a>TABELA EXTERNA

O utilizador com as permissões para ler a tabela pode aceder a ficheiros externos utilizando uma TABELA EXTERNA criada em cima do conjunto de pastas e ficheiros de armazenamento Azure.

O utilizador que tem [permissões para criar tabela externa](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions) (por exemplo, CRIAR TABELA e ALTERAR QUALQUER CREDENCIAL ou REFERÊNCIAS BASE DE DADOS SCOPED CREDENCIAL) pode utilizar o seguinte script para criar uma tabela em cima da fonte de dados de armazenamento Azure:

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

O utilizador com permissão de BASE DE DADOS DE CONTROLO teria de criar credencial SCOPED DE BASE DE DADOS QUE será utilizada para aceder ao armazenamento e fonte de dados externa que especifica o URL da fonte de dados e da credencial que deve ser utilizada:

```sql
CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://samples.blob.core.windows.net/products' ,
 CREDENTIAL = cred
 ) ;
```

BASE DE DADOS SCOPED CREDENTIAL especifica como aceder a ficheiros na fonte de dados referenciada.

### <a name="reading-external-files-with-external-table"></a>Leitura de ficheiros externos com TABELA EXTERNA

TABELA EXTERNA permite-lhe ler dados a partir dos ficheiros que são referenciados através de fonte de dados usando a declaração padrão SQL SELECT:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

O chamador deve ter as seguintes permissões para ler dados:
- `SELECT`permissão ON tabela externa
- `REFERENCES DATABASE SCOPED CREDENTIAL`permissão se `DATA SOURCE` tiver`CREDENTIAL`

## <a name="permissions"></a>Permissões

As seguintes listas de tabelas requeriam permissões para as operações acima enumeradas.

| Consulta | Permissões obrigatórias|
| --- | --- |
| OPENROWSET (BULK) sem fonte de dados | `ADMINISTER BULK ADMIN`, `ADMINISTER DATABASE BULK ADMIN` ou o login SQL deve ter REFERÊNCIAS CREDENTIAL:: para armazenamento protegido por \<URL> SAS |
| OPENROWSET (GRANEL) com fonte de dados sem credencial | `ADMINISTER BULK ADMIN``ADMINISTER DATABASE BULK ADMIN`ou, |
| OPENROWSET (GRANEL) com fonte de dados com credencial | `ADMINISTER BULK ADMIN`, `ADMINISTER DATABASE BULK ADMIN` ou`REFERENCES DATABASE SCOPED CREDENTIAL` |
| CRIAR FONTE DE DADOS EXTERNA | `ALTER ANY EXTERNAL DATA SOURCE` e `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CRIAR TABELA EXTERNA | `CREATE TABLE`, `ALTER ANY SCHEMA` `ALTER ANY EXTERNAL FILE FORMAT` e`ALTER ANY EXTERNAL DATA SOURCE` |
| SELECIONE A PARTIR DA TABELA EXTERNA | `SELECT TABLE` e `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | Para criar mesa - `CREATE TABLE` , , , e `ALTER ANY SCHEMA` `ALTER ANY DATA SOURCE` `ALTER ANY EXTERNAL FILE FORMAT` . Para ler dados: `ADMIN BULK OPERATIONS` ou `REFERENCES CREDENTIAL` por cada `SELECT TABLE` tabela/visualização/função em consulta + permissão R/W no armazenamento |

## <a name="next-steps"></a>Passos seguintes

Está agora pronto para continuar com os seguintes artigos:

- [Dados de consulta sobre o armazenamento](query-data-storage.md)

- [Consultar ficheiro CSV](query-single-csv-file.md)

- [Consultar pastas e vários ficheiros](query-folders-multiple-csv-files.md)

- [Consulta de ficheiros específicos](query-specific-files.md)

- [Consultar ficheiros de Parquet](query-parquet-files.md)

- [Tipos aninhados de consulta](query-parquet-nested-types.md)

- [Consultar ficheiros JSON](query-json-files.md)

- [Criar e usar vistas](create-use-views.md)
