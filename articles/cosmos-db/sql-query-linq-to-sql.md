---
title: Tradução LINQ para SQL em Azure Cosmos DB
description: Saiba os operadores linq suportados e como as consultas linq são mapeadas para consultas SQL em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3f8753518e1d54ddba4fc15a5a030308d0c112a1
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042497"
---
# <a name="linq-to-sql-translation"></a>Tradução LINQ para SQL

O fornecedor de consulta DB Azure Cosmos realiza um melhor mapeamento de esforço de uma consulta LINQ em uma consulta Cosmos DB SQL. Se pretender obter a consulta SQL que é traduzida para LINQ, utilize o `ToString()` método no `IQueryable` objeto gerado. A seguinte descrição pressupõe uma familiaridade básica com o LINQ.

O sistema de tipo fornecedor de consulta suporta apenas os tipos primitivos JSON: numérico, Boolean, string e nulo.

O prestador de consultas suporta as seguintes expressões escalar:

- Valores constantes, incluindo valores constantes dos tipos de dados primitivos no tempo de avaliação de consulta.
  
- Expressões de índice de propriedade/matriz que se referem à propriedade de um objeto ou de um elemento de matriz. Por exemplo:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Expressões aritméticas, incluindo expressões aritméticas comuns sobre valores numéricos e booleanos. Para obter a lista completa, consulte a [especificação Azure Cosmos DB SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Expressões de comparação de cordas, que incluem comparar um valor de corda com algum valor de corda constante.  
  
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

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Operadores linq apoiados

O fornecedor LINQ incluído com o SQL .NET SDK suporta os seguintes operadores:

- **Selecione**: As projeções traduzem-se para SQL SELECT, incluindo a construção de objetos.
- **Onde:** Os filtros traduzem-se para SQL ONDE e apoiam a tradução `&&` `||` entre, e `!` para os operadores SQL
- **SelectMany**: Permite desenrolar as matrizes à cláusula DE JUNÇÃO SQL. Utilize para acorrentar ou nidificar expressões de matrizes.
- **OrderBy** e **OrderByDescending**: Traduza para ENCOMENDA BY com ASC ou DESC.
- **Count**, **Sum**, **Min,** **Max,** e **Operadores médios** para agregação, e seus equivalentes async **CountAsync**, **SumAsync,** **MinAsync,** **MaxAsync**, e **AverageAsync**.
- **CompareTo**: Traduz para comparações de alcance. Normalmente usados para cordas, uma vez que não são comparáveis em .NET.
- **Saltar** e **Tomar**: Traduz para SQL OFFSET e LIMIT para limitar os resultados de uma consulta e fazer paginação.
- **Funções matemáticas**: Suporta a tradução de .NET , , , , , , , , , , , , , `Abs` , , `Acos` , e para `Asin` `Atan` `Ceiling` `Cos` `Exp` as `Floor` `Log` `Log10` `Pow` `Round` `Sign` `Sin` `Sqrt` `Tan` `Truncate` funções equivalentes SQL incorporados.
- **Funções de corda**: Suporta a tradução de .NET , , , , , , , `Concat` , , `Contains` , , , `Count` , , e `EndsWith` para `IndexOf` as `Replace` `Reverse` `StartsWith` `SubString` `ToLower` `ToUpper` `TrimEnd` `TrimStart` funções equivalentes sql incorporadas.
- **Funções de matriz**: Suporta a tradução de .NET `Concat` , e para as `Contains` `Count` funções equivalentes de SQL incorporadas.
- **Funções de Extensão Geoespacial**: Suporta a tradução a partir de métodos de `Distance` `IsValid` `IsValidDetailed` encaixe, e `Within` para as funções equivalentes de SQL incorporadas.
- **Função-Definição de função Função :** Suporta a tradução do método do encaixe `UserDefinedFunctionProvider.Invoke` para a função definida pelo utilizador correspondente.
- **Diversos**: Suporta a tradução de `Coalesce` operadores condicional. Pode traduzir `Contains` para String CONTAINS, ARRAY_CONTAINS ou SQL IN, dependendo do contexto.

## <a name="examples"></a>Exemplos

Os exemplos a seguir ilustram como alguns dos operadores de consulta padrão linq se traduzem para consultas cosmos DB.

### <a name="select-operator"></a>Selecione operador

A sintaxe `input.Select(x => f(x))` é, onde `f` está uma expressão escalar.

**Selecione operador, exemplo 1:**

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

A sintaxe é `input.SelectMany(x => f(x))` , onde está uma expressão escalar que devolve um tipo de `f` recipiente.

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

A sintaxe é `input.Where(x => f(x))` , onde está uma expressão `f` escalar, que devolve um valor booleano.

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

## <a name="composite-sql-queries"></a>Consultas compostas de SQL

Pode compor os operadores anteriores para formar consultas mais poderosas. Uma vez que a Cosmos DB suporta recipientes aninhados, pode concatenar ou nidificar a composição.

### <a name="concatenation"></a>Concatenação

A sintaxe `input(.|.SelectMany())(.Select()|.Where())*` é. Uma consulta concatenated pode começar com uma `SelectMany` consulta opcional, seguida de vários `Select` ou `Where` operadores.

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

A sintaxe é `input.SelectMany(x=>x.Q())` onde `Q` está `Select` `SelectMany` um, ou `Where` operador.

Uma consulta aninhada aplica a consulta interna a cada elemento do recipiente exterior. Uma característica importante é que a consulta interna pode referir-se aos campos dos elementos no recipiente exterior, como uma união auto-união.

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


## <a name="next-steps"></a>Próximos passos

- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento do modelo](modeling-data.md)
