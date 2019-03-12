---
title: Consultas SQL para o Azure Cosmos DB
description: Saiba mais sobre a sintaxe SQL, conceitos de base de dados e consultas SQL para o Azure Cosmos DB. SQL pode utilizado como uma linguagem de consulta JSON no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mjbrown
ms.openlocfilehash: 5833ee3964958437b7834ff25f1bce7837370fb1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550588"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Exemplos de consulta SQL para o Azure Cosmos DB

O Azure Cosmos DB suporta itens de consultas com o SQL (Structured Query Language) como uma linguagem de consulta JSON em contas da API de SQL. Ao conceber a linguagem de consulta para o Azure Cosmos DB, são considerados os seguintes dois objetivos:

* Em vez de inventar uma nova linguagem de consulta, Tornamos a Azure Cosmos DB para suportar o SQL, uma das linguagens de consulta mais populares e familiares. Azure Cosmos DB SQL fornece um modelo de programação formal para consultas sobre itens JSON.  

* O Azure Cosmos DB utiliza o modelo de programação do JavaScript como a base para a linguagem de consulta. A API de SQL está enraizada no sistema de tipos do JavaScript, avaliação de expressões e invocação de função. No folheio fornece um modelo de programação natural para as projeções relacionais, a navegação hierárquica entre itens JSON, associações automáticas, consultas espaciais e invocação de funções definidas pelo utilizador (UDFs) escritos inteiramente no JavaScript, entre outros recursos.

Este artigo explica algumas consultas de SQL de exemplo ao utilizar itens JSON simples. Para saber mais sobre a sintaxe de linguagem de SQL do Azure Cosmos DB, veja [referência de sintaxe SQL](sql-api-query-reference.md) artigo.

## <a id="GettingStarted"></a>Começar a utilizar com os comandos SQL

Vamos criar dois itens JSON simples e a consulta em relação a esses dados. Considere os dois itens JSON sobre famílias, inserir esses itens JSON para um contentor e, em seguida, consultar os dados. Aqui temos um JSON simple do item para as famílias Andersen e Wakefield, os pais, filhos (e seus animais de estimação), endereço e as informações de registo. O item tem as cadeias de caracteres, números, booleanos, matrizes e propriedades aninhadas.

**Item1**

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Aqui está um segundo item com uma diferença sutil – `givenName` e `familyName` são utilizados em vez de `firstName` e `lastName`.

**Item de 2**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

Agora vamos experimentar algumas consultas em relação a esses dados para compreender alguns dos principais aspectos da linguagem de consulta SQL do Azure Cosmos DB.

**Consulta1**: Por exemplo, a seguinte consulta devolve os itens em que o campo id corresponde `AndersenFamily`. Uma vez que é um `SELECT *`, o resultado da consulta é o item JSON completo, para saber mais sobre a sintaxe, consulte [instrução SELECT](sql-api-query-reference.md#select-query):

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Resultados**

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

**Query2:** Agora, considere o caso em que temos de reformatar a saída JSON numa forma diferente. Esta consulta projeta um novo objeto JSON com dois campos selecionados, o nome e a cidade, quando cidade do endereço tem o mesmo nome, como o estado. Neste caso, corresponde a "NY, NY".

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

**Resultados**

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

**Query3**: Esta consulta devolve todos os nomes de elementos subordinados na família cujo id corresponde aos `WakefieldFamily` ordenadas por cidade de residência.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

**Resultados**

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

Seguem-se alguns aspectos da linguagem de consulta do Cosmos DB por meio de exemplos que já viu até agora:  

* Uma vez que a API de SQL funciona em valores JSON, ele lida com árvore em forma de entidades em vez de linhas e colunas. Por conseguinte, o idioma permite-lhe fazer referência a nós da árvore em qualquer profundidade arbitrária, como `Node1.Node2.Node3…..Nodem`, semelhante a SQL relacional que faça referência a referência de duas partes de `<table>.<column>`.

* A linguagem de consulta estruturadas funciona com dados sem esquemas. Por conseguinte, o sistema de tipo tem de ser vinculado dinamicamente. A mesma expressão poderia resultar em diferentes tipos nos itens diferentes. O resultado de uma consulta é um valor JSON válido, mas não é garantido que estará de um esquema fixo.  

* Azure Cosmos DB suporta apenas os itens JSON rigorosos. Isso significa que o sistema de tipos e as expressões são restritas lidar apenas com os tipos JSON. Consulte a [especificação do JSON](https://www.json.org/) para obter mais detalhes.  

* Um contentor do Cosmos DB é uma coleção de esquemas de mensagens em fila de itens JSON. As relações de entidades de dados dentro e entre itens num contentor implicitamente são capturadas por contenção e não pela chave primária e relações de chave estrangeiras. Este é um aspecto importante a pena apontar tendo em consideração as junções de intra-item, abordadas posteriormente neste artigo.

## <a id="SelectClause"></a>Cláusula SELECT

Cada consulta é composta por uma cláusula SELECT e FROM opcional e cláusulas WHERE por padrões ANSI SQL. Normalmente, para cada consulta, a origem na cláusula FROM é enumerada. Em seguida, o filtro na cláusula WHERE é aplicado na origem para obter um subconjunto de itens JSON. Por fim, a cláusula SELECT é utilizada para os valores JSON de pedidos na lista de seleção do projeto. Para saber mais sobre a sintaxe, consulte [sintaxe SELECIONE](sql-api-query-reference.md#bk_select_query).

O exemplo seguinte mostra uma consulta SELECT típica.

**Consulta**

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Resultados**

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }
    }]
```

### <a name="nested-properties"></a>Propriedades aninhadas

No exemplo a seguir, está a projetar duas propriedades aninhadas `f.address.state` e `f.address.city`.

**Consulta**

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Resultados**

```json
    [{
      "state": "WA",
      "city": "seattle"
    }]
```

Projeção também suporta expressões de JSON, conforme mostrado no exemplo a seguir:

**Consulta**

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Resultados**

```json
    [{
      "$1": {
        "state": "WA",
        "city": "seattle",
        "name": "AndersenFamily"
      }
    }]
```

Vamos examinar a função de `$1` aqui. O `SELECT` cláusula tem de criar um objeto JSON e uma vez que não for fornecida nenhuma chave, utilizamos o argumento implícita os nomes de variáveis a partir do `$1`. Por exemplo, esta consulta devolve duas variáveis de argumento implícito, o nome `$1` e `$2`.

**Consulta**

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Resultados**

```json
    [{
      "$1": {
        "state": "WA",
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>Cláusula FROM

A partir de < from_specification > cláusula é opcional, a menos que a origem é filtrada ou prevista mais tarde na consulta. Para saber mais sobre a sintaxe, consulte [desde sintaxe](sql-api-query-reference.md#bk_from_clause). Uma consulta como `SELECT * FROM Families` indica que o contêiner de famílias inteiro é a origem durante o qual a enumeração. Um identificador especial raiz pode ser utilizado para representar o contentor em vez de utilizar o nome do contentor.
A lista seguinte contém as regras que são impostas por consulta:

* O contentor pode ser um alias, como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Aqui `f` é o equivalente de `Families`. `AS` é o identificador de uma palavra-chave opcional para o alias.  

* Uma vez, um alias, não é possível vincular a origem original. Por exemplo, `SELECT Families.id FROM Families f` é sintaticamente inválido, uma vez que o identificador "Famílias" não é possível resolver mais.  

* Todas as propriedades que precisam ser mencionados devem ser totalmente qualificadas. A ausência da aderência do esquema restrito, isso é imposto para evitar qualquer vinculação ambígua. Por conseguinte, `SELECT id FROM Families f` é sintaticamente inválida desde a propriedade `id` não está vinculado.

### <a name="get-subitems-using-from-clause"></a>Obter Subitems usando a cláusula FROM

A origem também pode ser reduzida a um subconjunto mais pequeno. Por exemplo, para enumerar apenas uma subárvore de cada item, o subroot poderia, em seguida, tornar-se a origem, conforme mostrado no exemplo a seguir:

**Consulta**

```sql
    SELECT *
    FROM Families.children
```

**Resultados**

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Embora o exemplo acima usado uma matriz, como a fonte, um objeto também poderia ser utilizado como a origem, o que é o que é mostrado no exemplo a seguir: Qualquer valor JSON válido (não indefinido) que pode ser encontrado na origem é considerado para inclusão no resultado da consulta. Se não tem algumas famílias um `address.state` valor, que estão excluídos no resultado da consulta.

**Consulta**

```sql
    SELECT *
    FROM Families.address.state
```

**Resultados**

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>Cláusula WHERE

A cláusula WHERE (**`WHERE <filter_condition>`**) é opcional. Especifica as condições que os itens JSON fornecidos pela origem tem de cumprir para ser incluído como parte do resultado. Qualquer item JSON têm de avaliar as condições especificadas como "true" para ser considerado para o resultado. A cláusula WHERE é usada pela camada de índice para determinar o subconjunto mais pequeno absoluto de itens de origem que podem fazer parte do resultado. Para saber mais sobre a sintaxe, consulte [sintaxe de onde](sql-api-query-reference.md#bk_where_clause).

A seguinte consulta solicita itens que contenham uma propriedade de nome cujo valor é `AndersenFamily`. Qualquer outro item que não tem uma propriedade de nome, ou onde o valor não corresponde ao `AndersenFamily` está excluído.

**Consulta**

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Resultados**

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }
    }]
```

O exemplo anterior mostrou uma consulta simples de igualdade. A API de SQL também suporta uma variedade de expressões escalares. O uso mais comum é expressões binário e unário. Referências de propriedade do objeto JSON de origem também são as expressões válidas.

Os seguintes operadores binários são atualmente suportados e podem ser utilizados em consultas, conforme mostrado nos exemplos a seguir:  

|**Tipo de operador**  | **Valores** |
|---------|---------|
|Operações aritméticas | +,-,*,/,% |
|bit a bit    | \|, &, ^, <<>>,, >>> (shift direita do preenchimento de zero) |
|Lógica    | E, EM ALTERNATIVA, NÃO      |
|Comparação | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Cadeia     |  \|\| (concatenar) |

Vamos dar uma olhada em algumas consultas com operadores binários.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5
```

Os operadores unários +,-, ~ e não são também suportadas e pode ser utilizado dentro de consultas, conforme mostrado nos exemplos a seguir:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Além de operadores binários e de unário, também são permitidas referências de propriedade. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` devolve o item JSON que contém a propriedade `isRegistered` em que o valor da propriedade é igual para o JSON `true` valor. Quaisquer outros valores (false, nulo, indefinido, o `<number>`, `<string>`, `<object>`, `<array>`, etc.) leva para o item de origem que está a ser excluído do resultado. 

### <a name="equality-and-comparison-operators"></a>Operadores de igualdade e de comparação

A tabela seguinte mostra o resultado de comparações de igualdade na API do SQL entre quaisquer dois tipos JSON.

| **OP** | **Não definido** | **Nulo** | **valor booleano** | **Número** | **Cadeia de caracteres** | **Objeto** | **Matriz** |
|---|---|---|---|---|---|---|---|
| **Não definido** | Não definido | Não definido | Não definido | Não definido | Não definido | Não definido | Não definido |
| **Nulo** | Não definido | **OK** | Não definido | Não definido | Não definido | Não definido | Não definido |
| **valor booleano** | Não definido | Não definido | **OK** | Não definido | Não definido | Não definido | Não definido |
| **Número** | Não definido | Não definido | Não definido | **OK** | Não definido | Não definido | Não definido |
| **Cadeia de caracteres** | Não definido | Não definido | Não definido | Não definido | **OK** | Não definido | Não definido |
| **Objeto** | Não definido | Não definido | Não definido | Não definido | Não definido | **OK** | Não definido |
| **Matriz** | Não definido | Não definido | Não definido | Não definido | Não definido | Não definido | **OK** |

Para outros operadores de comparação, tais como >, > =,! =, <, e < =, as seguintes regras aplicam-se:

* Comparação entre tipos resulta em indefinido.  
* Comparação entre dois objetos ou duas matrizes resulta em indefinido.

Se o resultado da expressão escalar no filtro é indefinido, o item correspondente seria não incluído no resultado, uma vez que indefinido não são logicamente equivalentes como "true".

## <a name="between-keyword"></a>ENTRE a palavra-chave
Também pode utilizar a palavra-chave BETWEEN para expressar consultas em intervalos de valores, como em ANSI SQL. ENTRE pode ser utilizado em relação a cadeias de caracteres ou números.

Por exemplo, esta consulta devolve todos os itens de famílias em que nível o primeiro subordinado é entre 1 a 5 (ambos, inclusive).

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Ao contrário em ANSI-SQL, também pode utilizar a cláusula BETWEEN na cláusula FROM, como no exemplo a seguir.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

A principal diferença entre usar BETWEEN na API de SQL e ANSI SQL é que pode expressar consultas de intervalo em Propriedades de tipos mistos – por exemplo, poderá ter de ser um número (5) "nível" em alguns itens e as cadeias de caracteres em outros ("grade4"). Nestes casos, como no JavaScript, uma comparação entre dois resultados de diferentes tipos em "undefined" e o item será ignorada.

> [!NOTE]
> Para tempos de execução de consulta mais rápidos, lembre-se criar uma política de indexação que utiliza um tipo de índice do intervalo em relação a quaisquer propriedades/caminhos numéricos que são filtrados na cláusula BETWEEN.

### <a name="logical-and-or-and-not-operators"></a>Lógicos (AND, OR e não) operadores
Operadores lógicos operam em valores booleanos. As tabelas de verdade lógicas para esses operadores são mostradas nas tabelas seguintes.

**OU o operador**

| OU | Verdadeiro | Falso | Não definido |
| --- | --- | --- | --- |
| Verdadeiro |Verdadeiro |Verdadeiro |Verdadeiro |
| Falso |Verdadeiro |Falso |Não definido |
| Não definido |Verdadeiro |Não definido |Não definido |

**E o operador**

| E | Verdadeiro | Falso | Não definido |
| --- | --- | --- | --- |
| Verdadeiro |Verdadeiro |Falso |Não definido |
| Falso |Falso |Falso |Falso |
| Não definido |Não definido |Falso |Não definido |

**Operador NOT**

| NÃO |  |
| --- | --- |
| Verdadeiro |Falso |
| Falso |Verdadeiro |
| Não definido |Não definido |

## <a name="in-keyword"></a>Palavra-chave

A palavra-chave IN pode ser utilizado para verificar se um valor especificado corresponde a um valor numa lista. Por exemplo, esta consulta devolve todos os itens de famílias em que o id é um dos "WakefieldFamily" ou "AndersenFamily".

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Este exemplo retorna todos os itens em que o estado é qualquer um dos valores especificados.

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="ternary--and-coalesce--operators"></a>Ternária (?) e os operadores de Coalesce (?)

Os operadores ternária e Coalesce podem ser utilizados para criar expressões condicionais, semelhantes a linguagens de programação populares, como c# e JavaScript. O operador Ternário (?) pode ser útil durante a criação de novas propriedades JSON em tempo real. Por exemplo, agora pode escrever consultas para classificar os níveis de classe num formato legível humano, como para iniciantes/intermédio/avançadas, como mostrado abaixo.

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Também pode aninhar as chamadas para o operador como na consulta abaixo.

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel
    FROM Families.children[0] c
```

Como com outros operadores de consulta, se as propriedades referenciadas na expressão condicional estão em falta em qualquer item, ou se os tipos que está a ser comparados forem diferentes, em seguida, esses itens são excluídos nos resultados da consulta.

O operador de Coalesce (?) pode ser usado para verificar a presença de uma propriedade num item com eficiência. Este operador é útil quando consultando semiestruturados ou dados de tipos mistos. Por exemplo, esta consulta devolve o "lastName" se estiver presente, ou "Apelido", se não estiver presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="EscapingReservedKeywords"></a>Acessador de propriedade com aspas
Também pode acessar propriedades usando o operador de propriedade delimitados por aspas `[]`. Por exemplo, `SELECT c.grade` e `SELECT c["grade"]` são equivalentes. Essa sintaxe é útil quando precisa usar uma propriedade que contém espaços, carateres especiais, ou acontece partilhar o mesmo nome que uma palavra-chave SQL ou uma palavra reservada.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="aliasing"></a>Aliasing

Agora vamos ampliar o exemplo acima, com o aliasing explícita de valores. Tal como está a palavra-chave utilizada para aliasing. É opcional, conforme mostrado e projeta o segundo valor como `NameInfo`.

No caso de uma consulta tem duas propriedades com o mesmo nome, o aliasing deve ser utilizado para mudar o nome de uma ou ambas as propriedades para que eles são a ambigüidade removidos no resultado previsto.

**Consulta**

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Resultados**

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="scalar-expressions"></a>Expressões escalares

Além das referências de propriedade, a cláusula SELECT também oferece suporte a expressões escalares como constantes, expressões aritméticas, expressões lógicas, etc. Por exemplo, eis uma consulta de "Hello World" simples.

**Consulta**

```sql
    SELECT "Hello World"
```

**Resultados**

```json
    [{
      "$1": "Hello World"
    }]
```

Eis um exemplo mais complexo que utiliza uma expressão escalar.

**Consulta**

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

**Resultados**

```json
    [{
      "$1": 1.33333
    }]
```

No exemplo a seguir, o resultado da expressão escalar é um booleano.

**Consulta**

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

**Resultados**

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="object-and-array-creation"></a>Criação do objeto e matriz

Outro recurso importante da API de SQL é a criação de matriz/objeto. No exemplo anterior, criou um novo objeto JSON. Da mesma forma, um pode também criar matrizes conforme mostrado nos exemplos a seguir:

**Consulta**

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

**Resultados**

```json
    [
      {
        "CityState": [
          "seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

## <a id="ValueKeyword"></a>Palavra-chave de valor

O **valor** palavra-chave fornece uma maneira de retornar o valor JSON. Por exemplo, a consulta mostrada abaixo retorna o valor de escalar `"Hello World"` em vez de `{$1: "Hello World"}`.

**Consulta**

```sql
    SELECT VALUE "Hello World"
```

**Resultados**

```json
    [
      "Hello World"
    ]
```

A seguinte consulta devolve o valor JSON sem o `"address"` etiqueta nos resultados.

**Consulta**

```sql
    SELECT VALUE f.address
    FROM Families f
```

**Resultados**

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

O exemplo seguinte expande para mostrar como devolver valores primitivos de JSON (nível de folha da árvore JSON).

**Consulta**

```sql
    SELECT VALUE f.address.state
    FROM Families f
```

**Resultados**

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="-operator"></a>* Operador
O operador especial (*) é suportado para projetar o item como-é. Quando utilizado, tem de ser o único campo previsto. Embora uma consulta como `SELECT * FROM Families f` for válido, `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` não são válidos.

**Consulta**

```sql
    SELECT * 
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Resultados**

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="TopKeyword"></a>Operador TOP

A palavra-chave superior pode ser utilizada para limitar o número de valores de uma consulta. Quando a parte superior é utilizado em conjunto com a cláusula ORDER BY, o conjunto de resultados é limitado para o primeiro número de N de valores ordenados; caso contrário, devolve o primeiro número de N dos resultados numa ordem indefinida. Como melhor prática, numa instrução SELECT, utilize sempre uma cláusula ORDER BY com a cláusula TOP. TCombining essas duas cláusulas é a única forma de forma previsível indicar que linhas são afetadas pela parte superior. 

**Consulta**

```sql
    SELECT TOP 1 *
    FROM Families f
```

**Resultados**

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Parte superior, pode ser utilizado com um valor constante (conforme mostrado acima) ou com um valor de variável usando consultas parametrizadas. Para obter mais detalhes, consulte as consultas parametrizadas abaixo.

## <a id="Aggregates"></a>Funções de agregação

Também pode efetuar agregações no `SELECT` cláusula. As funções de agregação executam um cálculo num conjunto de valores e devolvem um valor único. Por exemplo, a consulta seguinte devolve a contagem de itens famílias dentro do contentor.

**Consulta**

```sql
    SELECT COUNT(1)
    FROM Families f
```

**Resultados**

```json
    [{
        "$1": 2
    }]
```

Também pode retornar o valor escalar da agregação usando o `VALUE` palavra-chave. Por exemplo, a consulta seguinte devolve a contagem de valores como um único número:

**Consulta**

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

**Resultados**

```json
    [ 2 ]
```

Também pode efetuar agregações em combinação com filtros. Por exemplo, a consulta seguinte devolve a contagem de itens com o endereço em estado de Washington.

**Consulta**

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

**Resultados**

```json
    [ 1 ]
```

A tabela seguinte mostra a lista de funções de agregação suportadas na API de SQL. `SUM` e `AVG` são executadas sobre valores numéricos, enquanto `COUNT`, `MIN`, e `MAX` pode ser efetuada através de números, cadeias de caracteres, booleanos e nulos.

| Utilização | Descrição |
|-------|-------------|
| COUNT | Devolve o número de itens na expressão. |
| SUM   | Devolve a soma de todos os valores na expressão. |
| MIN   | Devolve o valor mínimo na expressão. |
| MAX   | Devolve o valor máximo na expressão. |
| AVG   | Devolve a média dos valores existentes na expressão. |

Também podem ser efetuadas agregações nos resultados de uma iteração de matriz. Para obter mais informações, consulte [iteração de matriz em consultas](#Iteration).

> [!NOTE]
> Ao utilizar o portal do Azure Data Explorer, tenha em atenção que as consultas de agregação podem retornar os resultados agregados parcialmente através de uma página de consulta. Os SDKs produzem um valor de cumulativo único em todas as páginas.
>
> Para executar consultas de agregação com o código, é necessário o SDK de .NET 1.12.0, o SDK para .NET Core 1.1.0 ou o SDK de Java 1.9.5 ou superior.
>

## <a id="OrderByClause"></a>Cláusula ORDER BY

Como em ANSI-SQL, pode incluir uma cláusula Order By opcional ao consultar. A cláusula pode incluir um argumento ASC/DESC opcional para especificar a ordem na qual os resultados devem ser recuperados.

Por exemplo, eis uma consulta que recupera famílias na ordem do nome da cidade residente.

**Consulta**

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

**Resultados**

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

E aqui está uma consulta que recupera famílias por ordem da data de criação, o que é armazenada como um número que representa a época de tempo, ou seja, tempo decorrido desde 1 de Janeiro de 1970 em segundos.

**Consulta**

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

**Resultados**

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

## <a id="Advanced"></a>Conceitos de banco de dados avançado e consultas SQL

### <a id="Iteration"></a>Iteração

Uma construção nova foi adicionada através da **IN** palavra-chave na API de SQL para fornecer suporte para iterar através das matrizes JSON. A origem FROM fornece suporte para iteração. Vamos começar com o exemplo seguinte:

**Consulta**

```sql
    SELECT *
    FROM Families.children
```

**Resultados**

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Agora vamos examinar a outra consulta, que faz a iteração através de subordinados no contentor. Tenha em atenção a diferença da matriz de saída. Neste exemplo divide `children` e nivela os resultados numa única matriz.  

**Consulta**

```sql
    SELECT *
    FROM c IN Families.children
```

**Resultados**

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Isso pode ser ainda mais usado para filtrar em cada entrada individual da matriz, como mostrado no exemplo a seguir:

**Consulta**

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

**Resultados**

```json
    [{
      "givenName": "Lisa"
    }]
```

Também pode executar a agregação sobre o resultado da iteração de matriz. Por exemplo, a consulta seguinte conta o número de subordinados entre todas as famílias.

**Consulta**

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

**Resultados**

```json
    [
      {
        "$1": 3
      }
    ]
```

### <a id="Joins"></a>Associações

Numa base de dados relacional, a necessidade de participar em tabelas, é importante. É o resultado de lógico para a criação de esquemas normalizadas. Por outro lado, a API de SQL lida com o modelo de dados desnormalizados de itens sem esquema, que é lógico equivalente de um "associação automática".

A sintaxe que suporta o idioma é `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Em geral, esta consulta devolve um conjunto de **N**- cadeias de identificação (cadeia de identificação com **N** valores). Cada tupla tem valores produzidos por fazendo a iteração de todos os aliases de contentor através de seus respectivos conjuntos. Em outras palavras, esta consulta faz um produto cruzado completo dos conjuntos de participar na União.

Os exemplos seguintes mostram como funciona a cláusula JOIN. No exemplo a seguir, o resultado está vazio, desde o produto cruzado de cada item de origem e um conjunto vazio está vazio.

**Consulta**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

**Resultados**

```json
    [{
    }]
```

No exemplo seguinte, a associação é entre a raiz de item e o `children` subroot. É um produto cruzado entre dois objetos JSON. O fato de que os filhos é uma matriz não é eficiente na associação, uma vez que estamos lidando com uma raiz única que é a matriz de elementos subordinados. Por conseguinte, o resultado contém apenas dois resultados, uma vez que o produto cruzado de cada item com a matriz produz exatamente a apenas um item.

**Consulta**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

**Resultados**

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

O exemplo seguinte mostra uma junção mais convencional:

**Consulta**

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

**Resultados**

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

A primeira coisa a observar é que o `from_source` das **associar** cláusula é um iterador. Então, o fluxo neste caso é o seguinte:  

* Expanda cada elemento subordinado **c** na matriz.
* Aplicam-se de um produto cruzado com a raiz do item **f** com cada elemento filho **c** que foi simplificado no primeiro passo.
* Por fim, o objeto raiz do projeto **f** nome de propriedade individualmente.

O primeiro item (`AndersenFamily`) contém apenas um elemento filho, para que o conjunto de resultados contém apenas um único objeto correspondente a este item. O segundo item (`WakefieldFamily`) contém dois filhos. Então, o produto cruzado produz um objeto separado para cada filho, resultando assim em dois objetos, um para cada filho correspondente a este item. Os campos de raiz em ambos os esses itens são iguais, tal como se poderia esperar num produto cruzado.

O utilitário real da associação é tuplas de formulário do produto cruzado numa forma que caso contrário, é difícil de projeto. Além disso, como podemos ver no exemplo abaixo, pode filtrar a combinação de uma cadeia de identificação que permite que o utilizador optou por uma condição cumprida pelas tuplas, o mais geral.

**Consulta**

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

**Resultados**

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Neste exemplo é uma extensão natural do exemplo anterior e efetua uma junção dupla. Então, o produto cruzado podem ser visualizado como o seguinte pseudocódigo:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` tem um subordinado que tenha um animal de estimação. Então, o produto cruzado produz uma linha (1\*1\*1) desta família. No entanto, WakefieldFamily tem dois filhos, mas apenas um subordinado "Jesse" tem animais de estimação. Jesse tem animais de dois estimação entanto. Por conseguinte, o produto cruzado produz 1\*1\*2 = 2 linhas desta família.

No exemplo seguinte, existe um filtro adicional no `pet`, que exclui todas as cadeias de identificação em que o nome do animal de estimação não é "Sombra". Tenha em atenção que é possível criar cadeias de identificação de matrizes, filtro em qualquer um dos elementos da tupla e qualquer combinação dos elementos do projeto.

**Consulta**

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

**Resultados**

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="JavaScriptIntegration"></a>Integração de JavaScript

O Azure Cosmos DB fornece um modelo de programação para executar lógica de aplicação baseados em JavaScript diretamente nos contentores em termos de procedimentos armazenados e acionadores, esse método permite suporta:

* Capacidade de fazer operações de CRUD transacionais de elevado desempenho e consultas aos itens num contentor devido a integração profunda do tempo de execução do JavaScript diretamente dentro do motor de base de dados.
* Uma modelagem natural de fluxo de controle, variável de âmbito e a atribuição e integração de primitivos com transações de base de dados de manipulação de exceção. Para obter mais detalhes sobre o suporte do Azure Cosmos DB para a integração de JavaScript, consulte a documentação de programação do lado do servidor do JavaScript.

### <a id="UserDefinedFunctions"></a>Funções definidas pelo utilizador (UDFs)

Juntamente com os tipos já definidos neste artigo, a API de SQL fornece suporte para o funções de definidas pelo utilizador (UDF). Em particular, escalares UDFs são suportadas em que os desenvolvedores podem passar argumentos de zero ou vários e devolver um resultado único argumento novamente. Cada um desses argumentos é analisada relativamente a ser valores legais de JSON.  

A sintaxe SQL é estendida para suportar a lógica de aplicativo personalizada, o uso dessas funções definidas pelo utilizador. UDFs podem ser registados com a API de SQL e, em seguida, ser referenciadas como parte de uma consulta SQL. Na verdade, a UDFs exquisitely foram concebida para chamar a partir de consultas. Como resultado para essa opção, UDFs não tem acesso ao objeto de contexto com os outros tipos de JavaScript (procedimentos armazenados e acionadores). Uma vez que as consultas são executadas como só de leitura, eles podem executar principal ou em réplicas secundárias. Por conseguinte, UDFs foram concebidos para serem executadas em réplicas secundárias, ao contrário de outros tipos de JavaScript.

Segue-se um exemplo de como uma UDF pode ser registrada na base de dados do Cosmos DB, especificamente num contêiner de item.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

O exemplo anterior cria uma UDF cujo nome é `REGEX_MATCH`. Ela aceita dois valores de cadeia de caracteres do JSON `input` e `pattern` e verifica se as correspondências primeiro o padrão especificado na segunda usando a função de string.match() do JavaScript.

Agora podemos usar esta UDF numa consulta numa projeção. UDFs tem de ser qualificados com o prefixo de maiúsculas e minúsculas de "udf." Quando é chamada em consultas.

> [!NOTE]
> Antes de 3/17/2015, o Cosmos DB suportado UDF chamadas sem "udf." prefixo, como SELECIONAR REGEX_MATCH(). Este padrão de chamada foi preterido.  
>

**Consulta**

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

**Resultados**

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

A UDF também pode ser usada dentro de um filtro conforme mostrado no exemplo abaixo, também qualificado com "udf." prefixo:

**Consulta**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

**Resultados**

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

Em essência, UDFs são expressões escalares válidas e podem ser usados em projeções e filtros.

Para expandir o poder do UDFs, vejamos outro exemplo com lógica condicional:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Segue-se um exemplo que exercita a UDF.

**Consulta**

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

**Resultados**

```json
     [
      {
        "city": "seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Como mostrar os exemplos anteriores, UDFs integram o poder da linguagem JavaScript com a API de SQL para fornecer uma interface avançada programável fazer lógica complexa de procedimento, condicional com a ajuda de capacidades de tempo de execução de JavaScript incorporadas.

A API de SQL fornece os argumentos para a UDFs para cada item na origem no estágio atual (cláusula WHERE ou cláusula SELECT) de processamento a UDF. O resultado é incorporado no pipeline de execução geral forma totalmente integrada. Se as propriedades referidas pela UDF parâmetros não estão disponíveis no valor JSON, o parâmetro é considerado como não definido e, por conseguinte, a invocação de UDF inteiramente é ignorada. Da mesma forma se o resultado do UDF não está definido, ele não está incluído no resultado.

Em resumo, UDFs são ferramentas excelentes para fazer a lógica de negócio complexa como parte da consulta.

### <a name="operator-evaluation"></a>Avaliação de operador

O cosmos DB, pela virtue de ser uma base de dados JSON, desenha o parallels com operadores de JavaScript e sua semântica de avaliação. Enquanto tenta manter a semântica de JavaScript em termos de suporte JSON do Cosmos DB, a avaliação de operação desvia em alguns casos.

Na API do SQL, ao contrário no SQL tradicional, os tipos de valores, muitas vezes, não são conhecidos até que os valores são obtidos a partir da base de dados. Para executar consultas com eficiência, a maioria dos operadores têm requisitos de tipo rígida.

A API de SQL não efetuar conversões implícitas, ao contrário do JavaScript. Por exemplo, uma consulta como `SELECT * FROM Person p WHERE p.Age = 21` corresponde aos itens que contenham uma propriedade de idade cujo valor é de 21. Qualquer outro item cuja propriedade de idade corresponda à variações possivelmente infinita de cadeia de caracteres "21", ou outras como "021", "21.0", "0021", "00021", etc. não será possível corresponder. Por outro lado é para o JavaScript em que os valores de cadeia de caracteres são implicitamente casted para números (com base no operador, por exemplo: = =). Esta opção é crucial para o índice eficiente de correspondência na API de SQL.

## <a name="parameterized-sql-queries"></a>Consultas SQL parametrizadas

O cosmos DB suporta consultas com parâmetros expressados com o familiar \@ notação. SQL parametrizado fornece robusto manipulando e carateres de escape do usuário de entrada, impedindo a exposição acidental de dados por meio de injeção de SQL.

Por exemplo, pode escrever uma consulta que usa o apelido e estado de endereço como parâmetros e, em seguida, executá-lo para vários valores de apelido e o estado de endereço com base na entrada do usuário.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Este pedido, em seguida, pode ser enviado para o Cosmos DB como uma consulta parametrizada de JSON, como mostrado abaixo.

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

O argumento ao início pode ser definido utilizando consultas parametrizadas, como mostrado abaixo.

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Valores de parâmetro podem ser qualquer um JSON válido (cadeias de caracteres, números, booleanos, nulos, até mesmo matrizes ou aninhados JSON). Também, uma vez que o Cosmos DB é sem esquemas, parâmetros não são validados em relação a qualquer tipo.

## <a id="BuiltinFunctions"></a>Funções incorporadas

O cosmos DB também suporta um número de funções internas para operações comuns, que podem ser utilizadas dentro de consultas como as funções definidas pelo utilizador (UDFs).

| Grupo de função | Operações |
|---------|----------|
| Funções matemáticas | ABS, LIMITE, EXP, ANDAR, LOG, LOG10, ENERGIA, ROUND, INÍCIO DE SESSÃO, SQRT, QUADRADO, TRUNC, FUNÇÕES ACOS, ASIN, ATAN, ATN2, COS, COT, GRAUS, PI, RADIANOS, SIN, TAN |
| As funções de verificação de tipo | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funções de cadeia | CONCAT, CONTÉM, ENDSWITH, INDEX_OF, À ESQUERDA, COMPRIMENTO, MAIS BAIXO, LTRIM, SUBSTITUIR, REPLICAR, INVERSO, CERTO, RTRIM, STARTSWITH, SUBSTRING, SUPERIOR |
| Funções de matriz | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE |
| Funções espaciais | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Se estiver a utilizar atualmente uma função definida pelo utilizador (UDF) para o qual uma função incorporada está agora disponível, deve usar a função interna correspondente à medida que vai ser mais rápido executar e com mais eficiência.

### <a name="mathematical-functions"></a>Funções matemáticas

As funções matemáticas realizar um cálculo, com base nos valores de entrada que são fornecidos como argumentos e devolvem um valor numérico. Aqui está uma tabela de funções matemáticas incorporadas.

| Utilização | Descrição |
|----------|--------|
| [ABS (num_expr) | Devolve o valor absoluto (positivo) da expressão especificada numérico. |
| CEILING (num_expr) | Devolve o menor valor de número inteiro maior que ou igual a, a expressão numérica especificada. |
| FLOOR (num_expr) | Devolve o maior número inteiro menor ou igual a expressão numérica especificada. |
| EXP (num_expr) | Devolve o expoente da expressão especificada numérico. |
| LOG (num_expr ,base) | Devolve o logaritmo natural da expressão numérica especificada ou o logaritmo com a base especificada |
| LOG10 (num_expr) | Devolve o valor de base 10 logarítmico da expressão especificada numérico. |
| ROUND (num_expr) | Devolve um valor numérico, arredondado para o valor de número inteiro mais próximo. |
| TRUNC (num_expr) | Devolve um valor numérico, truncado para o valor de número inteiro mais próximo. |
| SQRT (num_expr) | Devolve a raiz quadrada da expressão especificada numérico. |
| SQUARE (num_expr) | Devolve o quadrado da expressão especificada numérico. |
| POWER (num_expr, num_expr) | Devolve o poder da expressão numérica especificada para o valor especificado. |
| SIGN (num_expr) | Devolve o valor de início de sessão (-1, 0, 1) da expressão especificada numérico. |
| ACOS (num_expr) | Devolve o ângulo em radianos, cujo co-seno é a expressão numérica especificada; Também chamado de arco de cosseno. |
| ASIN (num_expr) | Devolve o ângulo em radianos, cujo seno é a expressão numérica especificada. Esta função também é denominada o arco de seno. |
| ATAN (num_expr) | Devolve o ângulo em radianos, cuja tangente é a expressão numérica especificada. Isso também é chamado o arco de tangente. |
| ATN2 (num_expr) | Devolve o ângulo em radianos, entre o eixo x positivo e o ray da origem para o ponto (y, x), onde x e y são os valores de duas expressões de vírgula flutuante especificado. |
| COS (num_expr) | Devolve o cosseno trigonométricos do ângulo especificado, em radianos, a expressão especificada. |
| COT (num_expr) | Devolve a co-tangente trigonométricos do ângulo especificado, em radianos, a expressão numérica especificado. |
| DEGREES (num_expr) | Devolve o ângulo correspondente em graus para um ângulo especificado em radianos. |
| PI () | Devolve o valor da constante de PI. |
| RADIANS (num_expr) | Devolve o radianos quando uma expressão numérica, em graus, que é introduzida. |
| SIN (num_expr) | Devolve o seno trigonométricos do ângulo especificado, em radianos, a expressão especificada. |
| TAN (num_expr) | Devolve a tangente de expressão de entrada, a expressão especificada. |

Por exemplo, agora, pode executar consultas conforme mostrado no exemplo a seguir:

**Consulta**

```sql
    SELECT VALUE ABS(-4)
```

**Resultados**

```json
    [4]
```

A principal diferença entre as funções do Cosmos DB em comparação comparada ANSI SQL é que elas foram projetadas para funcionar bem com os dados de esquema de esquema e mistos. Por exemplo, se tiver um item em que a propriedade Size está em falta ou tem um valor de não numéricos como "desconhecido", em seguida, o item é ignorado, em vez de retornar um erro.

### <a name="type-checking-functions"></a>As funções de verificação de tipo

As funções de verificação de tipo permitem-lhe verificar o tipo de uma expressão dentro de consultas SQL. Funções de verificação de tipo podem ser utilizadas para determinar o tipo de propriedades em itens sem perder tempo quando é desconhecido ou de variável. Aqui está uma tabela de tipo incorporado suportado, a verificação de funções.

| **Utilização** | **Descrição** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | Devolve um valor booleano que indica se o tipo do valor é uma matriz. |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | Devolve um valor booleano que indica se o tipo do valor é um booleano. |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | Devolve um valor booleano que indica se o tipo do valor é nulo. |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | Devolve um valor booleano que indica se o tipo do valor é um número. |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | Devolve um valor booleano que indica se o tipo do valor é um objeto JSON. |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | Devolve um valor booleano que indica se o tipo do valor é uma cadeia de caracteres. |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | Devolve um valor booleano que indica se a propriedade foi atribuída um valor. |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | Devolve um valor booleano que indica se o tipo do valor é uma cadeia de caracteres, número, booleano ou null. |

O uso dessas funções, agora, pode executar consultas conforme mostrado no exemplo a seguir:

**Consulta**

```sql
    SELECT VALUE IS_NUMBER(-4)
```

**Resultados**

```json
    [true]
```

### <a name="string-functions"></a>Funções de cadeia

As seguintes funções escalares realizar uma operação num valor de entrada de cadeia de caracteres e retornam uma cadeia de caracteres, o valor numérico ou booleano. Esta é uma tabela de funções de cadeias internas:

| Utilização | Descrição |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | Devolve o número de carateres da expressão de cadeia especificada |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Devolve uma cadeia que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres. |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | Devolve a parte de uma expressão de cadeia de caracteres. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Retorna um Booleano indicando se a primeira expressão de cadeia começa com o segundo |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Retorna um Booleano indicando se a primeira expressão de cadeia termina com o segundo |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Retorna um Booleano indicando se a primeira cadeia de expressão contém o segundo. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | Devolve a posição inicial da primeira ocorrência da segunda cadeia de expressão dentro da primeira expressão de cadeia especificada ou -1 se a cadeia de caracteres não foi encontrada. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | Devolve a parte esquerda de uma cadeia com o número especificado de carateres. |
| [DIREITA (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | Devolve a parte direita de uma cadeia de caracteres com o número especificado de carateres. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | Devolve uma expressão de cadeia de caracteres depois que ele remove espaços em branco à esquerda. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | Devolve uma expressão de cadeia de caracteres depois truncar todos os espaços em branco de à direita. |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em maiúsculas em minúsculas. |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em minúsculas em maiúsculas. |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | Substitui todas as ocorrências de um valor de cadeia especificada com outro valor de cadeia de caracteres. |
| [REPLICATE (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) | Repete-se um valor de cadeia de caracteres um número de vezes especificado. |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | Devolve a ordem inversa de um valor de cadeia de caracteres. |

O uso dessas funções, agora, pode executar consultas semelhante ao seguinte. Por exemplo, pode retornar o nome de família em maiúsculas da seguinte forma:

**Consulta**

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

**Resultados**

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

Ou concatenar cadeias de caracteres, como neste exemplo:

**Consulta**

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]
```

Funções de cadeia de caracteres também podem ser utilizadas na cláusula WHERE para filtrar os resultados, como no exemplo a seguir:

**Consulta**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Funções de matriz

As seguintes funções escalares executam uma operação num valor de entrada de matriz e o valor de matriz, booleano ou numérico de retorno. Esta é uma tabela de funções incorporadas de matriz:

| Utilização | Descrição |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |Devolve o número de elementos da expressão de matriz especificada. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Devolve uma matriz que é o resultado da concatenação de dois ou mais valores de matriz. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Devolve um valor booleano que indica se a matriz contém o valor especificado. Pode especificar se a correspondência total ou parcial. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Devolve a parte de uma expressão de matriz. |

Funções de matriz podem ser utilizadas para manipular matrizes no JSON. Por exemplo, eis uma consulta que devolve todos os itens em que é um dos pais "Round Robin Wakefield". 

**Consulta**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Pode especificar um fragmento parcial para corresponder elementos dentro da matriz. A consulta seguinte localiza todos os elementos principais com o `givenName` de `Robin`.

**Consulta**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Aqui está outro exemplo que utiliza ARRAY_LENGTH para obter o número de subordinados por família.

**Consulta**

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>Funções espaciais

O cosmos DB suporta as seguintes funções internas de Open Geoespacial Consortium (OGC) para consultar dados geoespaciais. 

| Utilização | Descrição |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | Devolve a distância entre as duas expressões LineString, Polygon ou GeoJSON ponto. |
| T_WITHIN (point_expr, polygon_expr) | Devolve uma expressão booleana que indica se o primeiro objeto GeoJSON (ponto, polígono ou LineString) está dentro do objeto de GeoJSON segundo (ponto, polígono ou LineString). |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Devolve uma expressão booleana que indica se os dois objetos de GeoJSON especificados (ponto, polígono ou LineString) intersect. |
| ST_ISVALID | Devolve um valor booleano que indica se a expressão de LineString, Polygon ou GeoJSON ponto especificada é válida. |
| ST_ISVALIDDETAILED | Devolve um valor JSON que contém um valor booleano valor se a expressão de LineString, Polygon ou GeoJSON ponto especificada é válida e se for inválido, além do motivo pelo qual como um valor de cadeia de caracteres. |

Funções espaciais podem ser utilizadas para executar consultas de proximidade contra dados geográficos. Por exemplo, eis uma consulta que devolve todos os itens de famílias que estão dentro de 30 km de distância do local especificado usando a função incorporada ST_DISTANCE.

**Consulta**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Para obter mais informações sobre o suporte geoespacial no Cosmos DB, consulte [trabalhar com dados geoespaciais no Azure Cosmos DB](geospatial.md). Isso encerra funções espaciais e a sintaxe SQL para o Cosmos DB. Agora vamos dar uma olhada em como funciona e como ele interage com a sintaxe de consulta de LINQ que vimos até aqui.

## <a id="Linq"></a>LINQ to SQL API

O LINQ é um modelo de programação do .NET que expresse a computação, como consultas em fluxos de objetos. O cosmos DB fornece uma biblioteca de cliente a interface com o LINQ ao facilitar uma conversão entre objetos JSON e o .NET e um mapeamento a partir de um subconjunto de consultas do LINQ para consultas do Cosmos DB.

A figura a seguir mostra a arquitetura de oferecer suporte a consultas LINQ com o Cosmos DB.  Utilizar o cliente do Cosmos DB, os desenvolvedores podem criar uma **IQueryable** objeto consulta diretamente o fornecedor de consulta do Cosmos DB, que, em seguida, traduz-se a consulta do LINQ numa consulta do Cosmos DB. A consulta é então passada para o servidor de Cosmos DB para obter um conjunto de resultados no formato JSON. Os resultados retornados são anular a serialização para um fluxo de objetos do .NET no lado do cliente.

![Arquitetura de oferecer suporte a consultas do LINQ usando a API de SQL - sintaxe SQL, linguagem de consulta JSON, conceitos de base de dados e consultas SQL][1]

### <a name="net-and-json-mapping"></a>.NET e o mapeamento de JSON

O mapeamento entre objetos .NET e itens JSON é natural - cada campo de membro de dados é mapeado para um objeto JSON, onde o nome do campo é mapeado para a parte de "chave" do objeto e a parte de "value" é recursivamente mapeado para a parte do valor do objeto. Considere o exemplo a seguir: O objeto de família criado é mapeado para o item JSON, conforme mostrado abaixo. E vice-versa, o item JSON é mapeado para um objeto .NET.

**Classe c#**

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

**JSON**

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female",
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller",
              "givenName": "Lisa",
              "gender": "female",
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```


### <a name="linq-to-sql-translation"></a>LINQ para tradução de SQL

O provedor de consultas do Cosmos DB executa um melhor mapeamento de esforço de uma consulta do LINQ numa consulta do Cosmos DB SQL. Na descrição a seguir, partimos do princípio que o leitor tem uma familiaridade básica do LINQ.

Em primeiro lugar, para o sistema de tipo, damos suporte a todos os JSON tipos primitivos, tipos numéricos, booleano, cadeia de caracteres e null. São suportados apenas esses tipos JSON. São suportadas as seguintes expressões escalares.

* Valores de constantes – eles incluem valores constantes dos tipos de dados primitivos no momento que da consulta é avaliada.
* Expressões de índice de matriz da propriedade / – essas expressões referem-se para a propriedade de um objeto ou um elemento de matriz.
  
     família. ID;    Family.children[0].familyName;    Family.children[0].grade;    Family.children[n].grade; n é uma variável de int
* Expressões aritméticas - incluem expressões aritméticas comuns nos valores numéricos e booleanos. Para obter a lista completa, consulte a especificação de SQL.
  
     2 * family.children[0].grade;    x + y;
* Expressão de comparação de cadeia de caracteres - estes incluem a comparar um valor de cadeia de caracteres para um valor de cadeia de caracteres constante.  
  
     mother.familyName = = "Smith";    child.givenName = = s; é uma variável de cadeia de caracteres
* Matriz de objeto/expressão de criação - essas expressões retorno um objeto do tipo de valor composta ou tipo anônimo ou uma matriz desses objetos. Estes valores podem ser aninhados.
  
     Principal novo {familyName = "Smith", givenName = "João"}; novo {primeiro = 1, em segundo lugar = 2;} um tipo anônimo com dois campos              
     novo int [] {child.grade 3, 5};

### <a id="SupportedLinqOperators"></a>Lista de operadores LINQ suportados

Aqui está uma lista de operadores LINQ suportados no provedor de LINQ incluído com o SDK de .NET de SQL.

* **Selecione**: Projeções traduzir para SQL SELECT, incluindo a construção de objeto
* **Onde**: Filtros de traduzir para o SQL onde e oferecer suporte a tradução entre & &, | | e! para os operadores de SQL
* **SelectMany**: Permite que o desenrolamento de matrizes à cláusula SQL JOIN. Pode ser utilizado para expressões para filtrar os elementos de matriz de cadeia/aninhados
* **OrderBy e OrderByDescending**: Traduz-se por ordem ascendente/descendente
* **Contagem**, **soma**, **Min**, **Max**, e **média** operadores de agregação e seus equivalentes de async **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, e **AverageAsync**.
* **CompareTo**: Se traduz em comparações de intervalo. Normalmente utilizado para cadeias de caracteres, uma vez que eles não são comparáveis em .NET
* **Take**: Traduz-se na parte superior de SQL para limitar os resultados de uma consulta
* **Funções matemáticas**: Oferece suporte à conversão de. Abs, funções Acos, Asin do NET, Atan, invisível, Cos, Exp, andar, Log, Log10, Pow, Round, início de sessão, Sin, Sqrt, Tan, Truncate para as funções internas de SQL equivalentes.
* **As funções de cadeias de caracteres**: Oferece suporte à conversão de. Concat, Contains, EndsWith do NET, IndexOf, contagem, ToLower, TrimStart, Replace, inverso, TrimEnd, StartsWith, SubString, ToUpper para as funções internas de SQL equivalentes.
* **As funções de matriz**: Oferece suporte à conversão de. Do NET Concat, Contains e contagem para as funções internas de SQL equivalentes.
* **Funções Geoespaciais extensão**: Oferece suporte à conversão de métodos de stub distância, dentro, IsValid e IsValidDetailed para as funções internas de SQL equivalentes.
* **Função definida pelo utilizador função extensão**: Oferece suporte à conversão do método de stub UserDefinedFunctionProvider.Invoke para a função definida pelo utilizador correspondente.
* **Diversos**: Oferece suporte à conversão do coalesce e operadores condicionais. Pode traduzir contém a cadeia de caracteres contém, ARRAY_CONTAINS ou o IN SQL dependendo do contexto.

### <a name="sql-query-operators"></a>Operadores de consulta SQL

Aqui estão alguns exemplos que ilustram como alguns dos operadores de consulta LINQ padrão são convertidas para baixo para consultas do Cosmos DB.

#### <a name="select-operator"></a>Selecione operador

A sintaxe é `input.Select(x => f(x))`, onde `f` é uma expressão escalar.

**Expressão lambda do LINQ**

```csharp
    input.Select(family => family.parents[0].familyName);
```

**SQL** 

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
```

**Expressão lambda do LINQ**

```csharp
    input.Select(family => family.children[0].grade + c); // c is an int variable
```

**SQL**

```sql
    SELECT VALUE f.children[0].grade + c
    FROM Families f
```

**Expressão lambda do LINQ**

```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
```

**SQL** 

```sql
    SELECT VALUE {"name":f.children[0].familyName,
                  "grade": f.children[0].grade + 3 }
    FROM Families f
```


#### <a name="selectmany-operator"></a>Operador SelectMany

A sintaxe é `input.SelectMany(x => f(x))`, onde `f` é uma expressão escalar, que retorna um tipo de contentor.

**Expressão lambda do LINQ**

```csharp
    input.SelectMany(family => family.children);
```

**SQL**

```sql
    SELECT VALUE child
    FROM child IN Families.children
```

#### <a name="where-operator"></a>Onde operador

A sintaxe é `input.Where(x => f(x))`, onde `f` é uma expressão escalar, que retorna um valor booleano.

**Expressão lambda do LINQ**

```csharp
    input.Where(family=> family.parents[0].familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**Expressão lambda do LINQ**

```csharp
    input.Where(
        family => family.parents[0].familyName == "Smith" &&
        family.children[0].grade < 3);
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3
```

### <a name="composite-sql-queries"></a>Consultas SQL compostas

Os operadores acima podem ser compostos para formar a consultas mais potentes. Uma vez que o Cosmos DB suporta contentores aninhados, a composição pode ser concatenada ou aninhada.

#### <a name="concatenation"></a>Concatenação

A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Uma consulta concatenada pode começar com um opcional `SelectMany` consulta seguido por vários `Select` ou `Where` operadores.

**Expressão lambda do LINQ**

```csharp
    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**Expressão lambda do LINQ**

```csharp
    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);
```

**SQL**

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3
```

**Expressão lambda do LINQ**

```csharp
    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)
```

**Expressão lambda do LINQ**

```csharp
    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"
```

#### <a name="nesting"></a>Aninhamento

A sintaxe é `input.SelectMany(x=>x.Q())` em que as perguntas e é um `Select`, `SelectMany`, ou `Where` operador.

Numa consulta aninhada, a consulta interna é aplicada a cada elemento do contêiner externo. Um recurso importante é que a consulta interna pode se referir aos campos dos elementos no contentor externo, como associações automáticas.

**Expressão lambda do LINQ**

```csharp
    input.SelectMany(family=>
        family.parents.Select(p => p.familyName));
```

**SQL**

```sql
    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents
```

**Expressão lambda do LINQ**

```csharp
    input.SelectMany(family =>
        family.children.Where(child => child.familyName == "Jeff"));
```

**SQL**

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"
```

**Expressão lambda do LINQ**

```csharp
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));
```

**SQL**

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName
```

## <a id="ExecutingSqlQueries"></a>Executar consultas SQL

O cosmos DB expõe recursos através de uma API REST que pode ser chamada por qualquer linguagem com capacidade de efetuar pedidos HTTP/HTTPS. Além disso, o Cosmos DB oferece bibliotecas de programação para várias linguagens populares, como .NET, node. js, JavaScript e Python. A API REST e das várias bibliotecas para todos os oferecem suporte de consulta através do SQL. O SDK do .NET oferece suporte a LINQ consultar, além de SQL.

Os exemplos seguintes mostram como criar uma consulta e enviá-lo numa conta de base de dados do Cosmos DB.

### <a id="RestAPI"></a>REST API

O cosmos DB oferece um modelo de programação RESTful aberto através de HTTP. Contas de base de dados podem ser aprovisionadas com uma subscrição do Azure. O modelo de recursos do Cosmos DB é composta por um conjunto de recursos numa conta de base de dados, cada uma delas é endereçável através de um URI de lógico e estável. Um conjunto de recursos é referido como um feed neste item. Uma conta de base de dados consiste num conjunto de bases de dados, cada um contendo vários contentores, cada um dos qual no folheio conter itens, UDFs e outros tipos de recursos.

O modelo de interação básicas com estes recursos é por meio dos verbos HTTP GET, PUT, POST e DELETE com sua interpretação padrão. O verbo POST é usado para a criação de um novo recurso, para executar um procedimento armazenado ou para emitir uma consulta do Cosmos DB. As consultas são sempre operações só de leitura com sem efeitos colaterais.

Os exemplos seguintes mostram uma POSTAGEM para uma consulta de SQL API feita relativamente de um contentor que contém os dois itens de exemplo que revemos até agora. A consulta tem um filtro simple na propriedade de nome de JSON. Observe o uso do `x-ms-documentdb-isquery` e Content-Type: `application/query+json` cabeçalhos para indicar que a operação é uma consulta.

**Pedido**
```
    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

**Resultados**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

O segundo exemplo mostra uma consulta mais complexa que retorna vários resultados da junção.

**Pedido**
```
    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

**Resultados**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Se os resultados de uma consulta não podem caber dentro de uma única página de resultados, em seguida, a API de REST devolve um token de continuação por meio do `x-ms-continuation-token` cabeçalho de resposta. Os clientes podem paginar os resultados, incluindo o cabeçalho nos resultados subsequentes. O número de resultados por página também pode ser controlado através de `x-ms-max-item-count` cabeçalho número. Se a consulta especificada tem uma função de agregação, como `COUNT`, em seguida, a página de consultas pode devolver um valor parcialmente agregado sobre a página de resultados. Os clientes tem de efetuar uma agregação de segundo nível sobre esses resultados para produzir os resultados finais, por exemplo, soma sobre as contagens devolvidas nas páginas individuais para devolver a contagem total.

Para gerir a política de consistência de dados para consultas, utilize o `x-ms-consistency-level` cabeçalho, como todos os pedidos de REST API. Para obter consistência da sessão, é necessário para ecoar também a versão mais recente `x-ms-session-token` cabeçalho de Cookie no pedido de consulta. Política de indexação do contentor consultados também pode influenciar a consistência dos resultados da consulta. Com as definições de política de indexação predefinidas, para contentores o índice é sempre atual com o conteúdo do item e os resultados da consulta correspondem a consistência escolhida para dados. Se a política de indexação é Relaxada para Lazy, as consultas podem devolver resultados obsoletos. Para obter mais informações, consulte [níveis de consistência do Azure Cosmos DB][consistency-levels].

Se a política de indexação configurada no contentor não é possível suportar a consulta especificada, o servidor do Azure Cosmos DB devolve 400 "Solicitação incorreta". Esta mensagem de erro é devolvida nas consultas de intervalo com caminhos configurados para pesquisas de hash (igualdade) e para caminhos explicitamente excluídos da indexação. O `x-ms-documentdb-query-enable-scan` cabeçalho pode ser especificado para permitir que a consulta para realizar uma análise quando um índice não está disponível.

Pode obter métricas detalhadas na execução da consulta, definindo `x-ms-documentdb-populatequerymetrics` cabeçalho para `True`. Para obter mais informações, consulte [métricas de consulta SQL para o Azure Cosmos DB](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>SDK DO C# (.NET)

O SDK do .NET oferece suporte a LINQ e SQL consultar. O exemplo seguinte mostra como executar a consulta de filtro introduzida anteriormente este item.
```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Este exemplo compara as duas propriedades de igualdade dentro de cada item e utiliza as projeções de anônimas.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

O exemplo seguinte mostra as associações, expressadas por meio de LINQ SelectMany.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

O cliente .NET automaticamente itera em todas as páginas de resultados da consulta nos blocos de foreach, conforme mostrado acima. As opções de consulta apresentadas na secção de REST API também estão disponíveis no SDK do .NET com o `FeedOptions` e `FeedResponse` classes no método CreateDocumentQuery. O número de páginas pode ser controlado com a `MaxItemCount` definição.

Explicitamente também pode controlar a paginação, criando `IDocumentQueryable` utilizando o `IQueryable` objeto, em seguida, ao ler o` ResponseContinuationToken` valores e passá-los de volta como `RequestContinuationToken` no `FeedOptions`. `EnableScanInQuery` pode ser definido para ativar a análise, quando a consulta não pode ser suportada pela política de indexação configurada. Para contentores particionadas, pode usar `PartitionKey` para executar a consulta em relação a uma única partição (embora o Azure Cosmos DB pode automaticamente extrair isso do texto da consulta), e `EnableCrossPartitionQuery` para executar consultas que poderão ter de ser executado em várias partições.

Consulte a [exemplos de .NET do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet) para obter mais amostras que contêm consultas.

### <a id="JavaScriptServerSideApi"></a>API de JavaScript do lado do servidor

O cosmos DB fornece um modelo de programação para executar lógica de aplicação baseados em JavaScript diretamente nos contentores usando procedimentos armazenados e acionadores. A lógica de JavaScript registrada num nível de contêiner, em seguida, pode emitir operações de banco de dados nas operações nos itens do contentor especificado. Estas operações são compactadas no ambiente transações ACID.

O exemplo seguinte mostra como utilizar o queryDocuments na API do servidor de JavaScript para fazer consultas a partir de dentro os procedimentos armazenados e acionadores.

```javascript
    function businessLogic(name, author) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="References"></a>Referências

1. [Introdução ao Azure Cosmos DB][introduction]
2. [Especificação de Cosmos DB SQL do Azure](https://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
4. [Níveis de consistência do Azure Cosmos DB][consistency-levels]
5. ANSI SQL 2011 [https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [https://json.org/](https://json.org/)
7. Especificação de JavaScript [https://www.ecma-international.org/publications/standards/Ecma-262.htm](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [https://msdn.microsoft.com/library/bb308959.aspx](https://msdn.microsoft.com/library/bb308959.aspx) 
9. Técnicas de avaliação de consulta para grandes bancos de dados [https://dl.acm.org/citation.cfm?id=152611](https://dl.acm.org/citation.cfm?id=152611)
10. Processamento de consultas no banco de dados relacional paralela sistemas, IEEE Computer sociedade Press, 1994
11. Tan lu, Ooi, processamento de consultas no banco de dados relacional paralela sistemas, IEEE Computer sociedade Press, 1994.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: PIg Latin: Um não-para-idiomas estrangeiros para processamento de dados, SIGMOD 2008.
13. G. Graefe. A estrutura de Revezar para otimização de consultas. Dados de IEEE Eng. Bull., 18(3): 1995.

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
