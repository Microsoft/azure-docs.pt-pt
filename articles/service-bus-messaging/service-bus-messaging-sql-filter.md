---
title: Referência de sintaxe de SQLFilter de barramento de serviço do Azure | Documentos da Microsoft
description: Detalhes sobre a gramática de SQLFilter.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: e490c7c24ed38e2988c1f097b09b508746f08178
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118140"
---
# <a name="sqlfilter-syntax"></a>Sintaxe de SQLFilter

R *SqlFilter* objeto é uma instância da [classe SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)e representa uma expressão de filtro com base na linguagem SQL que é avaliada em comparação com uma [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Um SqlFilter suporta um subconjunto do SQL-92 padrão.  
  
 Este tópico lista os detalhes sobre a gramática de SqlFilter.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>Argumentos  
  
-   `<scope>` é uma cadeia de caracteres opcional que indica o escopo do `<property_name>`. Os valores válidos são `sys` ou `user`. O `sys` valor indica o âmbito do sistema em que `<property_name>` é um nome de propriedade pública da [BrokeredMessage classe](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica o âmbito de utilizador onde `<property_name>` é uma chave do [BrokeredMessage classe](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) dicionário. `user` o escopo é o âmbito de predefinição se `<scope>` não for especificado.  
  
## <a name="remarks"></a>Observações

Uma tentativa de aceder uma propriedade de sistema não existente é um erro, enquanto uma tentativa de aceder uma propriedade de utilizador não existente não é um erro. Em vez disso, uma propriedade de usuário inexistentes internamente é avaliada como um valor desconhecido. Um valor desconhecido é tratado especialmente durante a avaliação de operador.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentos  

 `<regular_identifier>` uma cadeia de caracteres é representada pela seguinte expressão regular:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
V této gramatice significa qualquer cadeia de caracteres que começa com uma letra e é seguida por uma ou mais um caráter de sublinhado/letra/dígitos.  
  
`[:IsLetter:]` significa que qualquer caráter Unicode que é categorizado como uma letra de Unicode. `System.Char.IsLetter(c)` Devolve `true` se `c` é uma letra de Unicode.  
  
`[:IsDigit:]` significa que qualquer caráter Unicode que é categorizado como um dígito decimal. `System.Char.IsDigit(c)` Devolve `true` se `c` é um dígito de Unicode.  
  
A `<regular_identifier>` não pode ser uma palavra-chave reservada.  
  
`<delimited_identifier>` é qualquer cadeia de caracteres entre com esquerda/direita Parênteses Retos ([]). Um colchete à direita é representado como dois colchetes à direita. Seguem-se exemplos de `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` é qualquer cadeia de caracteres é colocadas entre aspas duplas. Uma aspas de fecho no identificador é representada como dois entre aspas duplas. Não é recomendado usar identificadores delimitados por aspas, porque ela pode facilmente ser confundida com uma constante de cadeia de caracteres. Utilize um identificador delimitado se possível. Segue-se um exemplo de `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Padrão  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações
  
`<pattern>` tem de ser uma expressão que é avaliada como uma cadeia de caracteres. Ele é usado como um padrão para o operador LIKE.      Pode conter os seguintes carateres universais:  
  
-   `%`:  Qualquer cadeia de caracteres de zero ou mais.  
  
-   `_`: Um único caráter.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações  

`<escape_char>` tem de ser uma expressão que é avaliada como uma cadeia de caracteres de comprimento 1. Ele é usado como um caráter de escape para o operador LIKE.  
  
 Por exemplo, `property LIKE 'ABC\%' ESCAPE '\'` corresponde `ABC%` em vez de uma cadeia de caracteres que começa com `ABC`.  
  
## <a name="constant"></a>constante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentos  
  
-   `<integer_constant>` é uma cadeia de números que não estão entre aspas e não contêm pontos decimais. Os valores são armazenados como `System.Int64` internamente e siga o mesmo intervalo.  
  
     Estes são exemplos de constantes de longas:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` é uma cadeia de números que não estão entre aspas e contêm uma casa decimal. Os valores são armazenados como `System.Double` internamente e siga a mesmo intervalo/precisão.  
  
     Numa versão futura, este número pode ser armazenado num tipo de dados diferentes para oferecer suporte a semântica de número exata, portanto, não deverá confiar no fato de subjacentes o tipo de dados é `System.Double` para `<decimal_constant>`.  
  
     Seguem-se exemplos de constantes decimais:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` é um escrito em número uma notação científica. Os valores são armazenados como `System.Double` internamente e siga a mesmo intervalo/precisão. Seguem-se exemplos de constantes de números aproximados:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Observações  

Booleanos constantes são representados por palavras-chave **TRUE** ou **falso**. Os valores são armazenados como `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Observações  

Constantes de cadeia de caracteres colocados entre aspas e incluam quaisquer carateres Unicode válidos. Um único ponto de exclamação incorporado numa constante de cadeia de caracteres é representado por dois único entre aspas duplas.  
  
## <a name="function"></a>função  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Observações
  
O `newid()` função devolve um **System** gerado pelo `System.Guid.NewGuid()` método.  
  
O `property(name)` função devolve o valor da propriedade referenciado pelo `name`. O `name` valor pode ser qualquer expressão válida, que retorna um valor de cadeia de caracteres.  
  
## <a name="considerations"></a>Considerações
  
Considere o seguinte [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) semântica:  
  
-   Os nomes das propriedades diferenciam maiúsculas de minúsculas.  
  
-   Operadores siga c# conversão implícita semântica sempre que possível.  
  
-   Propriedades do sistema são propriedades públicas expostas na [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) instâncias.  
  
    Considere o seguinte `IS [NOT] NULL` semântica:  
  
    -   `property IS NULL` é avaliada como `true` se a propriedade não existe ou é o valor da propriedade `null`.  
  
### <a name="property-evaluation-semantics"></a>Semântica de avaliação de propriedade  
  
- Uma tentativa para avaliar uma propriedade inexistente sistema gera uma [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) exceção.  
  
- Uma propriedade que não existe internamente é avaliada como **desconhecido**.  
  
  Avaliação desconhecido no operadores aritméticos:  
  
- Para operadores binários, se é considerado de esquerda e direita do operandos **desconhecido**, em seguida, o resultado é **desconhecido**.  
  
- Para os operadores unários, se um operando é avaliado como **desconhecido**, em seguida, o resultado é **desconhecido**.  
  
  Avaliação desconhecido no operadores de comparação binário:  
  
- Se é considerado de esquerda e direita do operandos **desconhecido**, em seguida, o resultado é **desconhecido**.  
  
  Avaliação desconhecido no `[NOT] LIKE`:  
  
- Se o operando qualquer é avaliado como **desconhecido**, em seguida, o resultado é **desconhecido**.  
  
  Avaliação desconhecido no `[NOT] IN`:  
  
- Se o operando esquerdo é avaliado como **desconhecido**, em seguida, o resultado é **desconhecido**.  
  
  Avaliação desconhecido no **AND** operador:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Avaliação desconhecido no **ou** operador:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Semântica da associação de operador
  
-   Operadores de comparação, como `>`, `>=`, `<`, `<=`, `!=`, e `=` siga a mesma semântica de como o operador de c# de enlace de promoções de tipo de dados e conversões implícitas.  
  
-   Operadores aritméticos como `+`, `-`, `*`, `/`, e `%` siga a mesma semântica de como o operador de c# de enlace de promoções de tipo de dados e conversões implícitas.

## <a name="next-steps"></a>Passos Seguintes

- [Classe de SQLFilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Classe de SQLFilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Classe de SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
