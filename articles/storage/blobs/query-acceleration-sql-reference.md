---
title: Referência linguística SQL de aceleração de consulta (pré-visualização)
titleSuffix: Azure Storage
description: Aprenda a usar a sintaxe de sintaxe de aceleração da consulta.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81772122"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>Referência linguística SQL de aceleração de consulta (pré-visualização)

A aceleração da consulta suporta uma linguagem semelhante a SQL ANSI para expressar consultas sobre conteúdos blob.  O dialeto SQL de aceleração da consulta é um subconjunto de ANSI SQL, com um conjunto limitado de tipos de dados suportados, operadores, etc., mas também expande-se no ANSI SQL para suportar consultas sobre formatos de dados semi-estruturados hierárquicos, como o JSON. 

> [!NOTE]
> A funcionalidade de aceleração da consulta está em pré-visualização pública, e está disponível nas regiões centrais do Canadá e da França Central. Para rever as limitações, consulte o artigo sobre [questões conhecidas.](data-lake-storage-known-issues.md) Para se inscrever na pré-visualização, consulte [este formulário](https://aka.ms/adls/qa-preview-signup). 

## <a name="select-syntax"></a>Sintaxe SELECT

A única declaração SQL suportada pela aceleração da consulta é a declaração SELECT. Este exemplo devolve todas as linhas para as quais a expressão retorna verdadeira.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

Para os dados formados por CSV, *a tabela* deve ser `BlobStorage`.  Isto significa que a consulta irá contra qualquer bolha especificada na chamada REST.
Para os dados formados pela JSON, a *tabela* é um "descritor de mesa".   Consulte a secção de [Descritores](#table-descriptors) de Tabela deste artigo.

No exemplo seguinte, para cada linha para a qual a *expressão* WHERE retorna verdadeira, esta afirmação devolverá uma nova linha que é feita a partir da avaliação de cada uma das expressões de projeção.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

O exemplo seguinte devolve um cálculo agregado (por exemplo: o valor médio de uma determinada coluna) sobre cada uma das linhas para as quais a *expressão* retorna verdadeira. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

O exemplo seguinte devolve compensações adequadas para dividir uma bolha formada em CSV.  Consulte a secção [Sys.Split](#sys-split) deste artigo.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>Tipos de Dados

|Tipo de Dados|Descrição|
|---------|-------------------------------------------|
|INT      |64 bits de inteiro assinado.                     |
|BOIA    |64 bits ("dupla precisão") ponto de flutuação.|
|CORDA   |Fio Unicode de comprimento variável.            |
|CARIMBO TEMPORAL|Um ponto no tempo.                           |
|BOOLEAN  |True ou false.                             |

Ao ler valores a partir de dados formados em CSV, todos os valores são lidos como cordas.  Os valores das cordas podem ser convertidos para outros tipos utilizando expressões CAST.  Os valores podem ser implicitamente lançados para outros tipos dependendo do contexto. para mais informações, consulte precedência do [tipo de dados (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## <a name="expressions"></a>Expressões

### <a name="referencing-fields"></a>Campos de referenciação

Para dados formados por JSON, ou dados formados em CSV com uma linha de cabeçalho, os campos podem ser referenciados pelo nome.  Os nomes de campo podem ser citados ou não citados. Os nomes de campo citados são incluídos em caracteres de citação dupla ("), podem conter espaços e são sensíveis aos casos.  Nomes de campo não citados são insensíveis a casos, e podem não conter quaisquer caracteres especiais.

Nos dados formados em CSV, os campos também podem ser referenciados por ordinal, pré-fixado com um caráter de sublinhado (_).  Por exemplo, o primeiro campo pode ser referenciado como _1, ou o décimo primeiro campo pode ser referenciado como _11.  Os campos de referência por ordinal são úteis para dados formados em CSV que não contenham uma linha de cabeçalho, caso em que a única forma de referência de um determinado campo é por ordinal.

### <a name="operators"></a>Operadores

São apoiados os seguintes operadores SQL padrão:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Se os tipos de dados à esquerda e à direita de um operador forem diferentes, então a conversão automática será efetuada de acordo com as regras aqui especificadas: Precedência do tipo de [dados (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

A linguagem SQL de aceleração da consulta suporta apenas um subconjunto muito pequeno dos tipos de dados discutidos nesse artigo.  Consulte a secção Tipos de [Dados](#data-types) deste artigo.

### <a name="casts"></a>Moldes

A linguagem SQL de aceleração da consulta suporta o operador CAST, de acordo com as regras aqui: Conversão de tipo de [dados (Motor de Base de Dados)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

A linguagem SQL de aceleração da consulta suporta apenas um pequeno subconjunto dos tipos de dados discutidos nesse artigo.  Consulte a secção Tipos de [Dados](#data-types) deste artigo.

### <a name="string-functions"></a>Funções de cadeia

A linguagem SQL de aceleração da consulta suporta as seguintes funções padrão de cadeia SQL:

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

Aqui estão alguns exemplos:

|Função|Exemplo|Resultado|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

A função [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) ajuda-o a procurar um padrão. Aqui estão alguns exemplos [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) que usam a ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``função LIKE para pesquisar a cadeia de dados .

|Consulta|Exemplo|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>Funções de data

As seguintes funções padrão de data SQL são suportadas:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Atualmente convertemos todos os [formatos de data is08601 .](https://www.w3.org/TR/NOTE-datetime) 

#### <a name="date_add-function"></a>função DATE_ADD

A linguagem SQL de aceleração da consulta suporta ano, mês, dia, hora, minuto, segundo para a ``DATE_ADD`` função.

Exemplos:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF função

A linguagem SQL de aceleração da consulta suporta ano, mês, dia, hora, minuto, segundo para a ``DATE_DIFF`` função.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>Função EXTRACT

Para extrato sem parte ``DATE_ADD`` que não a data suportada para a função, a linguagem SQL de aceleração da consulta suporta timezone_hour e timezone_minute como parte da data.

Exemplos:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING função

Exemplos:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

Esta tabela descreve cordas que pode utilizar para ``TO_STRING`` especificar o formato de saída da função.

|Cadeia de formato    |Saída                               |
|-----------------|-------------------------------------|
|yy               |Ano em formato de 2 dígitos – 1999 como '99'|
|S                |Ano em formato de 4 dígitos               |
|yyyy             |Ano em formato de 4 dígitos               |
|M                |Mês do ano - 1                    |
|MM               |Zero mês acolchoado - 01               |
|MMM              |O Abbr. mês do ano -JAN            |
|MMMM             |Mês completo - maio                      |
|d                |Dia do mês (1-31)                  |
|dd               |Zero dia acolchoado do mês (01-31)     |
|a                |AM ou PM                             |
|h                |Hora do dia (1-12)                   |
|hh               |Dia zero acolchoado Horas od (01-12)     |
|H                |Hora do dia (0-23)                   |
|HH               |Zero Hora acolchoada do dia (00-23)      |
|m                |Minuto de hora (0-59)                |
|mm               |Zero minuto acolchoado (00-59)           |
|t                |Segundo minutos (0-59)             |
|ss               |Zero acolchoados Segundos (00-59)          |
|S                |Fração de Segundos (0.1-0.9)        |
|SS               |Fração de Segundos (0.01-0.99)      |
|SSS              |Fração de Segundos (0.001-0.999)    |
|X                |Compensado em Horas                      |
|XX ou XXXX       |Compensado em horas e minutos (+0430)  |
|XXX ou XXXXX     |Offset em horas e minutos (-07:00) |
|x                |Compensado em horas (7)                  |
|xx ou xxxx       |Compensado em hora e minuto (+0530)    |
|Xxx ou xxxxx     |Offset em hora e minuto (+05:30)   |

#### <a name="to_timestamp-function"></a>função TO_TIMESTAMP

Apenas são suportados os formatos IS08601.

Exemplos:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> Também pode utilizar ``UTCNOW`` a função para obter o tempo do sistema.


## <a name="aggregate-expressions"></a>Expressões agregadas

Uma declaração SELECT pode conter uma ou mais expressões de projeção ou uma única expressão agregada.  As seguintes expressões agregadas são suportadas:

|Expressão|Descrição|
|--|--|
|[CONDE.\*](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Devolve o número de registos que correspondem à expressão predicada.|
|[CONDE (expressão)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Devolve o número de registos para os quais a expressão não é nula.|
|[MÉDIA (expressão)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Devolve a média dos valores não nulos de expressão.|
|[MIN (expressão)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Devolve o valor mínimo de expressão não nulo.|
|[MAX (expressão)](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15)    |Devolve o valor máximo de expressão não nulo.|
|[SOMA (expressão)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Devolve a soma de todos os valores de expressão não nulos.|

### <a name="missing"></a>DESAPARECIDO

O ``IS MISSING`` operador é o único não padrão que a linguagem SQL de aceleração da consulta suporta.  Para os dados da JSON, se falta um campo ``IS MISSING`` de um determinado registo de entrada, o campo de expressão avaliará o valor booleano verdadeiro.

<a id="table-descriptors" />

## <a name="table-descriptors"></a>Descritores de mesa

Para os dados csv, `BlobStorage`o nome da tabela é sempre .  Por exemplo:

```sql
SELECT * FROM BlobStorage
```

Para os dados da JSON, estão disponíveis opções adicionais:

```sql
SELECT * FROM BlobStorage[*].path
```

Isto permite consultas sobre subconjuntos dos dados JSON.

Para consultas json, você pode mencionar o caminho em parte da cláusula FROM. Estes caminhos ajudarão a analisar o subconjunto de dados da JSON. Estes caminhos podem fazer referência aos valores jSON Array e Object.

Vamos dar um exemplo para entender isto mais detalhadamente.

Estes são os nossos dados da amostra:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Pode estar interessado apenas no `warehouses` objeto JSON a partir dos dados acima referidos. O `warehouses` objeto é um tipo de matriz JSON, por isso pode mencioná-lo na cláusula FROM. A sua consulta de amostra pode parecer algo assim.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

A consulta recebe todos os campos, mas seleciona apenas a latitude.

Se quiser aceder apenas ao valor do `dimensions` objeto JSON, pode utilizar o referir-se a esse objeto na sua consulta. Por exemplo:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Isto também limita o seu `dimensions` acesso aos membros do objeto. Se quiser aceder a outros membros dos campos JSON e valores internos dos objetos JSON, então poderá utilizar uma consulta como a mostrada no seguinte exemplo:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage e BlobStorage\*referem-se a todo o objeto. No entanto, se tiver um caminho na cláusula FROM, terá\*de usar o BlobStorage.

<a id="sys-split" />

## <a name="syssplit"></a>Sys.Split

Esta é uma forma especial da declaração SELECT, que está disponível apenas para dados formados em CSV.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Utilize esta declaração nos casos em que pretende descarregar e, em seguida, processar registos de dados do CSV em lotes. Dessa forma, pode processar registos em paralelo em vez de ter de descarregar todos os registos de uma só vez. Esta declaração não devolve os registos do ficheiro CSV. Em vez disso, devolve uma coleção de tamanhos de lote. Em seguida, pode utilizar cada tamanho do lote para recuperar um lote de registos de dados. 

Utilize o parâmetro *split_size* para especificar o número de bytes que pretende que cada lote contenha. Por exemplo, se quiser processar apenas 10 MB de dados de cada `SELECT sys.split(10485760)FROM BlobStorage` vez, é uma afirmação que seria assim: porque 10 MB é igual a 10.485.760 bytes. Cada lote conterá o máximo de registos que puder caber nesses 10 MB. 

Na maioria dos casos, o tamanho de cada lote será ligeiramente superior ao número que especifica. Isso é porque um lote não pode conter um registo parcial. Se o último registo de um lote começar antes do fim do seu limiar, o lote será maior para que possa conter o registo completo. O tamanho do último lote será provavelmente menor do que o tamanho que especifica.

>[!NOTE]
> O split_size deve ser de, pelo menos, 10 MB (10485760).

## <a name="see-also"></a>Consulte também

- [Aceleração da consulta de armazenamento do lago Azure Data (pré-visualização)](data-lake-storage-query-acceleration.md)
- [Filtrar dados utilizando a aceleração da consulta de armazenamento do lago de dados do Azure (pré-visualização)](data-lake-storage-query-acceleration-how-to.md)

