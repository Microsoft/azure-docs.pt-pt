---
title: TRADUÇÃO LINQ para SQL em Azure Cosmos DB
description: Saiba como os operadores linq suportados e como as consultas LINQ são mapeadas para consultas SQL em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441259"
---
# <a name="linq-to-sql-translation"></a>Tradução LINQ para SQL

O fornecedor de consulta SQ D da Azure Cosmos realiza um melhor esforço de mapeamento de uma consulta LINQ para uma consulta Cosmos DB SQL. A seguinte descrição pressupõe uma familiaridade básica com o LINQ.

O sistema de tipo de fornecedor de consulta suporta apenas os tipos primitivos JSON: numérico, booleano, corda e nulo.

O prestador de consultas suporta as seguintes expressões escalar:

- Valores constantes, incluindo valores constantes dos tipos de dados primitivos no tempo de avaliação da consulta.
  
- Expressões de índice de propriedade/matriz que se referem à propriedade de um objeto ou de um elemento matriz. Por exemplo:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Expressões aritméticas, incluindo expressões aritméticas comuns sobre valores numéricos e booleanos. Para a lista completa, consulte a [especificação Azure Cosmos DB SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Expressões de comparação de cordas, que incluem comparar um valor de cadeia com algum valor de cadeia constante.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Expressões de criação de objetos/matrizes, que devolvem um objeto de tipo de valor composto ou tipo anónimo, ou uma matriz desses objetos. Pode nidificar estes valores.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Operadores linq suportados

O fornecedor LINQ incluído com o SQL .NET SDK suporta os seguintes operadores:

- **Selecione**: As projeções traduzem-se para O Select SQL, incluindo a construção de objetos.
- **Sempre**que : Filtros se traduzam para `&&` `||`SQL WHERE, e tradução de suporte entre, e `!` para os operadores SQL
- **SelectMany:** Permite o desenrolar das matrizes para a cláusula SQL JOIN. Utilize para correntes ou expressões de ninho para filtrar elementos de matriz.
- **OrderBy** e **OrderByDescending:** Traduza para ENCOMENDAR POR COM ASC ou DESC.
- **Contagem,** **Soma,** **Min,** **Max**e operadores **médios** para agregação, e os seus equivalentes async **CountAsync,** **SumAsync,** **MinAsync,** **MaxAsync**e **AverageAsync.**
- **CompareTo:** Traduz-se em comparações de alcance. Comumente usados para cordas, uma vez que não são comparáveis em .NET.
- **Skip** and **Take**: Traduz-se para SQL OFFSET e LIMIT para limitar os resultados de uma consulta e fazer paginação.
- **Funções matemáticas:** Suporta a `Abs` `Acos`tradução de .NET, `Asin` `Pow`, `Round` `Sign` `Sin` `Sqrt` `Tan` `Truncate` `Atan`, `Ceiling` `Cos` `Exp` `Floor` `Log` `Log10`, , , , , , , , , , , , e para as funções equivalentes sQL incorporadas.
- **Funções**de corda: Suporta `Concat`a `Contains` `Count`tradução de `Reverse` `StartsWith`.NET, `ToUpper` `TrimEnd`, `EndsWith``IndexOf` `TrimStart` , `Replace` `SubString`, , , `ToLower`, , , , , e para as funções equivalentes sQL incorporadas.
- **Funções de matriz:** Suporta `Concat` `Contains`a `Count` tradução de .NET, e para as funções incorporadas sQL equivalentes.
- **Funções de extensão geoespacial:** Suporta `Distance` `IsValid`a `IsValidDetailed`tradução dos métodos de tubos, , e `Within` as funções incorporadas equivalentes da SQL.
- **Função**de extensão de função definida `UserDefinedFunctionProvider.Invoke` pelo utilizador : Suporta a tradução do método do tubo para a função definida pelo utilizador correspondente.
- **Diversos**: Apoia a tradução `Coalesce` e os operadores condicional. Pode `Contains` traduzir-se para String CONTAINS, ARRAY_CONTAINS ou SQL IN, dependendo do contexto.

## <a name="examples"></a>Exemplos

Os exemplos seguintes ilustram como alguns dos operadores de consulta padrão linq se traduzem para consultas de DB cosmos.

### <a name="select-operator"></a>Selecionar operador

A sintaxe `input.Select(x => f(x))` `f` é, onde é uma expressão escalar.

**Selecione o operador, exemplo 1:**

- **Expressão de lambda LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Selecione o operador, exemplo 2:** 

- **Expressão de lambda LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Selecione operador, exemplo 3:**

- **Expressão de lambda LINQ**
  
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

### <a name="selectmany-operator"></a>SelectMany operador

A sintaxe `input.SelectMany(x => f(x))` `f` é, onde está uma expressão escalar que devolve um tipo de recipiente.

- **Expressão de lambda LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Onde operador

A sintaxe `input.Where(x => f(x))` `f` é, onde está uma expressão escalar, que devolve um valor booleano.

**Quando o operador, exemplo 1:**

- **Expressão de lambda LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Quando o operador, exemplo 2:**

- **Expressão de lambda LINQ**
  
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

Pode compor os operadores anteriores para formar consultas mais poderosas. Uma vez que cosmos DB suporta recipientes aninhados, você pode concatenar ou nidificar a composição.

### <a name="concatenation"></a>Concatenação

A sintaxe é. `input(.|.SelectMany())(.Select()|.Where())*` Uma consulta concatenada pode começar `SelectMany` com uma consulta `Select` `Where` opcional, seguida por vários ou operadores.

**Concatenação, exemplo 1:**

- **Expressão de lambda LINQ**
  
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

- **Expressão de lambda LINQ**
  
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

- **Expressão de lambda LINQ**
  
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

- **Expressão de lambda LINQ**
  
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

### <a name="nesting"></a>Nidificação

A sintaxe `Q` é `Select` `input.SelectMany(x=>x.Q())` `SelectMany`onde `Where` está um, ou operador.

Uma consulta aninhada aplica a consulta interna a cada elemento do recipiente exterior. Uma característica importante é que a consulta interna pode referir-se aos campos dos elementos no recipiente exterior, como uma auto-ligação.

**Nidificação, exemplo 1:**

- **Expressão de lambda LINQ**
  
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

**Nidificação, exemplo 2:**

- **Expressão de lambda LINQ**
  
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

**Nidificação, exemplo 3:**

- **Expressão de lambda LINQ**
  
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

- [Amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento do modelo](modeling-data.md)
