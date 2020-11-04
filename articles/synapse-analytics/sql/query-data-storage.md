---
title: Armazenamento de dados de consulta com piscina SQL sem servidor (pré-visualização)
description: Este artigo descreve como consultar o armazenamento Azure usando o recurso sql sem servidor (pré-visualização) dentro do Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3fd3a94efd6e7870ae3919a011fc24f66b97c559
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310948"
---
# <a name="query-storage-files-with-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Ficheiros de armazenamento de consultas com piscina SQL sem servidor (pré-visualização) no Azure Synapse Analytics

O pool SQL sem servidor (pré-visualização) permite-lhe consultar dados no seu lago de dados. Oferece uma área de superfície de consulta T-SQL que acomoda consultas de dados semi-estruturadas e não estruturadas. Para consulta, os seguintes aspetos T-SQL são suportados:

- Área de superfície [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) completa, incluindo a maioria das [funções e operadores SQL.](overview-features.md)
- CRIAR TABELA EXTERNA COMO SELECT[(CETAS)](develop-tables-cetas.md)cria uma [tabela externa](develop-tables-external-tables.md) e, em seguida, exporta, paralelamente, os resultados de uma declaração De Select Transact-SQL para o Azure Storage.

Para obter mais informações sobre o que é vs. o que não é suportado atualmente, leia o artigo [de visão geral da piscina sql sem servidor,](on-demand-workspace-overview.md) ou os seguintes artigos:
- [Desenvolva o acesso](develop-storage-files-overview.md) ao armazenamento onde pode aprender a usar a [função Externa](develop-tables-external-tables.md) e [OPENROWSET](develop-openrowset.md) para ler dados a partir do armazenamento.
- [Controle o acesso](develop-storage-files-storage-access-control.md) ao armazenamento onde pode aprender a permitir que o Sinaapse SQL aceda ao armazenamento usando a autenticação SAS ou identidade gerida do espaço de trabalho.

## <a name="overview"></a>Descrição geral

Para suportar uma experiência suave para a consulta em vigor de dados localizados em ficheiros de armazenamento Azure, o pool SQL sem servidor utiliza a função [OPENROWSET](develop-openrowset.md) com capacidades adicionais:

- [Consultar vários ficheiros ou pastas](#query-multiple-files-or-folders)
- [Formato de ficheiro PARQUET](#query-parquet-files)
- [Consulta CSV e texto delimitado (exterminador de campo, exterminador de linha, char de fuga)](#query-csv-files)
- [Leia um subconjunto de colunas escolhido](#read-a-chosen-subset-of-columns)
- [Inferência do esquema](#schema-inference)
- [função de nome de arquivo](#filename-function)
- [função de filepa](#filepath-function)
- [Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>Arquivos DE CONSULTA PARQUET

Para consultar os dados de origem do Parquet, utilize o FORMATO = 'PARQUET'

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 as varchar(max)) as rows
```

Reveja o artigo [de ficheiros Query Parquet](query-parquet-files.md) para obter exemplos de utilização.

## <a name="query-csv-files"></a>Consultar ficheiros CSV

Para consultar os dados de origem CSV, utilize o FORMATO = 'CSV'. Pode especificar o esquema do ficheiro CSV como parte da `OPENROWSET` função quando consultar ficheiros CSV:

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 as varchar(max)) as rows
```

Existem algumas opções adicionais que podem ser usadas para ajustar as regras de análise ao formato CSv personalizado:
- ESCAPE_CHAR = 'char' Especifica o carácter do ficheiro que é usado para escapar a si próprio e todos os valores delimiter no ficheiro. Se o personagem de fuga for seguido por um valor diferente de si mesmo, ou por qualquer um dos valores delimiter, o personagem de fuga é deixado cair ao ler o valor.
O parâmetro ESCAPE_CHAR será aplicado quer o FIELDQUOTE esteja ou não ativado. Não será usado para escapar ao personagem citando. O personagem citando deve ser escapado com outro personagem citando. Citar o carácter só pode aparecer dentro do valor da coluna se o valor for encapsulado com caracteres citantes.
- FIELDTERMINATOR ='field_terminator' Especifica o exterminador de campo a utilizar. O exterminador de campo padrão é uma vírgula (" **,** ")
- ROWTERMINATOR ='row_terminator' Especifica o exterminador da linha a utilizar. O exterminador de linha predefinido é um personagem newline: **\r\n**.

## <a name="file-schema"></a>Esquema de arquivo

O idioma SQL em Synapse SQL permite-lhe definir o esquema do ficheiro como parte da `OPENROWSET` função e ler todo ou subconjunto de colunas, ou tenta determinar automaticamente tipos de colunas a partir do ficheiro usando inferência de esquema.

### <a name="read-a-chosen-subset-of-columns"></a>Leia um subconjunto de colunas escolhido

Para especificar as colunas que pretende ler, pode fornecer uma cláusula opcional com a sua `OPENROWSET` declaração.

- Se existirem ficheiros de dados CSV, para ler todas as colunas, forneça nomes de colunas e seus tipos de dados. Se pretender um subconjunto de colunas, utilize números ordinais para recolher as colunas dos ficheiros de dados originários por ordinal. As colunas ficarão vinculadas pela designação ordinal.
- Se existirem ficheiros de dados do Parquet, forneça nomes de colunas que correspondam aos nomes das colunas nos ficheiros de dados originários. As colunas ficarão ligadas pelo nome.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 as varchar(max)
) as rows
```

Para cada coluna, tem de especificar o nome da coluna e escrever a `WITH` cláusula.
Para amostras, consulte os [ficheiros de Leitura de CSV sem especificar todas as colunas](query-single-csv-file.md#return-a-subset-of-columns).

## <a name="schema-inference"></a>Inferência do esquema

Ao omitir a cláusula WITH da `OPENROWSET` declaração, pode instruir o serviço a detetar (inferir) o esquema dos ficheiros subjacentes.

> [!NOTE]
> Atualmente, isto funciona apenas para o formato de ficheiro PARQUET.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

Certifique-se de que [os tipos de dados inferidos adequados](best-practices-sql-on-demand.md#check-inferred-data-types) são utilizados para um desempenho ótimo. 

## <a name="query-multiple-files-or-folders"></a>Consultar vários ficheiros ou pastas

Para executar uma consulta T-SQL sobre um conjunto de ficheiros dentro de uma pasta ou conjunto de pastas enquanto os trata como uma única entidade ou conjunto de linha, fornecer um caminho para uma pasta ou um padrão (usando wildcards) sobre um conjunto de ficheiros ou pastas.

Aplicam-se as seguintes regras:

- Os padrões podem aparecer em parte de um caminho de diretório ou em um nome de arquivo.
- Vários padrões podem aparecer no mesmo passo de diretório ou nome de arquivo.
- Se houver vários wildcards, os ficheiros dentro de todos os caminhos correspondentes serão incluídos no conjunto de ficheiros resultante.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

Consulte [as pastas de consulta e vários ficheiros](query-folders-multiple-csv-files.md) para obter exemplos de utilização.

## <a name="file-metadata-functions"></a>Funções de metadados de ficheiros

### <a name="filename-function"></a>Função de nome de ficheiro

Esta função devolve o nome do ficheiro de que a linha é originária. 

Para consultar ficheiros específicos, leia a secção de nome de ficheiros no artigo [de ficheiros específicos](query-specific-files.md#filename) da Consulta.

O tipo de dados de devolução é nvarchar(1024). Para um desempenho ótimo, elenco sempre o resultado da função de nome de ficheiro para o tipo de dados apropriado. Se utilizar o tipo de dados de caracteres, certifique-se de que o comprimento adequado é utilizado.

### <a name="filepath-function"></a>Função de filepa

Esta função retorna um caminho completo ou uma parte do caminho:

- Quando chamado sem parâmetro, devolve o caminho completo do ficheiro de que uma linha se origina.
- Quando chamado com parâmetro, retorna parte do caminho que corresponde ao wildcard na posição especificada no parâmetro. Por exemplo, o valor do parâmetro 1 devolveria parte do caminho que corresponde ao primeiro wildcard.

Para obter informações adicionais, leia a secção Filepath do artigo [de ficheiros específicos da Consulta.](query-specific-files.md#filepath)

O tipo de dados de devolução é nvarchar(1024). Para um desempenho ótimo, elenco sempre o resultado da função de filepas para o tipo de dados apropriado. Se utilizar o tipo de dados de caracteres, certifique-se de que o comprimento adequado é utilizado.

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas

Para permitir uma experiência suave com dados armazenados em tipos de dados aninhados ou repetidos, como em ficheiros [Parquet,](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) o pool SQL sem servidor adicionou as extensões que se seguem.

#### <a name="project-nested-or-repeated-data"></a>Projeto aninhado ou repetido dados

Para projetar dados, execute uma declaração SELECT sobre o ficheiro Parquet que contém colunas de tipos de dados aninhados. Na saída, os valores aninhados serão serializados em JSON e devolvidos como um tipo de dados DE SQL varchar(8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Para obter informações mais detalhadas, consulte a secção de dados aninhada ou repetida do artigo dos [tipos aninhados query Parquet.](query-parquet-nested-types.md#project-nested-or-repeated-data)

#### <a name="access-elements-from-nested-columns"></a>Elementos de acesso a partir de colunas aninhadas

Para aceder a elementos aninhados a partir de uma coluna aninhada, como a Struct, use a "notação do ponto" para concatenar os nomes de campo no caminho. Fornecer o caminho como column_name na cláusula COM da `OPENROWSET` função.

O exemplo do fragmento de sintaxe é o seguinte:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Por predefinição, a `OPENROWSET` função corresponde ao nome e caminho do campo de origem com os nomes da coluna fornecidos na cláusula COM. Os elementos contidos em diferentes níveis de nidificação dentro da mesma fonte o ficheiro Parquet podem ser acedidos através da cláusula WITH.

**Valores de retorno**

- A função devolve um valor escalar, como int, decimal e varchar, do elemento especificado, e no caminho especificado, para todos os tipos de Parquet que não estão no grupo Do Tipo Aninhado.
- Se o caminho aponta para um elemento que é de um Tipo Aninhado, a função devolve um fragmento JSON a partir do elemento superior no caminho especificado. O fragmento JSON é do tipo varchar(8000).
- Se a propriedade não puder ser encontrada no column_name especificado, a função retorna um erro.
- Se a propriedade não puder ser encontrada no column_path especificado, dependendo do [modo Path,](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)a função retorna um erro quando em modo rígido ou nulo quando em modo laxista.

Para obter amostras de consulta, reveja os elementos de acesso da secção de colunas aninhadas no artigo [dos tipos aninhados que tanto em Query Parquet.](query-parquet-nested-types.md#read-properties-from-nested-object-columns)

#### <a name="access-elements-from-repeated-columns"></a>Elementos de acesso a colunas repetidas

Para aceder a elementos de uma coluna repetida, como um elemento de um Array ou Mapa, utilize a função [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para cada elemento escalar que você precisa projetar e fornecer:

- Coluna aninhada ou repetida, como o primeiro parâmetro
- Um [caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) que especifica o elemento ou propriedade para aceder, como um segundo parâmetro

Para aceder a elementos não escalares a partir de uma coluna repetida, utilize a função [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para cada elemento não escalar que você precisa projetar e fornecer:

- Coluna aninhada ou repetida, como o primeiro parâmetro
- Um [caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) que especifica o elemento ou propriedade para aceder, como um segundo parâmetro

Consulte o fragmento de sintaxe abaixo:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Pode encontrar amostras de consulta para aceder a elementos de colunas repetidas no artigo [dos tipos aninhados que se encontram em Query Parquet.](query-parquet-nested-types.md#access-elements-from-repeated-columns)

## <a name="query-samples"></a>Exemplos de consulta

Pode aprender mais sobre a consulta de vários tipos de dados utilizando as consultas de amostragem.

### <a name="tools"></a>Ferramentas

As ferramentas que precisa para emitir consultas:
    - Azure Synapse Studio (pré-visualização)
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>Configuração de demonstração

O seu primeiro passo é **criar uma base de dados** onde executará as consultas. Em seguida, rubricará os objetos executando o [script de configuração](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nessa base de dados. 

Este script de configuração criará as fontes de dados, credenciais de base de dados e formatos de ficheiros externos que são usados para ler dados nestas amostras.

> [!NOTE]
> As bases de dados são utilizadas apenas para visualização de metadados, não para dados reais.  Anote o nome da base de dados que usa, vai precisar mais tarde.

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>Dados de demonstração fornecidos

Os dados de demonstração contêm os seguintes conjuntos de dados:

- NYC Taxi - Yellow Taxi Trip Records - parte do conjunto de dados públicos de NYC em formato CSV e Parquet
- Conjunto de dados populacionais no formato CSV
- Amostra de arquivos Parquet com colunas aninhadas
- Livros em formato JSON

| Folder path                                                  | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Pasta-mãe para dados em formato CSV                         |
| /csv/população/<br />/csv/população-unix/<br />/csv/população unix-hdr/<br />/csv/população-unix-hdr-escape<br />/csv/população-unix-hdr-citado | Pastas com ficheiros de dados da População em diferentes formatos CSV. |
| /csv/táxi/                                                   | Pasta com ficheiros de dados públicos de NYC em formato CSV              |
| /parquet/                                                    | Pasta-mãe para dados em formato Parquet                     |
| /parquet/táxi                                                | Ficheiros de dados públicos de NYC no formato Parquet, divididos por ano e mês usando o esquema de partição Hive/Hadoop. |
| /parquet/aninhado/                                             | Amostra de arquivos Parquet com colunas aninhadas                     |
| /json/                                                       | Pasta-mãe para dados em formato JSON                        |
| /json/livros/                                                 | Ficheiros JSON com dados de livros                                   |


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como consultar diferentes tipos de ficheiros e criar e utilizar pontos de vista, consulte os seguintes artigos:

- [Consultar ficheiros CSV](query-single-csv-file.md)
- [Consultar ficheiros de Parquet](query-parquet-files.md)
- [Consultar ficheiros JSON](query-json-files.md)
- [Valores aninhados de consulta](query-parquet-nested-types.md)
- [Pastas de consulta e vários ficheiros CSV](query-folders-multiple-csv-files.md)
- [Use metadados de ficheiros em consultas](query-specific-files.md)
- [Criar e utilizar vistas](create-use-views.md)
