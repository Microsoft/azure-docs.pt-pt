---
title: Interpretação semântica - API de serviço de exploração de conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar interpretação semântica no conhecimento exploração de serviço (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814486"
---
# <a name="semantic-interpretation"></a>Interpretação semântica

Interpretação semântica associa saída semântica cada caminho interpretado por meio da gramática.  Em particular, o serviço avalia a seqüência de instruções no `tag` elementos envolvidos pela interpretação para computar a saída final.  

Uma instrução pode ser uma atribuição de um literal ou uma variável a outra variável.  -Lo, também, pode atribuir a saída de uma função com 0 ou mais parâmetros a uma variável.  Cada parâmetro de função pode ser especificado com um literal ou uma variável.  Se a função não devolve quaisquer dados, a atribuição é omitida.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Uma variável é especificada com um identificador de nome que começa com uma letra e consiste apenas em letras (A-Z), números (0-9) e o caráter de sublinhado (\_).  O tipo implicitamente é inferido do literal ou valor atribuído a ele de saída de função. 

Segue-se uma lista de tipos de dados atualmente suportados:

|Tipo|Descrição|Exemplos|
|----|----|----|
|String|Sequência de 0 ou mais carateres|"Hello World!"<br/>""|
|Booleano|Valor booleano|true<br/>false|
|Int32|inteiro de 32 bits assinado.  -2.1e9 para 2.1e9|123<br/>-321|
|Int64|inteiro assinado de 64 bits. -9.2e18 e 9.2e18|9876543210|
|Double|Vírgula flutuante de dupla precisão. 1.7E + /-308 (15 dígitos)|123.456789<br/>1.23456789e2|
|Guid|Identificador exclusivo global|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Consulta|Expressão de consulta que especifica um subconjunto de objetos de dados no índice|ALL()<br/>And(*q1*, *q2*)|

## <a name="semantic-functions"></a>Funções semânticas

Existe um conjunto interno de funções de semânticas.  Eles permitem a construção de consultas avançadas e fornecem controle sensível ao contexto sobre interpretações de gramática.

### <a name="and-function"></a>E a função

`query = And(query1, query2);`

Devolve uma consulta composta a partir da interseção entre duas consultas de entrada.

### <a name="or-function"></a>Ou uma função

`query = Or(query1, query2);`

Devolve uma consulta composta provenientes da União de duas consultas de entrada.

### <a name="all-function"></a>Função all

`query = All();`

Devolve uma consulta que inclui todos os objetos de dados.

No exemplo a seguir, usamos a função de All() iterativamente acumular uma consulta com base na intersecção de 1 ou mais palavras-chave.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Nenhuma função

`query = None();`

Devolve uma consulta que inclui não existem objetos de dados.

No exemplo a seguir, usamos a função de None() iterativamente acumular uma consulta com base na União de 1 ou mais palavras-chave.

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

Devolve uma consulta que inclui apenas os objetos de dados cujo atributo *attrName* corresponde ao valor *valor* , de acordo com a operação especificada *op*, que está predefinido para "eq".  Normalmente, utiliza um `attrref` elemento para criar uma consulta com base na cadeia de consulta de entrada correspondida.  Se um valor é dado ou obtido por meio de outros meios, a função de Query () pode ser utilizada para criar uma consulta que correspondam este valor.

No exemplo a seguir, usamos a função de Query () para implementar o suporte para especificar as publicações acadêmicas de uma década específica.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Função composta

`query = Composite(innerQuery);`

Devolve uma consulta que encapsula uma *innerQuery* composto por correspondências em relação a atributos de frações de um atributo composto comuns *attr*.  O encapsulamento requer o atributo composto *attr* de qualquer objeto de dados correspondente para ter, pelo menos, um valor que satisfaça individualmente a *innerQuery*.  Tenha em atenção que uma consulta em atributos de frações de um atributo composto tem de ser encapsuladas com a função de Composite() antes de ele pode ser combinado com outras consultas.

Por exemplo, a seguinte consulta devolve publicações acadêmicas por "harry shum" enquanto esteve na "microsoft":
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Por outro lado, a seguinte consulta devolve as publicações acadêmicas em que é um dos autores "harry shum" e uma das afiliações é "microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>Função de GetVariable

`value = GetVariable(name, scope);`

Devolve o valor da variável *name* definidas em especificado *âmbito*.  *nome* é um identificador que começa com uma letra e consiste apenas em letras (A-Z), números (0-9) e o caráter de sublinhado (_).  *âmbito* pode ser definido como "pedido" ou "system".  Tenha em atenção que as variáveis definidas em escopos diferentes são diferentes entre si, incluindo aquelas definidas por meio de saída das funções de semânticas.

Variáveis de âmbito do pedido são partilhadas entre todos os interpretações dentro do pedido atual de interpretação.  Eles podem ser utilizados para controlar o âmbito da procura interpretações sobre a gramática.

Variáveis do sistema estão predefinidas pelo serviço e podem ser usadas para recuperar várias estatísticas sobre o estado atual do sistema.  Segue-se o conjunto de variáveis do sistema atualmente suportados:

|Name|Tipo|Descrição|
|----|----|----|
|IsAtEndOfQuery|Booleano|VERDADEIRO se a interpretação atual encontrou correspondências em todo o texto de consulta de entrada|
|IsBeyondEndOfQuery|Booleano|VERDADEIRO se a interpretação atual sugeriu conclusões além do texto de consulta de entrada|

### <a name="setvariable-function"></a>Função de SetVariable

`SetVariable(name, value, scope);`

Atribui *valor* a variável *nome* sob especificado *âmbito*.  *nome* é um identificador que começa com uma letra e consiste apenas em letras (A-Z), números (0-9) e o caráter de sublinhado (_).  Atualmente, o único valor válido para *âmbito* é "Pedir".  Não há nenhuma variável do sistema definível.

Variáveis de âmbito do pedido são partilhadas entre todos os interpretações dentro do pedido atual de interpretação.  Eles podem ser utilizados para controlar o âmbito da procura interpretações sobre a gramática.

### <a name="assertequals-function"></a>Função de AssertEquals

`AssertEquals(value1, value2);`

Se *value1* e *value2* serão equivalentes, a função tem êxito e não tem efeitos colaterais.  Caso contrário, a função falha e rejeita a interpretação.

### <a name="assertnotequals-function"></a>Função de AssertNotEquals

`AssertNotEquals(value1, value2);`

Se *value1* e *value2* serão não equivalentes, a função tem êxito e não tem efeitos colaterais.  Caso contrário, a função falha e rejeita a interpretação.


