---
title: Interpretação semântica - Serviço de Exploração de Conhecimento API
titlesuffix: Azure Cognitive Services
description: Saiba como usar interpretação semântica na API do Serviço de Exploração do Conhecimento (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385656"
---
# <a name="semantic-interpretation"></a>Interpretação Semântica

A interpretação semântica associa a produção semântica a cada caminho interpretado através da gramática.  Em particular, o serviço avalia a sequência de declarações no `tag` elementos atravessados pela interpretação para calcular a saída final.  

Uma declaração pode ser uma atribuição de uma variável literal ou variável a outra variável.  Pode também atribuir a saída de uma função com 0 ou mais parâmetros a uma variável.  Cada parâmetro de função pode ser especificado com uma variável literal ou variável.  Se a função não devolver qualquer saída, a atribuição é omitida.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Uma variável é especificada usando um identificador de nome que começa com uma letra e consiste apenas em letras (A-Z), números (0-9) e o sublinhado (\_).  O seu tipo é implicitamente inferido do valor literal ou de saída da função que lhe é atribuído. 

Abaixo está uma lista de tipos de dados atualmente suportados:

|Tipo|Descrição|Exemplos|
|----|----|----|
|String|Sequência de 0 ou mais caracteres|"Olá Mundo!"<br/>""|
|Bool|Valor booleano|true<br/>false|
|Int32|32 bits de inteiro assinado.  -2.1e9 a 2.1e9|123<br/>-321|
|Int64|64 bits de inteiro assinado. -9.2e18 e 9.2e18|9876543210|
|Valor de duplo|Ponto flutuante de dupla precisão. 1.7e+/-308 (15 dígitos)|123.456789<br/>1.23456789e2|
|Guid|Identificador globalmente único|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Consulta|Expressão de consulta que especifica um subconjunto de objetos de dados no índice|Todos()<br/>E,*q1,* *q2,*|

## <a name="semantic-functions"></a>Funções semânticas

Há um conjunto incorporado de funções semânticas.  Permitem a construção de consultas sofisticadas e proporcionam um controlo sensível ao contexto sobre as interpretações gramaticais.

### <a name="and-function"></a>E Função

`query = And(query1, query2);`

Devolve uma consulta composta a partir do cruzamento de duas consultas de entrada.

### <a name="or-function"></a>Ou Função

`query = Or(query1, query2);`

Devolve uma consulta composta pela união de duas consultas de entrada.

### <a name="all-function"></a>Todas as funções

`query = All();`

Devolve uma consulta que inclui todos os objetos de dados.

No exemplo seguinte, utilizamos a função All() para construir iterativamente uma consulta baseada na intersecção de 1 ou mais palavras-chave.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Nenhuma Função

`query = None();`

Devolve uma consulta que não inclui objetos de dados.

No exemplo seguinte, utilizamos a função None para construir iterativamente uma consulta baseada na união de 1 ou mais palavras-chave.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Função de consulta

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Devolve uma consulta que inclui apenas objetos de dados cujo atributo *attrName* corresponde ao *valor* de acordo com a operação especificada *operação operação*operação, que não se aplica a "eq".  Normalmente, use um elemento `attrref` para criar uma consulta com base na cadeia de consulta de entrada compatível.  Se um valor for dado ou obtido através de outros meios, a função Consulta() pode ser utilizada para criar uma consulta que corresponda a este valor.

No exemplo seguinte, utilizamos a função Consulta para implementar suporte para especificar publicações académicas de uma determinada década.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Função Composta

`query = Composite(innerQuery);`

Devolve uma consulta que encapsula um *interiorComposto* composto por fósforos contra sub-atributos de um atributo composto comum *attr*.  A encapsulação requer que o atributo composto *de* qualquer objeto de dados correspondente tenha pelo menos um valor que satisfaça individualmente o *interior.*  Note que uma consulta sobre sub-atributos de um atributo composto deve ser encapsulada utilizando a função Composta() antes de poder ser combinada com outras consultas.

Por exemplo, a seguinte consulta devolve publicações académicas por "harry shum" enquanto esteve na "Microsoft":
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Por outro lado, a seguinte consulta devolve publicações académicas onde um dos autores é "harry shum" e uma das afiliações é "microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>Função GetVariable

`value = GetVariable(name, scope);`

Devolve o valor da *denominação* variável definido no *âmbito*especificado .  *o nome* é um identificador que começa com uma letra e consiste apenas em letras (A-Z), números (0-9) e o sublinhado (_).  *o âmbito* pode ser definido para "solicitar" ou "sistema".  Note que as variáveis definidas em diferentes âmbitos são distintas umas das outras, incluindo as definidas através da saída de funções semânticas.

As variáveis de âmbito de pedido são partilhadas em todas as interpretações dentro do pedido de interpretação atual.  Podem ser usados para controlar a procura de interpretações sobre a gramática.

As variáveis do sistema são predefinidas pelo serviço e podem ser usadas para recuperar várias estatísticas sobre o estado atual do sistema.  Abaixo está o conjunto de variáveis do sistema atualmente suportadas:

|Nome|Tipo|Descrição|
|----|----|----|
|IsAtEndOfQuery|Bool|verdade se a interpretação atual correspondeu a todo o texto de consulta de entrada|
|IsBeyondEndOfQuery|Bool|verdade se a interpretação atual sugeriu conclusões para além do texto de consulta de entrada|

### <a name="setvariable-function"></a>Função SetVariable

`SetVariable(name, value, scope);`

Atribui *valor* à *denominação* variável sob o *âmbito*especificado .  *o nome* é um identificador que começa com uma letra e consiste apenas em letras (A-Z), números (0-9) e o sublinhado (_).  Atualmente, o único valor válido para *o âmbito* é "pedido".  Não há variáveis de sistema definidos.

As variáveis de âmbito de pedido são partilhadas em todas as interpretações dentro do pedido de interpretação atual.  Podem ser usados para controlar a procura de interpretações sobre a gramática.

### <a name="assertequals-function"></a>Assertequals Função

`AssertEquals(value1, value2);`

Se o *valor1* e o *valor2* forem equivalentes, a função tem sucesso e não tem efeitos secundários.  Caso contrário, a função falha e rejeita a interpretação.

### <a name="assertnotequals-function"></a>Função AssertNotEquals

`AssertNotEquals(value1, value2);`

Se o *valor1* e o *valor2* não forem equivalentes, a função tem sucesso e não tem efeitos secundários.  Caso contrário, a função falha e rejeita a interpretação.


