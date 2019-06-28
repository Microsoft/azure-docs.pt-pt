---
title: LINQ para tradução de SQL no Azure Cosmos DB
description: O mapeamento de consultas do LINQ para consultas de SQL do Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 057614da8fd29e1208c2788049c5d6d1a985eed5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342518"
---
# <a name="linq-to-sql-translation"></a>LINQ para tradução de SQL

O provedor de consultas do Azure Cosmos DB executa um melhor mapeamento de esforço de uma consulta do LINQ numa consulta do Cosmos DB SQL. A seguinte descrição pressupõe uma familiaridade básica com o LINQ.

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

## <a id="SupportedLinqOperators"></a>Operadores LINQ suportados

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

## <a name="examples"></a>Exemplos

Os exemplos seguintes mostram como traduzir alguns dos operadores de consulta padrão do LINQ para consultas do Cosmos DB.

### <a name="select-operator"></a>Selecione operador

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

## <a name="composite-sql-queries"></a>Consultas SQL compostas

É possível compor os operadores anteriores para formar a consultas mais potentes. Uma vez que o Cosmos DB suporta contentores aninhados, pode concatenar ou aninhar a composição.

### <a name="concatenation"></a>Concatenação

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

### <a name="nesting"></a>Aninhamento

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


## <a name="next-steps"></a>Passos Seguintes

- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dados de documento do modelo](modeling-data.md)
