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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
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
  
-   `<scope>` é uma cadeia opcional que indica o alcance do `<property_name>`. Os valores válidos são `sys` ou `user`. O valor `sys` indica o âmbito do sistema onde `<property_name>` é um nome de propriedade pública da [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica o âmbito do utilizador onde `<property_name>` é uma chave do dicionário da [classe BrokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) `user` âmbito é o âmbito padrão se não for especificado `<scope>`.  
  
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

 `<regular_identifier>` é uma cadeia representada pela seguinte expressão regular:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Esta gramática significa qualquer corda que comece com uma letra e seja seguida por um ou mais sublinhados/letra/dígito.  
  
`[:IsLetter:]` significa qualquer personagem Unicode que seja categorizado como uma letra Unicode. `System.Char.IsLetter(c)` devolve `true` se `c` é uma letra Unicode.  
  
`[:IsDigit:]` significa qualquer personagem unicode que seja categorizado como um dígito decimal. `System.Char.IsDigit(c)` retorna `true` se `c` for um dígito Unicode.  
  
Um `<regular_identifier>` não pode ser uma palavra-chave reservada.  
  
`<delimited_identifier>` é qualquer corda que seja fechada com suportes quadrados esquerdo/direito ([]). Um suporte quadrado direito é representado como dois suportes quadrados direito. Seguem-se exemplos de `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` é qualquer corda que seja fechada com duas aspas. Uma marca dupla de citação no identificador é representada como duas marcas duplas de citação. Não é aconselhável utilizar identificadores citados porque pode facilmente ser confundido com uma constante de corda. Utilize um identificador delimitado, se possível. Segue-se um exemplo de `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>padrão  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações
  
`<pattern>` deve ser uma expressão que é avaliada como uma corda. É usado como um padrão para o operador LIKE.      Pode conter os seguintes caracteres wildcard:  
  
-   `%`: Qualquer série de caracteres zero ou mais.  
  
-   `_`: Qualquer personagem.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações  

`<escape_char>` deve ser uma expressão que é avaliada como uma cadeia de comprimento 1. É usado como um personagem de fuga para o operador LIKE.  
  
 Por exemplo, `property LIKE 'ABC\%' ESCAPE '\'` corresponde `ABC%` em vez de uma corda que começa com `ABC`.  
  
## <a name="constant"></a>constante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentos  
  
-   `<integer_constant>` é uma série de números que não estão fechados em aspas e não contêm pontos decimais. Os valores são armazenados como `System.Int64` internamente, e seguem a mesma gama.  
  
     Estes são exemplos de longas constantes:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` é uma série de números que não estão fechados em aspas, e contêm um ponto decimal. Os valores são armazenados como `System.Double` internamente, e seguem a mesma gama/precisão.  
  
     Numa versão futura, este número pode ser armazenado num tipo de dados diferente para suportar a semântica exata dos números, pelo que não deve confiar no facto de o tipo de dados subjacente estar `System.Double` para `<decimal_constant>`.  
  
     Seguem-se exemplos de constantes decimais:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` é um número escrito em notação científica. Os valores são armazenados como `System.Double` internamente, e seguem a mesma gama/precisão. Seguem-se exemplos de constantes de número supressão:  
  
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

As constantes booleanas são representadas pelas palavras-chave **TRUE** ou **FALSE**. Os valores são armazenados como `System.Boolean`.  
  
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
  
A função `newid()` devolve um **System.Guid** gerado pelo método `System.Guid.NewGuid()`.  
  
A função `property(name)` devolve o valor do imóvel referenciado por `name`. O valor `name` pode ser qualquer expressão válida que retorne um valor de cadeia.  
  
## <a name="considerations"></a>Considerações
  
Considere a seguinte semântica [SqlFilter:](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)  
  
-   Os nomes de propriedade são insensíveis.  
  
-   Os C# operadores seguem sempre que possível a semântica implícita de conversão.  
  
-   As propriedades do sistema são propriedades públicas expostas em casos [brokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)  
  
    Considere a seguinte semântica `IS [NOT] NULL` seguinte:  
  
    -   `property IS NULL` é avaliado como `true` se a propriedade não existe ou se o valor do imóvel é `null`.  
  
### <a name="property-evaluation-semantics"></a>Semântica de avaliação de propriedades  
  
- Uma tentativa de avaliar uma propriedade do sistema inexistente lança uma exceção [filterException.](/dotnet/api/microsoft.servicebus.messaging.filterexception)  
  
- Um imóvel que não existe é avaliado internamente como **desconhecido.**  
  
  Avaliação desconhecida nos operadores aritméticos:  
  
- Para os operadores binários, se o lado esquerdo e/ou direito dos operands for avaliado como **desconhecido,** então o resultado é **desconhecido**.  
  
- Para os operadores não-secundários, se um operand é avaliado como **desconhecido,** então o resultado é **desconhecido.**  
  
  Avaliação desconhecida nos operadores de comparação binária:  
  
- Se o lado esquerdo e/ou direito dos operands for avaliado como **desconhecido,** então o resultado é **desconhecido.**  
  
  Avaliação desconhecida em `[NOT] LIKE`:  
  
- Se algum operand for avaliado como **desconhecido,** então o resultado é **desconhecido.**  
  
  Avaliação desconhecida em `[NOT] IN`:  
  
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
  
-   Operadores de comparação como `>`, `>=`, `<`, `<=`C# , `!=`e `=` seguem a mesma semântica que o operador vincula em promoções de tipo de dados e conversões implícitas.  
  
-   Operadores aritméticos como `+`, `-`, `*`, `/`C# e `%` seguem a mesma semântica que o operador vincula em promoções de tipo de dados e conversões implícitas.

## <a name="next-steps"></a>Passos seguintes

- [Classe SQLFilter (.QUADRO NET)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Classe SQLFilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Classe SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
