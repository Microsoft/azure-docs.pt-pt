---
title: Referência linguística SQL de aceleração de consulta (pré-visualização)
titleSuffix: Azure Storage
description: Aprenda a usar a sintaxe sql de aceleração de consulta.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: 3408970bcf5e34ce9f0f0afe9e723b4877dcd694
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84193397"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>Referência linguística SQL de aceleração de consulta (pré-visualização)

A aceleração da consulta suporta uma linguagem semelhante a ANSI SQL para expressar consultas sobre conteúdo sonoro.  O dialeto SQL de aceleração de consulta é um subconjunto de ANSI SQL, com um conjunto limitado de tipos de dados suportados, operadores, etc., mas também se expande em ANSI SQL para suportar consultas sobre formatos hierárquicos de dados semi-estruturados, como JSON. 

> [!NOTE]
> A funcionalidade de aceleração de consulta está em visualização pública, e está disponível nas regiões Central e Francesa do Canadá. Para rever as limitações, consulte o artigo [questões conhecidas.](data-lake-storage-known-issues.md) Para se inscrever na pré-visualização, consulte [este formulário](https://aka.ms/adls/qa-preview-signup). 

## <a name="select-syntax"></a>Selecione Sintaxe

A única declaração SQL suportada por aceleração de consulta é a declaração SELECT. Este exemplo devolve todas as linhas para as quais a expressão retorna verdadeira.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

Para os dados formatados pelo CSV, *a tabela* deve ser `BlobStorage` .  Isto significa que a consulta será feita contra qualquer bolha especificada na chamada REST.
Para os dados formatados pelo JSON, *a tabela* é um "descritor de mesa".   Consulte a secção [descritivas](#table-descriptors) deste artigo.

No exemplo seguinte, para cada linha para a qual a *expressão* WHERE retorna verdadeiramente, esta afirmação devolverá uma nova linha que é feita a partir da avaliação de cada uma das expressões de projeção.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

O exemplo a seguir devolve um cálculo agregado (Por exemplo: o valor médio de uma determinada coluna) sobre cada uma das linhas para as quais a *expressão* retorna verdadeiramente. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

O exemplo a seguir devolve compensações adequadas para a divisão de uma bolha com formato CSV.  Consulte a secção [Sys.Split](#sys-split) deste artigo.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>Tipos de Dados

|Tipo de Dados|Descrição|
|---------|-------------------------------------------|
|INT      |64 bits de inteiro assinado.                     |
|FLUTUAR    |Ponto flutuante de 64 bits ("dupla precisão")|
|CORDA   |Cadeia unicódigo de comprimento variável.            |
|TIMETAMP|Um ponto no tempo.                           |
|BOOLEANA  |True ou false.                             |

Ao ler os valores a partir de dados formatados pelo CSV, todos os valores são lidos como cordas.  Os valores das cordas podem ser convertidos para outros tipos utilizando expressões CAST.  Os valores podem ser implicitamente lançados para outros tipos dependendo do contexto. para obter mais informações, consulte [precedência do tipo de dados (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## <a name="expressions"></a>Expressions (Expressões)

### <a name="referencing-fields"></a>Campos de referência

Para dados formatados por JSON, ou dados formatados por CSV com uma linha de cabeçalho, os campos podem ser referenciados pelo nome.  Os nomes de campo podem ser citados ou não citados. Os nomes de campo citados são incluídos em caracteres de dupla cotação ("), podem conter espaços e são sensíveis a casos.  Os nomes de campo não citados são insensíveis a casos, e podem não conter caracteres especiais.

Nos dados formatados pelo CSV, os campos também podem ser referenciados por ordinal, prefixados com um carácter sublinhado (_).  Por exemplo, o primeiro campo pode ser referenciado como _1, ou o décimo primeiro campo pode ser referenciado como _11.  Referenciar campos por ordinal é útil para dados formatados por CSV que não contenham uma linha de cabeçalho, caso em que a única maneira de referenciar um determinado campo é por ordinal.

### <a name="operators"></a>Operadores

São apoiados os seguintes operadores standard de SQL:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Se os tipos de dados à esquerda e à direita de um operador forem diferentes, então a conversão automática será efetuada de acordo com as regras aqui especificadas: [Precedência do tipo de dados (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

A aceleração da consulta A linguagem SQL suporta apenas um subconjunto muito pequeno dos tipos de dados discutidos nesse artigo.  Consulte a secção Tipos de [Dados](#data-types) deste artigo.

### <a name="casts"></a>Moldes

A aceleração de consulta A linguagem SQL suporta o operador CAST, de acordo com as regras aqui: [Conversão do tipo de dados (Motor de Base de Dados)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

A aceleração da consulta A linguagem SQL suporta apenas um pequeno subconjunto dos tipos de dados discutidos nesse artigo.  Consulte a secção Tipos de [Dados](#data-types) deste artigo.

### <a name="string-functions"></a>Funções de cadeia

A aceleração de consulta O idioma SQL suporta as seguintes funções padrão de cadeia SQL:

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

A função [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) ajuda-o a procurar um padrão. Aqui estão alguns exemplos que usam a função [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) para pesquisar a cadeia de ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i `` dados.

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

Atualmente convertemos todos os [formatos de data do IS08601 padrão](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>função DATE_ADD

A aceleração da consulta A linguagem SQL suporta o ano, mês, dia, hora, minuto, segundo para a ``DATE_ADD`` função.

Exemplos:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>função DATE_DIFF

A aceleração da consulta A linguagem SQL suporta o ano, mês, dia, hora, minuto, segundo para a ``DATE_DIFF`` função.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>Função EXTRACT

Para EXTRA que não seja a parte da data suportada para a ``DATE_ADD`` função, a cultura SQL de aceleração de consulta suporta timezone_hour e timezone_minute como parte da data.

Exemplos:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>função TO_STRING

Exemplos:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

Esta tabela descreve as cordas que pode utilizar para especificar o formato de saída da ``TO_STRING`` função.

|Cadeia de formato    |Saída                               |
|-----------------|-------------------------------------|
|yy               |Ano em formato de 2 dígitos – 1999 como '99'|
|y                |Ano em formato de 4 dígitos               |
|yyyy             |Ano em formato de 4 dígitos               |
|M                |Mês do ano - 1                    |
|MM               |Mês Acolchoado Zero – 01               |
|MMM              |Abbr. mês do Ano -JAN            |
|MMMM             |Mês completo - maio                      |
|d                |Dia do mês (1-31)                  |
|dd               |Zero acolchoado dia do mês (01-31)     |
|a                |AM ou PM                             |
|h                |Hora do dia (1-12)                   |
|hh               |Zero horas acolchoadas od dia (01-12)     |
|H                |Hora do dia (0-23)                   |
|HH               |Zero Hora Acolchoada do Dia (00-23)      |
|m                |Minuto de hora (0-59)                |
|mm               |Minuto acolchoado zero (00-59)           |
|t                |Segundo de Minutos (0-59)             |
|ss               |Zero acolchoado Segundos (00-59)          |
|S                |Fração de Segundos (0.1-0.9)        |
|SS               |Fração de Segundos (0.01-0.99)      |
|SSS              |Fração de Segundos (0.001-0.999)    |
|X                |Compensado em Horas                      |
|XX ou XXXX       |Compensado em horas e minutos (+0430)  |
|XXX ou XXXXX     |Compensado em horas e minutos (-07:00) |
|x                |Compensado em horas (7)                  |
|xx ou xxxx       |Offset em hora e minuto (+0530)    |
|Xxx ou xxxxx     |Offset em hora e minuto (+05:30)   |

#### <a name="to_timestamp-function"></a>função TO_TIMESTAMP

Apenas os formatos IS08601 são suportados.

Exemplos:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> Também pode utilizar a ``UTCNOW`` função para obter o tempo do sistema.


## <a name="aggregate-expressions"></a>Expressões Agregadas

Uma declaração SELECT pode conter uma ou mais expressões de projeção ou uma única expressão agregada.  São apoiadas as seguintes expressões agregadas:

|Expressão|Descrição|
|--|--|
|[COUNT. \*](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Devolve o número de registos que correspondem à expressão predicado.|
|[COUNT(expressão)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Devolve o número de registos para os quais a expressão não é nula.|
|[MÉDIA (expressão)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Devolve a média dos valores não nulos de expressão.|
|[MIN(expressão)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Devolve o valor mínimo não nulo de expressão.|
|[MAX (expressão)](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15)    |Devolve o valor máximo não nulo de expressão.|
|[SUM(expressão)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Devolve a soma de todos os valores não nulos de expressão.|

### <a name="missing"></a>DESAPARECIDO

O ``IS MISSING`` operador é o único não padrão que a aceleração de consulta SQL suporta.  Para os dados do JSON, se faltar um campo a partir de um registo de entrada particular, o campo de expressão ``IS MISSING`` avaliará o valor booleano verdadeiro.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>Descritores de tabela

Para os dados do CSV, o nome da tabela é sempre `BlobStorage` .  Por exemplo:

```sql
SELECT * FROM BlobStorage
```

Para os dados do JSON, estão disponíveis opções adicionais:

```sql
SELECT * FROM BlobStorage[*].path
```

Isto permite consultas sobre subconjuntos dos dados JSON.

Para consultas JSON, pode mencionar o caminho em parte da cláusula FROM. Estes caminhos ajudarão a analisar o subconjunto de dados JSON. Estes caminhos podem fazer referência aos valores de Matriz e Objeto JSON.

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

Pode interessar-se apenas pelo `warehouses` objeto JSON a partir dos dados acima. O `warehouses` objeto é um tipo de matriz JSON, por isso pode mencionar isso na cláusula FROM. A sua consulta de amostra pode ser algo assim.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

A consulta obtém todos os campos, mas seleciona apenas a latitude.

Se quiser aceder apenas ao valor do `dimensions` objeto JSON, pode utilizar consultar esse objeto na sua consulta. Por exemplo:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Isto também limita o seu acesso aos membros do `dimensions` objeto. Se quiser aceder a outros membros dos campos JSON e valores internos dos objetos JSON, então poderá utilizar uma consulta como mostrada no seguinte exemplo:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage e BlobStorage \* referem-se a todo o objeto. No entanto, se tiver um caminho na cláusula FROM, então terá de usar o BlobStorage. \*

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys.Split

Esta é uma forma especial da declaração SELECT, que está disponível apenas para dados em formatados com CSV.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Utilize esta declaração nos casos em que pretende descarregar e, em seguida, processar registos de dados CSV em lotes. Assim, pode processar registos em paralelo em vez de ter de descarregar todos os registos de uma só vez. Esta declaração não devolve registos do ficheiro CSV. Em vez disso, devolve uma coleção de tamanhos de lote. Em seguida, pode utilizar cada tamanho do lote para recuperar um lote de registos de dados. 

Utilize o parâmetro *split_size* para especificar o número de bytes que pretende que cada lote contenha. Por exemplo, se quiser processar apenas 10 MB de dados de cada vez, a sua declaração seria assim: `SELECT sys.split(10485760)FROM BlobStorage` porque 10 MB é igual a 10.485.760 bytes. Cada lote conterá o máximo de registos que puder caber nesses 10 MB. 

Na maioria dos casos, o tamanho de cada lote será ligeiramente superior ao número que especificar. Isso é porque um lote não pode conter um registo parcial. Se o último registo de um lote começar antes do fim do seu limiar, o lote será maior para que possa conter o registo completo. O tamanho do último lote será provavelmente menor do que o tamanho especificado.

>[!NOTE]
> O split_size deve ser de, pelo menos, 10 MB (10485760).

## <a name="see-also"></a>Veja também

- [Aceleração da consulta de armazenamento do lago de dados Azure (pré-visualização)](data-lake-storage-query-acceleration.md)
- [Filtrar dados utilizando a aceleração da consulta de armazenamento do lago de dados Azure (pré-visualização)](data-lake-storage-query-acceleration-how-to.md)

