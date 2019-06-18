---
title: Consultas SQL para o Azure Cosmos DB
description: Saiba mais sobre a sintaxe SQL, conceitos de base de dados e consultas SQL para o Azure Cosmos DB. Utilize o SQL como uma linguagem de consulta JSON do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: mjbrown
ms.openlocfilehash: 943ed63aed0f64ae6cbd62c52731c6ec73ddd0bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388488"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Exemplos de consulta SQL para o Azure Cosmos DB

As contas do Azure Cosmos DB SQL API suportam itens de consultas usando a linguagem SQL (Structured Query) como uma linguagem de consulta JSON. Os objetivos de design da linguagem de consulta do Azure Cosmos DB são:

* Suporta SQL, uma das linguagens de consulta mais populares e familiares, em vez de inventar uma nova linguagem de consulta. SQL fornece um modelo de programação formal para consultas sobre itens JSON.  

* Utilize o modelo de programação do JavaScript como a base para a linguagem de consulta. Sistema de tipos, avaliação de expressões e invocação de função do JavaScript são as raízes da API do SQL. Essas raízes fornecem um modelo de programação natural para funcionalidades como projeções relacionais, navegação hierárquica entre itens JSON, associações automáticas, consultas espaciais e invocação de funções definidas pelo utilizador (UDFs) escritos inteiramente no JavaScript.

Este artigo explica algumas consultas de SQL de exemplo nos itens JSON simples. Para saber mais sobre a sintaxe de linguagem de SQL do Azure Cosmos DB, veja [referência de sintaxe SQL](sql-api-query-reference.md).

## <a id="GettingStarted"></a>Introdução às consultas SQL

Na sua conta do Cosmos DB com API SQL, criar um contentor chamado `Families`. Crie dois itens JSON simples no contentor e executar algumas consultas simples contra eles.

### <a name="create-json-items"></a>Criar itens JSON

O código a seguir cria dois itens JSON simples sobre famílias. Os itens JSON simples para as famílias Andersen e Wakefield incluem elementos principais, filhos e seus animais de estimação, endereço e informações de registo. O primeiro item tem as cadeias de caracteres, números, booleanos, matrizes e propriedades aninhadas.


```json
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
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

O segundo item utiliza `givenName` e `familyName` em vez de `firstName` e `lastName`.

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
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Consultar os itens JSON

Tente algumas consultas com os dados JSON para compreender alguns dos principais aspectos da linguagem de consulta SQL do Azure Cosmos DB.

A seguinte consulta devolve os itens em que o `id` correspondências de campo `AndersenFamily`. Uma vez que é um `SELECT *` consulta, o resultado da consulta é o item JSON completo. Para obter mais informações sobre a sintaxe SELECIONE, consulte [instrução SELECT](sql-api-query-reference.md#select-query). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados da consulta são: 

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
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

A seguinte consulta reformata a saída JSON para um formato diferente. A consulta projeta um nova JSON `Family` objeto com dois campos selecionados, `Name` e `City`, quando a cidade de endereço é o mesmo que o estado. Neste caso, corresponde a "NY, NY".

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Os resultados da consulta são:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

A seguinte consulta devolve todos os nomes de elementos subordinados na família cujo `id` corresponde ao `WakefieldFamily`ordenadas por cidade.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Os resultados são:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

Os exemplos anteriores mostram vários aspectos da linguagem de consulta do Cosmos DB:  

* Uma vez que a API de SQL funciona em valores JSON, ele lida com entidades em forma de árvore, em vez de linhas e colunas. Pode consultar os nós da árvore em qualquer profundidade arbitrária, como `Node1.Node2.Node3….Nodem`, semelhante a referência de duas partes de `<table>.<column>` em ANSI SQL.

* Como funciona a linguagem de consulta com os dados sem esquemas, o sistema de tipo deve ser vinculado dinamicamente. A mesma expressão poderia resultar em diferentes tipos nos itens diferentes. O resultado de uma consulta é um valor JSON válido, mas não é garantido para ser de um esquema fixo.  

* Azure Cosmos DB suporta apenas os itens JSON rigorosos. O sistema de tipos e as expressões são restritas lidar apenas com os tipos JSON. Para obter mais informações, consulte a [especificação do JSON](https://www.json.org/).  

* Um contentor do Cosmos DB é uma coleção de esquemas de mensagens em fila de itens JSON. As relações de dentro e entre itens de contentor são implicitamente capturadas por contenção, não pela chave primária e relações de chave estrangeiras. Esta funcionalidade é importante para as junções de intra-item, abordadas posteriormente neste artigo.

## <a id="SelectClause"></a>Cláusula SELECT

Cada consulta é composta por uma cláusula SELECT e FROM opcional e cláusulas WHERE, por padrões ANSI SQL. Normalmente, a origem na cláusula FROM é enumerada e a cláusula WHERE aplica-se um filtro na origem para obter um subconjunto de itens JSON. A cláusula SELECT, em seguida, os valores JSON de pedidos na lista de seleção de projetos. Para obter mais informações sobre a sintaxe, consulte [instrução SELECT](sql-api-query-reference.md#select-query).

A seguinte, SELECIONE consulta exemplo devolve `address` partir `Families` cujo `id` corresponde ao `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a id="EscapingReservedKeywords"></a>Acessador de propriedade com aspas
Pode acessar propriedades usando a propriedade delimitados por aspas operator []. Por exemplo, `SELECT c.grade` e `SELECT c["grade"]` são equivalentes. Essa sintaxe é útil para escapar uma propriedade que contém espaços, carateres especiais, ou que tenha o mesmo nome que uma palavra-chave SQL ou uma palavra reservada.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>Propriedades aninhadas

O exemplo a seguir duas propriedades aninhadas, de projetos `f.address.state` e `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="json-expressions"></a>Expressões de JSON

Projeção também oferece suporte a expressões de JSON, conforme mostrado no exemplo a seguir:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

No exemplo anterior, a cláusula SELECT tem de criar um objeto JSON e, como o exemplo não fornece nenhuma chave, a cláusula utiliza o nome da variável implícita argumento `$1`. A seguinte consulta devolve duas variáveis de argumento implícita: `$1` e `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="ValueKeyword"></a>Palavra-chave de valor

A palavra-chave de valor fornece uma maneira de retornar o valor JSON sozinho. Por exemplo, a consulta mostrada abaixo retorna a expressão escalar `"Hello World"` em vez de `{$1: "Hello World"}`:

```sql
    SELECT VALUE "Hello World"
```

A seguinte consulta devolve os valores JSON sem o `address` etiqueta:

```sql
    SELECT VALUE f.address
    FROM Families f
```

Os resultados são:

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

O exemplo seguinte mostra como devolver valores primitivos de JSON (nível de folha da árvore JSON):


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

Os resultados são:

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="DistinctKeyword"></a>Palavra-chave DISTINCT

A palavra-chave DISTINCT elimina duplicados na projeção da consulta.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Neste exemplo, a consulta projeta valores para cada apelido.

Os resultados são:

```json
[
    "Andersen"
]
```

Pode também projetar objetos exclusivos. Neste caso, o campo lastName não existe em um dos dois documentos, para que a consulta retorna um objeto vazio.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Os resultados são:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT também pode ser usado na projecção dentro de uma subconsulta:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Esta consulta projeta uma matriz que contém o givenName cada filho com os duplicados removidos. Essa matriz tiver o alias ChildNames e previsto na consulta externa.

Os resultados são:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="aliasing"></a>Aliasing

Pode explicitamente alias valores nas consultas. Se uma consulta tem duas propriedades com o mesmo nome, utilize o aliasing para mudar o nome de uma ou ambas as propriedades para que esteja a ambigüidade removidas no resultado previsto.

A palavra-chave usada para aliasing é opcional, conforme mostrado no exemplo a seguir ao projetar o segundo valor como `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>Cláusula FROM

Campo FROM (`FROM <from_specification>`) cláusula é opcional, a menos que a origem é filtrada ou prevista mais tarde na consulta. Para obter mais informações sobre a sintaxe, consulte [desde sintaxe](sql-api-query-reference.md#bk_from_clause). Uma consulta, como `SELECT * FROM Families` enumera sobre toda a `Families` contentor. Também pode utilizar o identificador especial raiz para o contentor em vez de utilizar o nome do contentor.

A cláusula FROM impõe as seguintes regras por consulta:

* O contentor pode ser um alias, como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Aqui `f` é o alias do `Families`. COMO é o identificador de uma palavra-chave opcional para o alias.  

* Uma vez, um alias, não é possível vincular o nome de origem original. Por exemplo, `SELECT Families.id FROM Families f` é sintaticamente inválida porque o identificador `Families` tem sido um alias e não pode ser resolvido mais.  

* Todas as propriedades referenciadas tem de ser totalmente qualificadas evitar qualquer vinculação ambígua na ausência da aderência do esquema restrito. Por exemplo, `SELECT id FROM Families f` é sintaticamente inválida porque a propriedade `id` não está vinculado.

### <a name="get-subitems-by-using-the-from-clause"></a>Obtenha subitems, utilizando a cláusula FROM

A cláusula FROM pode reduzir a origem para um subconjunto mais pequeno. Para enumerar apenas uma subárvore de cada item, o subroot pode tornar-se a origem, conforme mostrado no exemplo a seguir:

```sql
    SELECT *
    FROM Families.children
```

Os resultados são:

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

A consulta anterior utilizado uma matriz, como a origem, mas também pode utilizar um objeto como a origem. A consulta considera qualquer valor JSON válido, definido na origem para inclusão no resultado. O exemplo seguinte seria excluir `Families` que não têm um `address.state` valor.

```sql
    SELECT *
    FROM Families.address.state
```

Os resultados são:

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>Cláusula WHERE

A cláusula WHERE opcional (`WHERE <filter_condition>`) Especifica as condições que os itens JSON de origem tem de cumprir para a consulta para as incluir nos resultados. Um item JSON tem de avaliar as condições especificadas para `true` para ser considerado para o resultado. A camada de índice utiliza a cláusula WHERE para determinar o subconjunto mais pequeno de itens de origem que podem fazer parte do resultado. Para obter mais informações sobre a sintaxe, consulte [sintaxe de onde](sql-api-query-reference.md#bk_where_clause).

A seguinte consulta itens de pedidos que contêm uma `id` cujo valor é de propriedade `AndersenFamily`. Ela exclui qualquer item que não tem um `id` propriedade ou cujo valor não corresponde ao `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Escalares expressões na cláusula WHERE

O exemplo anterior mostrou uma consulta simples de igualdade. A API de SQL também suporta várias [expressões escalares](#scalar-expressions). O uso mais comum é expressões binário e unário. Referências de propriedade do objeto JSON de origem também são as expressões válidas.

Pode utilizar os seguintes operadores binários suportados:  

|**Tipo de operador**  | **Valores** |
|---------|---------|
|Operações aritméticas | +,-,*,/,% |
|bit a bit    | \|, &, ^, <<>>,, >>> (shift direita do preenchimento de zero) |
|Lógica    | E, EM ALTERNATIVA, NÃO      |
|Comparação | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Cadeia     |  \|\| (concatenar) |

As seguintes consultas de usam operadores binários:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Também pode utilizar os operadores unários +,-, ~ e não em consultas, conforme mostrado nos exemplos a seguir:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Também pode utilizar referências de propriedade em consultas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` devolve o item JSON que contém a propriedade `isRegistered` com valor igual a `true`. Qualquer outro valor, tal como `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, ou `<array>`, exclui o item do resultado. 

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

Para os operadores de comparação, como `>`, `>=`, `!=`, `<`, e `<=`, comparação em tipos de ou entre dois objetos ou matrizes produz `Undefined`.  

Se for o resultado da expressão escalar `Undefined`, o item não está incluído no resultado, porque `Undefined` não é igual a `true`.

### <a name="logical-and-or-and-not-operators"></a>Lógicos (AND, OR e não) operadores

Operadores lógicos operam em valores booleanos. As tabelas seguintes mostram as tabelas de verdade lógicas para esses operadores:

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

## <a name="between-keyword"></a>ENTRE a palavra-chave

Como em ANSI SQL, pode usar a palavra-chave BETWEEN para expressar consultas em intervalos de valores numéricos ou de cadeia de caracteres. Por exemplo, a seguinte consulta devolve todos os itens em que nível o primeiro subordinado é de 1 a 5, inclusive.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Ao contrário em ANSI SQL, também pode utilizar a cláusula BETWEEN na cláusula FROM, como no exemplo seguinte.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Na API de SQL, ao contrário da ANSI SQL, pode expressar consultas de intervalo em Propriedades de tipos mistos. Por exemplo, `grade` pode ser um número semelhante `5` em alguns itens e uma cadeia de caracteres como `grade4` em outros. Nestes casos, como em JavaScript, a comparação entre os dois tipos diferentes resulta em `Undefined`, por isso, o item é ignorado.

> [!TIP]
> Para tempos de execução de consulta mais rápidos, crie uma política de indexação que utiliza um tipo de índice do intervalo em relação a quaisquer propriedades numéricas ou caminhos que filtra a cláusula BETWEEN.

## <a name="in-keyword"></a>Palavra-chave

Utilize a palavra-chave IN para verificar se um valor especificado corresponde a um valor numa lista. Por exemplo, a seguinte consulta devolve todos os itens de famílias em que o `id` é `WakefieldFamily` ou `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

O exemplo seguinte devolve todos os itens em que o estado é qualquer um dos valores especificados:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* operador

O operador especial * projetos todo o item como está. Quando utilizado, tem de ser o único campo previsto. Uma consulta, como `SELECT * FROM Families f` for válido, mas `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` não são válidos. O [consultará primeiro neste artigo](#query-the-json-items) utilizado o * operador. 

## <a name="-and--operators"></a>? e?? Operadores

Pode utilizar o ternária (?) e Coalesce operadores (?) para criar expressões condicionais, como em linguagens como o C# e JavaScript. 

Pode utilizar o? operador de construir novas propriedades JSON em tempo real. Por exemplo, a seguinte consulta classifica os níveis de nível para o `elementary` ou `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Também pode aninhar chamadas para o? operador, tal como a seguinte consulta: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Tal como acontece com outros operadores de consulta, o? operador exclui itens, se as propriedades referenciadas estão em falta ou os tipos que está a ser comparados são diferentes.

Utilize o?? operador para procurar com eficiência uma propriedade num item ao consultar os dados semiestruturados ou tipo misto. Por exemplo, a consulta seguinte devolve `lastName` se estiver presente, ou `surname` se `lastName` não estiver presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>Operador TOP

A palavra-chave superior devolve o primeiro `N` o número de resultados da consulta numa ordem indefinido. Como melhor prática, utilize superior com a cláusula ORDER BY para limitar os resultados para o primeiro `N` o número de valores ordenadas. Combinar essas duas cláusulas é a única forma de forma previsível indicar que linhas afeta superior.

Pode usar a parte superior com um valor constante, como no exemplo seguinte, ou com um valor de variável usando consultas parametrizadas. Para obter mais informações, consulte a [consultas parametrizadas](#parameterized-queries) secção.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Os resultados são:

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
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="OrderByClause"></a>Cláusula ORDER BY

Como em ANSI SQL, pode incluir uma cláusula ORDER BY opcional em consultas. O argumento opcional do ASC ou DESC Especifica se pretende recuperar resultados em ordem ascendente ou descendente. ASC é a predefinição.

Por exemplo, eis uma consulta que obtém as famílias em ordem ascendente do nome da cidade residente:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Os resultados são:

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

A seguinte consulta obtém família `id`s por ordem de sua data de criação de item. Item `creationDate` é um número que representa o *hora "Epoch"* , ou o tempo decorrido desde 1 de Janeiro de 1970 em segundos.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Os resultados são:

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

Além disso, pode ordenar por várias propriedades. Necessita de uma consulta que ordena por várias propriedades de um [índice composto](index-policy.md#composite-indexes). Considere a seguinte consulta:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Esta consulta obtém a família `id` em ordem ascendente do nome de cidade. Se vários itens têm o mesmo nome de cidade, a consulta será ordenar pelo `creationDate` por ordem descendente.

## <a id="OffsetLimitClause"></a>Cláusula de limite de deslocamento

LIMITE de deslocamento é uma cláusula opcional para ignorar, dê algum número de valores da consulta. A contagem de deslocamento e a contagem de limite são necessários na cláusula de limite de deslocamento. Atualmente, esta cláusula é suportada para as consultas existentes apenas uma única partição, ele ainda não suportam consultas entre partições. 

Quando o limite de deslocamento é utilizado em conjunto com uma cláusula ORDER BY, o conjunto de resultados é produzido por ignorar a fazer e assumir os valores ordenados. Não se for utilizada nenhuma cláusula ORDER BY, irá resultar numa ordem determinista de valores.

Por exemplo, eis uma consulta que ignora o primeiro valor e retorna o segundo valor (por ordem do nome da cidade residente):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Eis uma consulta que ignora o primeiro valor e retorna o segundo valor (sem ordem):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```




## <a name="scalar-expressions"></a>Expressões escalares

A cláusula SELECT oferece suporte a expressões escalares, como constantes, expressões aritméticas e expressões lógicas. A consulta seguinte utiliza uma expressão escalar:


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Os resultados são:

```json
    [{
      "$1": 1.33333
    }]
```

Na seguinte consulta, o resultado da expressão escalar é um booleano:


```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Os resultados são:

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

Uma funcionalidade-chave da API de SQL é a criação de matriz e objeto. O exemplo anterior criou um novo objeto JSON, `AreFromSameCityState`. Também é possível construir matrizes, conforme mostrado no exemplo a seguir:


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Os resultados são:

```json
    [
      {
        "CityState": [
          "Seattle",
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

A seguinte consulta SQL é outro exemplo do uso de matriz dentro em subconsultas. Esta consulta obtém todos os diferentes nomes de filhos numa matriz.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```


## <a id="Iteration"></a>Iteração

A API de SQL fornece suporte para iterar através das matrizes JSON, com uma construção nova adicionada através da palavra-chave IN na origem FROM. No exemplo a seguir:

```sql
    SELECT *
    FROM Families.children
```

Os resultados são:

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

A próxima consulta faz a iteração através de `children` no `Families` contentor. A matriz de saída é diferente da consulta precedente. Neste exemplo divide `children`e nivela os resultados numa única matriz:  

```sql
    SELECT *
    FROM c IN Families.children
```

Os resultados são:

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

Pode filtrar ainda mais em cada entrada individual da matriz, como mostrado no exemplo a seguir:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Os resultados são:

```json
    [{
      "givenName": "Lisa"
    }]
```

Também pode agregar sobre o resultado de uma iteração de matriz. Por exemplo, a consulta seguinte conta o número de elementos subordinados entre todas as famílias:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Os resultados são:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>Associações

Numa base de dados relacional, as associações entre tabelas são o resultado de lógico para a criação de esquemas normalizadas. Por outro lado, a API de SQL utiliza o modelo de dados desnormalizados de itens sem esquema, que é lógico equivalente de um *associação automática*.

A linguagem suporta a sintaxe `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Esta consulta devolve um conjunto de tuplas com `N` valores. Cada tupla tem valores produzidos por fazendo a iteração de todos os aliases de contentor através de seus respectivos conjuntos. Em outras palavras, esta consulta faz um produto cruzado completo dos conjuntos de participar na União.

Os exemplos seguintes mostram como funciona a cláusula JOIN. No exemplo a seguir, o resultado está vazio, desde o produto cruzado de cada item de origem e um conjunto vazio está vazio:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

O resultado é:

```json
    [{
    }]
```

No exemplo seguinte, a associação é um produto cruzado entre dois objetos JSON, a raiz do item `id` e o `children` subroot. O fato de que `children` é uma matriz não é eficaz na associação, como ela lida com uma raiz única que é o `children` matriz. O resultado contém apenas dois resultados, uma vez que o produto cruzado de cada item com a matriz produz exatamente a apenas um item.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Os resultados são:

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

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Os resultados são:

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

A fonte FROM da cláusula JOIN é um iterador. Então, o fluxo no exemplo anterior é:  

1. Expanda cada elemento subordinado `c` na matriz.
2. Aplicam-se de um produto cruzado com a raiz do item `f` com cada elemento filho `c` que o primeiro passo simplificado.
3. Por fim, o objeto raiz do projeto `f` `id` propriedade individualmente.

O primeiro item `AndersenFamily`, contém apenas um `children` elemento, para que o conjunto de resultados contém apenas um único objeto. O segundo item `WakefieldFamily`, contém dois `children`, para que o produto cruzado produz dois objetos, um para cada `children` elemento. Os campos de raiz em ambos os esses itens são iguais, tal como se poderia esperar num produto cruzado.

O utilitário real da cláusula de JUNÇÃO é formulário cadeias de identificação do produto numa forma que caso contrário, é difícil de projeto. O exemplo abaixo filtros a combinação de uma cadeia de identificação que permite que o usuário escolha uma condição satisfeito pelas tuplas geral.

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

Os resultados são:

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

A extensão seguinte do exemplo anterior efetua uma junção dupla. Pode ver o produto cruzado como o seguinte pseudocódigo:

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

`AndersenFamily` tem um subordinado que tenha um animal de estimação, para que o produto cruzado produz uma linha (1\*1\*1) desta família. `WakefieldFamily` tem dois filhos, apenas um dos quais tem animais de estimação, mas esse filho tem dois animais de estimação. O produto cruzado para esta família produz 1\*1\*2 = 2 linhas.

No exemplo seguinte, existe um filtro adicional no `pet`, que exclui todas as cadeias de identificação em que o nome do animal de estimação não é `Shadow`. Pode criar cadeias de identificação de matrizes, filtro em qualquer um dos elementos da tupla e qualquer combinação dos elementos do projeto.

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

Os resultados são:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="UserDefinedFunctions"></a>Funções definidas pelo utilizador (UDFs)

A API de SQL fornece suporte para as funções definidas pelo utilizador (UDFs). Com UDFs escalares, pode passar argumentos de zero ou vários e devolver um resultado único argumento. A API verifica cada argumento por ser valores legais de JSON.  

A API estende a sintaxe do SQL Server para suportar a lógica de aplicativo personalizada com UDFs. Pode registar UDFs com a API de SQL e referenciá-los em consultas SQL. Na verdade, a UDFs exquisitely foram concebida para chamar a partir de consultas. Como resultado, UDFs não tem acesso ao objeto de contexto, como outros tipos de JavaScript, como procedimentos armazenados e acionadores. Consultas são só de leitura e podem ser executada em réplicas primárias ou secundárias. UDFs, ao contrário de outros tipos de JavaScript, foram concebidos para serem executadas em réplicas secundárias.

O exemplo a seguir registra uma UDF num contêiner de item na base de dados do Cosmos DB. O exemplo cria uma UDF cujo nome é `REGEX_MATCH`. Ela aceita dois valores de cadeia de caracteres do JSON, `input` e `pattern`, e verifica se as correspondências primeiro o padrão especificado na segunda através do JavaScript `string.match()` função.

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

Agora, utilize este UDF uma projeção da consulta. Tem qualificar UDFs com o prefixo de maiúsculas e minúsculas `udf.` ao chamá-los a partir de dentro de consultas.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Os resultados são:

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

Pode usar a UDF qualificada com o `udf.` prefixo dentro de um filtro, como no exemplo seguinte:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Os resultados são:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

Em essência, UDFs são expressões escalares válidas que pode usar em projeções e filtros.

Para expandir o poder do UDFs, veja outro exemplo com lógica condicional:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
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

O exemplo seguinte executa a UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Os resultados são:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Se as propriedades referidas pela UDF não estão disponíveis no valor JSON parâmetros, o parâmetro é considerado como não definida e a invocação de UDF é ignorada. Da mesma forma, se o resultado do UDF não está definido, ele não está incluído no resultado.

Como mostram exemplos anteriores, UDFs integram o poder da linguagem JavaScript com a API de SQL. UDFs fornecem uma interface avançada programável fazer lógica complexa de procedimento, condicional com a ajuda de recursos internos de tempo de execução de JavaScript. A API de SQL fornece os argumentos para a UDFs para cada item de origem na cláusula SELECT ou onde atual estágio de processamento. O resultado é diretamente incorporado no pipeline de execução geral. Em resumo, UDFs são ferramentas excelentes para fazer a lógica de negócio complexa como parte das consultas.

## <a id="Aggregates"></a>Funções de agregação

As funções de agregação executam um cálculo num conjunto de valores na cláusula SELECT e devolvem um valor único. Por exemplo, a seguinte consulta devolve a contagem de itens dentro do `Families` contentor:

```sql
    SELECT COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [{
        "$1": 2
    }]
```

Também podem retornar apenas o valor escalar da agregação utilizando a palavra-chave de valor. Por exemplo, a consulta seguinte devolve a contagem de valores como um único número:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [ 2 ]
```

Também pode combinar as agregações com filtros. Por exemplo, a consulta seguinte devolve a contagem de itens com o estado de endereço de `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Os resultados são:

```json
    [ 1 ]
```

A API de SQL suporta as seguintes funções de agregação. SOMA e média utilizar valores numéricos e COUNT, MIN e MAX trabalham em números, cadeias de caracteres, booleanos e nulos.

| Função | Descrição |
|-------|-------------|
| COUNT | Devolve o número de itens na expressão. |
| SUM   | Devolve a soma de todos os valores na expressão. |
| MIN   | Devolve o valor mínimo na expressão. |
| MAX   | Devolve o valor máximo na expressão. |
| AVG   | Devolve a média dos valores existentes na expressão. |

Também pode agregar nos resultados de uma iteração de matriz. Para obter mais informações, consulte a [iteração](#Iteration) secção.

> [!NOTE]
> No Explorador de dados do portal do Azure, as consultas de agregação podem agregar resultados parciais ao longo da página de apenas uma consulta. O SDK produz um valor de cumulativo único em todas as páginas. Para executar consultas de agregação com o código, é necessário o SDK de .NET 1.12.0, o SDK para .NET Core 1.1.0 ou o SDK de Java 1.9.5 ou superior.
>

## <a id="BuiltinFunctions"></a>Funções incorporadas

O cosmos DB também suporta um número de funções internas para operações comuns, que pode ser usado dentro de consultas como as funções definidas pelo utilizador (UDFs).

| Grupo de função | Operações |
|---------|----------|
| Funções matemáticas | ABS, LIMITE, EXP, ANDAR, LOG, LOG10, ENERGIA, ROUND, INÍCIO DE SESSÃO, SQRT, QUADRADO, TRUNC, FUNÇÕES ACOS, ASIN, ATAN, ATN2, COS, COT, GRAUS, PI, RADIANOS, SIN, TAN |
| Verificação do tipo de funções | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funções de cadeia | CONCAT, CONTÉM, ENDSWITH, INDEX_OF, À ESQUERDA, COMPRIMENTO, MAIS BAIXO, LTRIM, SUBSTITUIR, REPLICAR, INVERSO, CERTO, RTRIM, STARTSWITH, SUBSTRING, SUPERIOR |
| Funções de matriz | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE |
| Funções espaciais | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Se estiver a utilizar atualmente uma função definida pelo utilizador (UDF) para o qual uma função incorporada está agora disponível, a função interna correspondente será mais rápida executar e mais eficiente.

A principal diferença entre as funções do Cosmos DB e as funções ANSI SQL é que as funções do Cosmos DB foram concebidas para funcionar bem com os dados sem esquemas e esquema misto. Por exemplo, se uma propriedade está em falta ou tem um valor não numéricos como `unknown`, o item é ignorado em vez de retornar um erro.

### <a name="mathematical-functions"></a>Funções matemáticas

As funções matemáticas realizar um cálculo, com base nos valores de entrada que são fornecidos como argumentos e devolvem um valor numérico. Aqui está uma tabela de funções matemáticas incorporadas.

| Utilização | Descrição |
|----------|--------|
| ABS (num_expr) | Devolve o valor absoluto (positivo) da expressão especificada numérico. |
| CEILING (num_expr) | Devolve o menor valor de número inteiro maior que ou igual a, a expressão numérica especificada. |
| FLOOR (num_expr) | Devolve o maior número inteiro menor ou igual a expressão numérica especificada. |
| EXP (num_expr) | Devolve o expoente da expressão especificada numérico. |
| LOG (num_expr, base) | Devolve o logaritmo natural da expressão numérica especificada ou o logaritmo com a base especificada. |
| LOG10 (num_expr) | Devolve o valor de base 10 logarítmico da expressão especificada numérico. |
| ROUND (num_expr) | Devolve um valor numérico, arredondado para o valor de número inteiro mais próximo. |
| TRUNC (num_expr) | Devolve um valor numérico, truncado para o valor de número inteiro mais próximo. |
| SQRT (num_expr) | Devolve a raiz quadrada da expressão especificada numérico. |
| SQUARE (num_expr) | Devolve o quadrado da expressão especificada numérico. |
| POWER (num_expr, num_expr) | Devolve o poder da expressão numérica especificada para o valor especificado. |
| SIGN (num_expr) | Devolve o valor de início de sessão (-1, 0, 1) da expressão especificada numérico. |
| ACOS (num_expr) | Devolve o ângulo em radianos, cujo co-seno é a expressão numérica especificada; Também chamado de arco de cosseno. |
| ASIN (num_expr) | Devolve o ângulo em radianos, cujo seno é a expressão numérica especificada. Esta função também é denominada o arco de seno. |
| ATAN (num_expr) | Devolve o ângulo em radianos, cuja tangente é a expressão numérica especificada. Esta função também é denominada o arco de tangente. |
| ATN2 (num_expr) | Devolve o ângulo em radianos, entre o eixo x positivo e o ray da origem para o ponto (y, x), onde x e y são os valores de duas expressões de vírgula flutuante especificado. |
| COS (num_expr) | Devolve o cosseno trigonométricos do ângulo especificado, em radianos, a expressão especificada. |
| COT (num_expr) | Devolve a co-tangente trigonométricos do ângulo especificado, em radianos, a expressão numérica especificado. |
| DEGREES (num_expr) | Devolve o ângulo correspondente em graus para um ângulo especificado em radianos. |
| PI () | Devolve o valor da constante de PI. |
| RADIANS (num_expr) | Devolve o radianos quando uma expressão numérica, em graus, que é introduzida. |
| SIN (num_expr) | Devolve o seno trigonométricos do ângulo especificado, em radianos, a expressão especificada. |
| TAN (num_expr) | Devolve a tangente de expressão de entrada, a expressão especificada. |

Pode executar consultas semelhante ao seguinte exemplo:

```sql
    SELECT VALUE ABS(-4)
```

O resultado é:

```json
    [4]
```

### <a name="type-checking-functions"></a>Verificação do tipo de funções

As funções de verificação de tipo permitem-lhe verificar o tipo de uma expressão dentro de uma consulta SQL. Pode utilizar funções de verificação de tipo para determinar os tipos de propriedades em itens rapidamente, quando estiverem variável ou desconhecido. Esta é uma tabela de suportadas funções internas de verificação de tipo:

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

O uso dessas funções, pode executar consultas semelhante ao seguinte exemplo:

```sql
    SELECT VALUE IS_NUMBER(-4)
```

O resultado é:

```json
    [true]
```

### <a name="string-functions"></a>Funções de cadeia

As seguintes funções escalares realizar uma operação num valor de entrada de cadeia de caracteres e retornam um valor de cadeia, numéricos ou booleanos. Esta é uma tabela de funções de cadeias internas:

| Utilização | Descrição |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | Devolve o número de carateres da expressão de cadeia especificada. |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Devolve uma cadeia que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres. |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | Devolve a parte de uma expressão de cadeia de caracteres. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Retorna um Booleano indicando se a primeira expressão de cadeia começa com a segunda. |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Retorna um Booleano indicando se a primeira expressão de cadeia termina com a segunda. |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Retorna um Booleano indicando se a primeira cadeia de expressão contém o segundo. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | Devolve a posição inicial da primeira ocorrência da segunda cadeia de expressão dentro da primeira expressão de cadeia especificada ou -1 se não for encontrada a cadeia de caracteres. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | Devolve a parte esquerda de uma cadeia com o número especificado de carateres. |
| [DIREITA (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | Devolve a parte direita de uma cadeia de caracteres com o número especificado de carateres. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | Devolve uma expressão de cadeia de caracteres depois que ele remove espaços em branco à esquerda. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | Devolve uma expressão de cadeia de caracteres depois truncar todos os espaços em branco de à direita. |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em maiúsculas em minúsculas. |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em minúsculas em maiúsculas. |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | Substitui todas as ocorrências de um valor de cadeia especificada com outro valor de cadeia de caracteres. |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | Repete-se um valor de cadeia de caracteres um número de vezes especificado. |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | Devolve a ordem inversa de um valor de cadeia de caracteres. |

O uso dessas funções, pode executar consultas com o seguinte, que retorna a família `id` em maiúsculas:

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

Os resultados são:

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

Ou concatenar cadeias de caracteres, como neste exemplo:

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

Os resultados são:

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

Também pode utilizar as funções de cadeia de caracteres na cláusula WHERE para filtrar os resultados, como no exemplo a seguir:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

Os resultados são:

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Funções de matriz

As seguintes funções escalares realizar uma operação num valor de entrada de matriz e retornam um numérico, booleano ou valor de matriz. Esta é uma tabela de funções incorporadas de matriz:

| Utilização | Descrição |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |Devolve o número de elementos da expressão de matriz especificada. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Devolve uma matriz que é o resultado da concatenação de dois ou mais valores de matriz. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Devolve um valor booleano que indica se a matriz contém o valor especificado. Pode especificar se a correspondência total ou parcial. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Devolve a parte de uma expressão de matriz. |

Utilize funções de matriz para manipular matrizes JSON. Por exemplo, eis uma consulta que devolva todos os itens `id`s em que um do `parents` é `Robin Wakefield`: 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

O resultado é:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Pode especificar um fragmento parcial para corresponder elementos dentro da matriz. A consulta seguinte localiza todos os itens `id`que tenham `parents` com o `givenName` de `Robin`:

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

O resultado é:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Aqui está outro exemplo que utiliza ARRAY_LENGTH para obter o número de `children` por família:

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

Os resultados são:

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

O cosmos DB suporta as seguintes funções internas de Open Geoespacial Consortium (OGC) para consultar o geoespacial: 

| Utilização | Descrição |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | Retorna a distância entre os dois GeoJSON `Point`, `Polygon`, ou `LineString` expressões. |
| T_WITHIN (point_expr, polygon_expr) | Devolve uma expressão booleana que indica se o primeiro objeto GeoJSON (`Point`, `Polygon`, ou `LineString`) é dentro do objeto GeoJSON segundo (`Point`, `Polygon`, ou `LineString`). |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Devolve uma expressão booleana que indica se os dois especificado objetos GeoJSON (`Point`, `Polygon`, ou `LineString`) intersect. |
| ST_ISVALID | Devolve um valor booleano que indica se o especificado GeoJSON `Point`, `Polygon`, ou `LineString` expressão é válida. |
| ST_ISVALIDDETAILED | Devolve um valor JSON que contém um valor booleano, se o especificado GeoJSON `Point`, `Polygon`, ou `LineString` expressão é válida e se for inválido, o motivo pelo qual como um valor de cadeia de caracteres. |

Pode utilizar funções geográficos para executar consultas de proximidade contra dados geográficos. Por exemplo, eis uma consulta que devolve todos os itens de famílias que estão dentro de 30 km de distância de um local especificado usando a função incorporada ST_DISTANCE:

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

O resultado é:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Para obter mais informações sobre o suporte geoespacial no Cosmos DB, consulte [trabalhar com dados geoespaciais no Azure Cosmos DB](geospatial.md). 

## <a name="parameterized-queries"></a>Consultas parametrizadas

O cosmos DB suporta consultas com parâmetros expressados por familiar @ notação. SQL parametrizado fornece processamento robusto e carateres de escape de entrada do usuário e impede a exposição acidental de dados por meio de injeção de SQL.

Por exemplo, pode escrever uma consulta que demora `lastName` e `address.state` como parâmetros e executá-lo para vários valores de `lastName` e `address.state` com base na entrada do usuário.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Em seguida, pode enviar este pedido para o Cosmos DB como uma consulta parametrizada de JSON semelhante ao seguinte:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

O exemplo seguinte define o argumento de principal com uma consulta parametrizada: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Valores de parâmetro podem ser qualquer um JSON válido: cadeias, números, booleanos, nulos, até mesmo matrizes ou JSON de aninhados. Uma vez que o Cosmos DB não tem esquema, parâmetros não são validados em relação a qualquer tipo.

## <a id="JavaScriptIntegration"></a>Integração de JavaScript

O Azure Cosmos DB fornece um modelo de programação para a execução de lógica do aplicativo com base em JavaScript diretamente em contentores, usando procedimentos armazenados e acionadores. Oferece suporte a esse modelo:

* Operações de CRUD transacionais de elevado desempenho e consultas aos itens num contentor, devido a integração profunda do tempo de execução do JavaScript dentro do motor de base de dados.
* Uma modelagem natural de fluxo de controle, variável de âmbito e a atribuição e integração de primitivos de manipulação de exceção com transações de base de dados. 

Para obter mais informações sobre a integração de JavaScript do Azure Cosmos DB, consulte a [API JavaScript do lado do servidor](#JavaScriptServerSideApi) secção.

### <a name="operator-evaluation"></a>Avaliação de operador

O cosmos DB, por não diferenciar uma base de dados JSON, desenha parallels com operadores de JavaScript e a semântica de avaliação. O cosmos DB tenta manter a semântica de JavaScript em termos de suporte JSON, mas a avaliação de operação desvia em alguns casos.

Na API do SQL, ao contrário no SQL tradicional, os tipos de valores, muitas vezes, não são conhecidos até que a API obtém os valores da base de dados. Para executar consultas com eficiência, a maioria dos operadores têm requisitos de tipo rígida.

Ao contrário do JavaScript, a API de SQL não efetuar conversões implícitas. Por exemplo, uma consulta, como `SELECT * FROM Person p WHERE p.Age = 21` corresponde aos itens que contêm um `Age` cujo valor é de propriedade `21`. Já não corresponde a qualquer outro item cuja `Age` possivelmente infinita de variações como corresponde a propriedade `twenty-one`, `021`, ou `21.0`. Isto contrasta com JavaScript, onde os valores de cadeia de caracteres são implicitamente convertidas em números baseados no operador, por exemplo: `==`. Este comportamento da API de SQL é crucial para a correspondência de índice eficiente.

## <a id="ExecutingSqlQueries"></a>Execução da consulta SQL

Qualquer linguagem com capacidade de efetuar pedidos HTTP/HTTPS, pode chamar a API de REST do Cosmos DB. O cosmos DB também oferece bibliotecas de programação para linguagens de programação .NET, node. js, JavaScript e Python. A API REST e de bibliotecas para todos os oferecem suporte à consulta através do SQL, e o SDK do .NET também suporta [LINQ consultar](#Linq).

Os exemplos seguintes mostram como criar uma consulta e enviá-lo numa conta de base de dados do Cosmos DB.

### <a id="RestAPI"></a>REST API

O cosmos DB oferece um modelo de programação RESTful aberto através de HTTP. O modelo de recursos é composta por um conjunto de recursos numa conta de base de dados, que Aprovisiona uma subscrição do Azure. A conta de base de dados consiste num conjunto de *bases de dados*, cada um dos quais pode conter vários *contentores*, que por sua vez de conter *itens*, UDFs e outros tipos de recursos. Cada recurso do Cosmos DB é endereçável através de um URI de lógico e estável. Um conjunto de recursos é chamado um *feed*. 

O modelo de interação básicas com estes recursos é por meio de verbos HTTP `GET`, `PUT`, `POST`, e `DELETE`, com seus interpretações padrão. Utilize `POST` para criar um novo recurso, executar um procedimento armazenado ou emitir uma consulta do Cosmos DB. As consultas são sempre operações só de leitura com sem efeitos colaterais.

Os exemplos seguintes mostram uma `POST` para uma consulta de API de SQL em relação as itens de exemplo. A consulta tem um filtro simple no JSON `name` propriedade. O `x-ms-documentdb-isquery` e Content-Type: `application/query+json` cabeçalhos indicam que a operação é uma consulta. Substitua `mysqlapicosmosdb.documents.azure.com:443` com o URI para a sua conta do Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
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

Os resultados são:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

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
                "city":"Seattle"
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

A consulta seguinte, mais complexa devolve vários resultados de uma associação:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
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

Os resultados são: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

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

Se os resultados de uma consulta não é possível se encaixam numa única página, a API de REST devolve um token de continuação por meio do `x-ms-continuation-token` cabeçalho de resposta. Os clientes podem paginar os resultados, incluindo o cabeçalho nos resultados da subsequentes. Também pode controlar o número de resultados por página usando o `x-ms-max-item-count` cabeçalho número. 

Se uma consulta tem uma função de agregação, como a CONTAGEM, a página de consulta pode retornar um valor parcialmente agregado ao longo de apenas uma página de resultados. Os clientes tem de efetuar uma agregação de segundo nível sobre esses resultados para produzir os resultados finais. Por exemplo, soma sobre as contagens devolvidas nas páginas individuais para devolver a contagem total.

Para gerir a política de consistência de dados para consultas, utilize o `x-ms-consistency-level` cabeçalho como em todos os pedidos de REST API. Consistência da sessão também requer que os ecoar a versão mais recente `x-ms-session-token` cabeçalho de cookie no pedido de consulta. Política de indexação do contentor consultados também pode influenciar a consistência dos resultados da consulta. Com as definições de política para contentores de indexação predefinidas, o índice é sempre atual com o conteúdo do item e os resultados da consulta correspondem a consistência escolhida para dados. Para obter mais informações, consulte [níveis de consistência do Azure Cosmos DB][consistency-levels].

Se a política de indexação configurada no contentor não é possível suportar a consulta especificada, o servidor do Azure Cosmos DB devolve 400 "Solicitação incorreta". Esta mensagem de erro, devolve para consultas, com caminhos explicitamente excluídos da indexação. Pode especificar o `x-ms-documentdb-query-enable-scan` cabeçalho para permitir que a consulta para realizar uma análise quando um índice não está disponível.

Pode obter métricas detalhadas na execução da consulta, definindo a `x-ms-documentdb-populatequerymetrics` cabeçalho para `true`. Para obter mais informações, consulte [métricas de consulta SQL para o Azure Cosmos DB](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>C#(SDK) DO .NET

O SDK do .NET oferece suporte a LINQ e SQL consultar. O exemplo seguinte mostra como executar a consulta de filtro anterior com .NET:

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

O exemplo a seguir compara as duas propriedades de igualdade dentro de cada item e utiliza projeções anônimas.

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

O exemplo seguinte mostra as associações, expressadas por meio de LINQ `SelectMany`.

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

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

O cliente .NET automaticamente itera em todas as páginas de resultados da consulta no `foreach` bloquear, conforme mostrado no exemplo anterior. As opções de consulta introduzido no [REST API](#RestAPI) secção também estão disponíveis no SDK do .NET, utilizando o `FeedOptions` e `FeedResponse` classes no `CreateDocumentQuery` método. Pode controlar o número de páginas utilizando o `MaxItemCount` definição.

Explicitamente também pode controlar a paginação, criando `IDocumentQueryable` utilizando o `IQueryable` objeto, em seguida, ao ler o `ResponseContinuationToken` valores e passá-los de volta como `RequestContinuationToken` no `FeedOptions`. Pode definir `EnableScanInQuery` para ativar a análise, quando a consulta não é suportada pela política de indexação configurada. Para contentores particionadas, pode usar `PartitionKey` para executar a consulta em relação a uma única partição, embora o Azure Cosmos DB pode automaticamente extrair isso o texto da consulta. Pode usar `EnableCrossPartitionQuery` para executar consultas em várias partições.

Para obter mais amostras de .NET com consultas, consulte a [exemplos de .NET do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet) no GitHub.

### <a id="JavaScriptServerSideApi"></a>API de JavaScript do lado do servidor

O cosmos DB fornece um modelo de programação para executar lógica de aplicação baseados em JavaScript diretamente em contentores, usando procedimentos armazenados e acionadores. A lógica de JavaScript registrada ao nível do contentor, em seguida, pode emitir operações de banco de dados nos itens do contentor determinado, encapsulado em ambiente transações ACID.

O exemplo seguinte mostra como utilizar `queryDocuments` na API para fazer consultas a partir do servidor de JavaScript dentro os procedimentos armazenados e acionadores:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>LINQ to SQL API

O LINQ é um modelo de programação do .NET que expresse a computação, como consultas em fluxos de objeto. O cosmos DB fornece uma biblioteca de cliente a interface com o LINQ ao facilitar uma conversão entre objetos JSON e o .NET e um mapeamento a partir de um subconjunto de consultas do LINQ para consultas do Cosmos DB.

O diagrama seguinte mostra a arquitetura de oferecer suporte a consultas LINQ com o Cosmos DB. Utilizar o cliente do Cosmos DB, pode criar um `IQueryable` objeto que diretamente consulta o provedor de consultas do Cosmos DB e traduz-se a consulta do LINQ numa consulta do Cosmos DB. Então, passa a consulta para o servidor de Cosmos DB, que obtém um conjunto de resultados no formato JSON. O desserializador JSON converte os resultados num fluxo de objetos do .NET no lado do cliente.

![Arquitetura de oferecer suporte a consultas do LINQ usando a API de SQL - sintaxe SQL, linguagem de consulta JSON, conceitos de base de dados e consultas SQL][1]

### <a name="net-and-json-mapping"></a>.NET e o mapeamento de JSON

O mapeamento entre objetos .NET e itens JSON é natural. Cada campo de membro de dados é mapeado para um objeto JSON, onde o nome do campo é mapeado para o *chave* parte do objeto e o valor recursivamente mapeia para o *valor* parte do objeto. O seguinte código mapas a `Family` classe a um item JSON e, em seguida, cria um `Family` objeto:

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

O exemplo anterior cria o seguinte item JSON:

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

O provedor de consultas do Cosmos DB executa um melhor mapeamento de esforço de uma consulta do LINQ numa consulta do Cosmos DB SQL. A seguinte descrição pressupõe uma familiaridade básica com o LINQ.

O sistema de tipo de fornecedor de consulta suporta apenas os tipos primitivos de JSON: numéricos, booleano, cadeia de caracteres e null. 

O provedor de consultas suporta as seguintes expressões escalares:

- Valores de constantes, incluindo valores de constantes dos tipos de dados primitivos no momento da avaliação de consulta.
  
- Expressões de índice de matriz/propriedade que se referem à propriedade de um objeto ou um elemento de matriz. Por exemplo:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Expressões aritméticas, incluindo expressões aritméticas comuns nos valores numéricos e booleanos. Para obter a lista completa, consulte a [especificação do Azure Cosmos DB SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Expressões comparação de cadeia de caracteres, que incluem a comparar um valor de cadeia de caracteres para um valor de cadeia de caracteres constante.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Matriz de objeto/criação expressões que devolvem um objeto do tipo de valor composta ou tipo anônimo, ou uma matriz desses objetos. Pode aninhar estes valores.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>Operadores LINQ suportados

O provedor LINQ incluído com o SDK de .NET de SQL suporta os seguintes operadores:

- **Selecione**: Projeções traduzir para SELECIONAR de SQL, incluindo a construção do objeto.
- **Onde**: Filtros de traduzir para onde SQL e oferecer suporte a tradução entre `&&`, `||`, e `!` para os operadores de SQL
- **SelectMany**: Permite que o desenrolamento de matrizes à cláusula SQL JOIN. Utilize para encadear ou aninhar expressões para filtrar os elementos de matriz.
- **OrderBy** e **OrderByDescending**: Traduza para ordenar por com ASC ou DESC.
- **Contagem**, **soma**, **Min**, **Max**, e **média** operadores de agregação e seus equivalentes de async **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, e **AverageAsync**.
- **CompareTo**: Se traduz em comparações de intervalo. Geralmente, utilizada para cadeias de caracteres, uma vez que eles não são comparáveis em .NET.
- **Take**: Traduz-se ao início do SQL para limitar os resultados de uma consulta.
- **Funções matemáticas**: Oferece suporte à conversão do .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, e `Truncate` para as funções internas de SQL equivalentes.
- **As funções de cadeias de caracteres**: Oferece suporte à conversão do .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, e `TrimStart` para as funções internas de SQL equivalentes.
- **As funções de matriz**: Oferece suporte à conversão do .NET `Concat`, `Contains`, e `Count` para as funções internas de SQL equivalentes.
- **As funções de extensão de dados Geoespaciais**: Oferece suporte à conversão de métodos de stub `Distance`, `IsValid`, `IsValidDetailed`, e `Within` para as funções internas de SQL equivalentes.
- **Função de extensão de função definida pelo utilizador**: Oferece suporte à conversão do método de stub `UserDefinedFunctionProvider.Invoke` para a função definida pelo utilizador correspondente.
- **Diversos**: Oferece suporte à conversão de `Coalesce` e operadores condicionais. Pode traduzir `Contains` contém a cadeia de caracteres, ARRAY_CONTAINS ou em SQL, dependendo do contexto.

### <a name="sql-query-operators"></a>Operadores de consulta SQL

Os exemplos seguintes mostram como traduzir alguns dos operadores de consulta padrão do LINQ para consultas do Cosmos DB.

#### <a name="select-operator"></a>Selecione operador

A sintaxe é `input.Select(x => f(x))`, onde `f` é uma expressão escalar.

**Selecione o operador, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Selecione o operador, exemplo 2:** 

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Selecione o operador, exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

#### <a name="selectmany-operator"></a>Operador SelectMany

A sintaxe é `input.SelectMany(x => f(x))`, onde `f` é uma expressão escalar, que retorna um tipo de contentor.

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

#### <a name="where-operator"></a>Onde operador

A sintaxe é `input.Where(x => f(x))`, onde `f` é uma expressão escalar, que retorna um valor booleano.

**Onde operador, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Onde operador, exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

### <a name="composite-sql-queries"></a>Consultas SQL compostas

É possível compor os operadores anteriores para formar a consultas mais potentes. Uma vez que o Cosmos DB suporta contentores aninhados, pode concatenar ou aninhar a composição.

#### <a name="concatenation"></a>Concatenação

A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Uma consulta concatenada pode começar com um opcional `SelectMany` consulta, seguida por vários `Select` ou `Where` operadores.

**Concatenação, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Concatenação, exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Concatenação, exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Concatenação, exemplo 4:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

#### <a name="nesting"></a>Aninhamento

A sintaxe é `input.SelectMany(x=>x.Q())` em que `Q` é um `Select`, `SelectMany`, ou `Where` operador.

Uma consulta aninhada aplica-se a consulta interna para cada elemento do contêiner externo. Um recurso importante é que a consulta interna pode se referir aos campos dos elementos no contêiner de externo, como uma associação automática.

**Aninhamento, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Aninhamento, exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Aninhamento, exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a id="References"></a>Referências

- [Especificação de Cosmos DB SQL do Azure](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [Especificação de JavaScript](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz. [Técnicas de avaliação para grandes bancos de dados de consulta](https://dl.acm.org/citation.cfm?id=152611). *ACM computação pesquisas* 25, não. 2 (1993).
- Graefe, G. "A estrutura de Revezar para otimização de consultas." *Dados de IEEE Eng. Bull.* 18, no. 3 (1995).
- Lu, Ooi, Tan. "Processamento de consultas em sistemas de base de dados relacional paralelas." *Computador de IEEE sociedade Press* (1994).
- Olston, Christopher, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, and Andrew Tomkins. "Pig Latin: Um não-para-idiomas estrangeiros para processamento de dados." *SIGMOD* (2008).

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Cosmos DB][introduction]
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Níveis de consistência do Azure Cosmos DB][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
