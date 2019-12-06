---
title: Conversão de LINQ to SQL em Azure Cosmos DB
description: Conheça os operadores LINQ com suporte e como as consultas LINQ são mapeadas para consultas SQL no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d8dd6392cf22852a10c1dc2600edcbc647f3c510
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871164"
---
# <a name="linq-to-sql-translation"></a>LINQ para tradução de SQL

O provedor de consulta Azure Cosmos DB executa um mapeamento de melhor esforço de uma consulta LINQ em uma consulta SQL Cosmos DB. A descrição a seguir pressupõe uma familiaridade básica com o LINQ.

O sistema do tipo de provedor de consulta dá suporte apenas aos tipos primitivos JSON: Numeric, Boolean, String e NULL.

O provedor de consultas oferece suporte às seguintes expressões escalares:

- Valores constantes, incluindo valores constantes dos tipos de dados primitivos no tempo de avaliação da consulta.
  
- Expressões de índice de matriz/propriedade que se referem à propriedade de um objeto ou de um elemento de matriz. Por exemplo:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Expressões aritméticas, incluindo expressões aritméticas comuns em valores numéricos e boolianos. Para obter a lista completa, consulte a [especificação de Azure Cosmos DB SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Expressões de comparação de cadeia de caracteres, que incluem a comparação de um valor de cadeia de caracteres com um valor de cadeia de caracteres  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Expressões de criação de objeto/matriz, que retornam um objeto de tipo de valor composto ou tipo anônimo, ou uma matriz desses objetos. Você pode aninhar esses valores.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>Operadores LINQ com suporte

O provedor LINQ incluído no SDK do .NET do SQL dá suporte aos seguintes operadores:

- **Selecione**: projeções traduzir para SQL SELECT, incluindo construção de objeto.
- **Onde**: os filtros são convertidos em SQL onde e dão suporte à conversão entre `&&`, `||`e `!` aos operadores SQL
- **SelectMany**: permite que o desenrolamento de matrizes à cláusula SQL JOIN. Use para encadear ou aninhar expressões para filtrar elementos de matriz.
- **OrderBy** e **OrderByDescending**: translate para order by com ASC ou Desc.
- **Contagem**, **soma**, **Min**, **Max**, e **média** operadores de agregação e seus equivalentes de async **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, e **AverageAsync**.
- **CompareTo**: se traduz em comparações de intervalo. Comumente usado para cadeias de caracteres, pois não são comparáveis em .NET.
- **Take**: traduz em SQL top para limitar os resultados de uma consulta.
- **Funções matemáticas**: dá suporte à conversão de .net `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`e `Truncate` para as funções internas SQL equivalentes.
- **Funções de cadeia de caracteres**: dá suporte à conversão de .net `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`e `TrimStart` para as funções internas SQL equivalentes.
- **Funções de matriz**: dá suporte à conversão do .net `Concat`, `Contains`e `Count` para as funções internas do SQL equivalentes.
- **Funções de extensão geoespaciais**: dá suporte à conversão de métodos stub `Distance`, `IsValid`, `IsValidDetailed`e `Within` às funções internas SQL equivalentes.
- **Função de extensão de função definida pelo usuário**: dá suporte à conversão do método stub `UserDefinedFunctionProvider.Invoke` para a função definida pelo usuário correspondente.
- **Miscelânea**: dá suporte à conversão de `Coalesce` e operadores condicionais. Pode converter `Contains` em cadeia de caracteres CONTAINS, ARRAY_CONTAINS ou SQL no, dependendo do contexto.

## <a name="examples"></a>Exemplos

Os exemplos a seguir ilustram como alguns dos operadores de consulta LINQ padrão são convertidos em consultas Cosmos DB.

### <a name="select-operator"></a>Selecionar operador

A sintaxe é `input.Select(x => f(x))`, onde `f` é uma expressão escalar.

**Selecione operador, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Selecione operador, exemplo 2:** 

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Selecione operador, exemplo 3:**

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

### <a name="selectmany-operator"></a>Operador SelectMany

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

### <a name="where-operator"></a>Onde operador

A sintaxe é `input.Where(x => f(x))`, onde `f` é uma expressão escalar, que retorna um valor booleano.

**Operador where, exemplo 1:**

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
  
**Operador where, exemplo 2:**

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

## <a name="composite-sql-queries"></a>Consultas SQL compostas

Você pode compor os operadores anteriores para formar consultas mais poderosas. Como Cosmos DB dá suporte a Contêineres aninhados, você pode concatenar ou aninhar a composição.

### <a name="concatenation"></a>Concatenação

A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Uma consulta concatenada pode começar com uma consulta `SelectMany` opcional, seguida por vários operadores `Select` ou `Where`.

**Concatenação, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
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

### <a name="nesting"></a>Aninhamento

A sintaxe é `input.SelectMany(x=>x.Q())` em que `Q` é um operador `Select`, `SelectMany`ou `Where`.

Uma consulta aninhada aplica a consulta interna a cada elemento do contêiner externo. Um recurso importante é que a consulta interna pode se referir aos campos dos elementos no contêiner externo, como uma auto-associação.

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


## <a name="next-steps"></a>Passos seguintes

- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento de modelo](modeling-data.md)
