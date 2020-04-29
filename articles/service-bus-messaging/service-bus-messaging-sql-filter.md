---
title: Referência sintaxe Azure Service Bus SQLFilter [ Sintaxe] Microsoft Docs
description: Este artigo fornece detalhes sobre a gramática SQLFilter. Um SqlFilter suporta um subconjunto da norma SQL-92.
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
ms.openlocfilehash: d5a8e165fcee23c5feecd5935983dd77d3ec6c30
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76759668"
---
# <a name="sqlfilter-syntax"></a>Sintaxe de SQLFilter

Um objeto *SqlFilter* é uma instância da [classe SqlFilter,](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)e representa uma expressão de filtro baseada em linguagem SQL que é avaliada contra uma [Mensagem intermediada](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Um SqlFilter suporta um subconjunto da norma SQL-92.  
  
 Este tópico lista detalhes sobre a gramática SqlFilter.  
  
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
  
-   `<scope>`é uma cadeia opcional que `<property_name>`indica o alcance do . Valores `sys` válidos são ou `user`. O `sys` valor indica `<property_name>` o âmbito do sistema onde é um nome de propriedade pública da [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`indica o `<property_name>` âmbito do utilizador onde é uma chave do dicionário da [classe BrokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) `user`âmbito é o `<scope>` âmbito padrão se não for especificado.  
  
## <a name="remarks"></a>Observações

Uma tentativa de aceder a uma propriedade do sistema inexistente é um erro, enquanto uma tentativa de aceder a uma propriedade de utilizador inexistente não é um erro. Em vez disso, uma propriedade de utilizador inexistente é avaliada internamente como um valor desconhecido. Um valor desconhecido é tratado especialmente durante a avaliação do operador.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentos  

 `<regular_identifier>`é uma cadeia representada pela seguinte expressão regular:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Esta gramática significa qualquer corda que comece com uma letra e seja seguida por um ou mais sublinhados/letra/dígito.  
  
`[:IsLetter:]`significa qualquer personagem Unicode que seja categorizado como uma letra Unicode. `System.Char.IsLetter(c)``true` devoluções `c` se for uma letra Unicode.  
  
`[:IsDigit:]`significa qualquer personagem Unicode que seja categorizado como um dígito decimal. `System.Char.IsDigit(c)``true` devoluções `c` se for um dígito Unicode.  
  
A `<regular_identifier>` não pode ser uma palavra-chave reservada.  
  
`<delimited_identifier>`é qualquer corda que seja fechada com suportes quadrados esquerdo/direito ([]). Um suporte quadrado direito é representado como dois suportes quadrados direito. Seguem-se exemplos de: `<delimited_identifier>`  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>`é qualquer corda que seja fechada com duas aspas. Uma marca dupla de citação no identificador é representada como duas marcas duplas de citação. Não é aconselhável utilizar identificadores citados porque pode facilmente ser confundido com uma constante de corda. Utilize um identificador delimitado, se possível. Segue-se um `<quoted_identifier>`exemplo de:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>padrão  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações
  
`<pattern>`deve ser uma expressão que é avaliada como uma corda. É usado como um padrão para o operador LIKE.      Pode conter os seguintes caracteres wildcard:  
  
-   `%`: Qualquer série de caracteres zero ou mais.  
  
-   `_`: Qualquer personagem único.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações  

`<escape_char>`deve ser uma expressão que é avaliada como uma cadeia de comprimento 1. É usado como um personagem de fuga para o operador LIKE.  
  
 Por `property LIKE 'ABC\%' ESCAPE '\'` exemplo, `ABC%` combina em vez `ABC`de uma corda que começa com .  
  
## <a name="constant"></a>constante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentos  
  
-   `<integer_constant>`é uma série de números que não estão fechados em aspas e não contêm pontos decimais. Os valores são `System.Int64` armazenados internamente e seguem a mesma gama.  
  
     Estes são exemplos de longas constantes:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`é uma série de números que não estão fechados em aspas, e contêm um ponto decimal. Os valores são `System.Double` armazenados internamente e seguem a mesma gama/precisão.  
  
     Numa versão futura, este número pode ser armazenado num tipo de dados diferente para suportar a semântica `System.Double` `<decimal_constant>`exata dos números, pelo que não deve confiar no facto de o tipo de dados subjacente ser para .  
  
     Seguem-se exemplos de constantes decimais:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`é um número escrito em notação científica. Os valores são `System.Double` armazenados internamente e seguem a mesma gama/precisão. Seguem-se exemplos de constantes de número supressão:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Observações  

As constantes booleanas são representadas pelas palavras-chave **TRUE** ou **FALSE**. Os valores são `System.Boolean`armazenados como .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Observações  

As constantes de cordas são fechadas em letras únicas e incluem quaisquer caracteres Unicode válidos. Uma única marca de citação incorporada numa constante de corda é representada como duas únicas marcas de citação.  
  
## <a name="function"></a>função  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Observações
  
A `newid()` função devolve um **System.Guid** gerado pelo `System.Guid.NewGuid()` método.  
  
A `property(name)` função devolve o valor `name`do imóvel referenciado por . O `name` valor pode ser qualquer expressão válida que retorne um valor de cadeia.  
  
## <a name="considerations"></a>Considerações
  
Considere a seguinte semântica [SqlFilter:](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)  
  
-   Os nomes de propriedade são insensíveis.  
  
-   Os operadores seguem a semântica de conversão implícita C# sempre que possível.  
  
-   As propriedades do sistema são propriedades públicas expostas em casos [brokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)  
  
    Considere a `IS [NOT] NULL` seguinte semântica:  
  
    -   `property IS NULL`é avaliado `true` como se a propriedade não existisse ou o `null`valor da propriedade é .  
  
### <a name="property-evaluation-semantics"></a>Semântica de avaliação de propriedades  
  
- Uma tentativa de avaliar uma propriedade do sistema inexistente lança uma exceção [filterException.](/dotnet/api/microsoft.servicebus.messaging.filterexception)  
  
- Um imóvel que não existe é avaliado internamente como **desconhecido.**  
  
  Avaliação desconhecida nos operadores aritméticos:  
  
- Para os operadores binários, se o lado esquerdo e/ou direito dos operands for avaliado como **desconhecido,** então o resultado é **desconhecido**.  
  
- Para os operadores não-secundários, se um operand é avaliado como **desconhecido,** então o resultado é **desconhecido.**  
  
  Avaliação desconhecida nos operadores de comparação binária:  
  
- Se o lado esquerdo e/ou direito dos operands for avaliado como **desconhecido,** então o resultado é **desconhecido.**  
  
  Avaliação `[NOT] LIKE`desconhecida em:  
  
- Se algum operand for avaliado como **desconhecido,** então o resultado é **desconhecido.**  
  
  Avaliação `[NOT] IN`desconhecida em:  
  
- Se o operand esquerdo for avaliado como **desconhecido,** então o resultado é **desconhecido.**  
  
  Avaliação desconhecida no **e** operador:  
  
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
  
 Avaliação desconhecida no operador **de OR:**  
  
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
  
### <a name="operator-binding-semantics"></a>Semântica de encadernação do operador
  
-   Operadores de `>` `>=`comparação como, `<`, , `<=`e `!=` `=` seguir a mesma semântica que o operador C# vincular em promoções de tipo de dados e conversões implícitas.  
  
-   Operadores aritméticos `-` `*`como, `+` `%` e `/`sigam a mesma semântica que o operador C# vinculando em promoções de tipo de dados e conversões implícitas.

## <a name="next-steps"></a>Passos seguintes

- [Classe SQLFilter (.QUADRO NET)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Classe SQLFilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Classe SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
