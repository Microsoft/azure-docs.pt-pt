---
title: Referência de sintaxe do Azure Service Bus SQLFilter Microsoft Docs
description: Este artigo fornece detalhes sobre a gramática SQLFilter. Um SqlFilter suporta um subconjunto da norma SQL-92.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8412dea583ae119b30976e53d4751411b45339a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341597"
---
# <a name="sqlfilter-syntax"></a>Sintaxe de SQLFilter

Um objeto *SqlFilter* é um exemplo da [classe SqlFilter,](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)e representa uma expressão de filtro baseada em linguagem SQL que é avaliada contra uma [Intermediada Message](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Um SqlFilter suporta um subconjunto da norma SQL-92.  
  
 Este tópico lista detalhes sobre a gramática sqlFilter.  
  
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
  
-   `<scope>` é uma cadeia opcional que indica o alcance do `<property_name>` . Valores válidos são `sys` ou `user` . O `sys` valor indica o âmbito do sistema onde é um nome de propriedade pública da classe `<property_name>` [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`indica o âmbito do utilizador onde `<property_name>` é uma chave do dicionário da classe [BrokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) `user` o âmbito de aplicação é o âmbito padrão se `<scope>` não for especificado.  
  
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

 `<regular_identifier>` é uma corda representada pela seguinte expressão regular:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Esta gramática significa qualquer cadeia que comece com uma letra e seja seguida por um ou mais sublinhado/letra/dígito.  
  
`[:IsLetter:]` significa qualquer personagem Unicode que seja classificado como uma letra Unicode. `System.Char.IsLetter(c)` retorna `true` se for uma letra `c` Unicode.  
  
`[:IsDigit:]` significa qualquer personagem Unicode que seja classificado como um dígito decimal. `System.Char.IsDigit(c)` retorna `true` se for um dígito `c` Unicode.  
  
A `<regular_identifier>` não pode ser uma palavra-chave reservada.  
  
`<delimited_identifier>` é qualquer corda que seja fechada com suportes quadrados esquerdo/direito ([]). Um suporte quadrado direito é representado como dois suportes quadrados direito. Seguem-se exemplos `<delimited_identifier>` de:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` é qualquer cadeia que é fechada com aspas duplas. Uma marca de dupla cotação no identificador é representada como duas aspas duplas. Não é aconselhável utilizar identificadores citados porque pode ser facilmente confundido com uma constante de corda. Use um identificador delimitado, se possível. Segue-se um exemplo `<quoted_identifier>` de:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>padrão  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações
  
`<pattern>` deve ser uma expressão que é avaliada como uma corda. É utilizado como um padrão para o operador LIKE.      Pode conter os seguintes caracteres wildcard:  
  
-   `%`: Qualquer sequência de zero ou mais caracteres.  
  
-   `_`: Qualquer personagem.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações  

`<escape_char>` deve ser uma expressão que seja avaliada como uma cadeia de comprimento 1. É usado como um personagem de fuga para o operador LIKE.  
  
 Por exemplo, `property LIKE 'ABC\%' ESCAPE '\'` combina em vez de uma corda que começa com `ABC%` `ABC` .  
  
## <a name="constant"></a>constante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentos  
  
-   `<integer_constant>` é uma série de números que não estão incluídos em aspas e não contêm pontos decimais. Os valores são armazenados `System.Int64` internamente e seguem o mesmo alcance.  
  
     Estes são exemplos de longas constantes:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` é uma série de números que não são incluídos em aspas, e contêm um ponto decimal. Os valores são armazenados `System.Double` internamente e seguem a mesma gama/precisão.  
  
     Numa versão futura, este número pode ser armazenado num tipo de dados diferente para suportar a semântica de número exato, pelo que não deve confiar no facto de que o tipo de dados subjacente é `System.Double` para `<decimal_constant>` .  
  
     Seguem-se exemplos de constantes decimais:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` é um número escrito em notação científica. Os valores são armazenados `System.Double` internamente e seguem a mesma gama/precisão. Seguem-se exemplos de constantes de números aproximados:  
  
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

As constantes booleanas são representadas pelas palavras-chave **TRUE** ou **FALSE**. Os valores são armazenados como `System.Boolean` .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Observações  

As constantes de corda são incluídas em aspas únicas e incluem caracteres Unicode válidos. Uma única marca de aspas incorporada numa constante de corda é representada como duas aspas únicas.  
  
## <a name="function"></a>função  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Observações
  
A `newid()` função devolve um **Sistema.Guid** gerado pelo `System.Guid.NewGuid()` método.  
  
A `property(name)` função devolve o valor do imóvel referenciado por `name` . O `name` valor pode ser qualquer expressão válida que devolva um valor de cadeia.  
  
## <a name="considerations"></a>Considerações
  
Considere as seguintes semânticas [SqlFilter:](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)  
  
-   Os nomes das propriedades são insensíveis.  
  
-   Os operadores seguem a semântica de conversão C# sempre que possível.  
  
-   As propriedades do sistema são propriedades públicas expostas em [instâncias de IntermedMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)  
  
    Considere as `IS [NOT] NULL` seguintes semânticas:  
  
    -   `property IS NULL` é avaliado como `true` se ou a propriedade não existisse ou o valor da propriedade é `null` .  
  
### <a name="property-evaluation-semantics"></a>Semântica de avaliação de propriedade  
  
- Uma tentativa de avaliar uma propriedade do sistema inexistente lança uma exceção [FilterException.](/dotnet/api/microsoft.servicebus.messaging.filterexception)  
  
- Um imóvel que não existe é avaliado internamente como **desconhecido.**  
  
  Avaliação desconhecida nos operadores aritméticos:  
  
- Para os operadores binários, se o lado esquerdo e/ou direito dos óperas for avaliado como **desconhecido,** então o resultado é **desconhecido.**  
  
- Para os operadores não-ofários, se um operand for avaliado como **desconhecido,** então o resultado é **desconhecido.**  
  
  Avaliação desconhecida nos operadores de comparação binária:  
  
- Se o lado esquerdo e/ou direito dos óperas for avaliado como **desconhecido,** então o resultado é **desconhecido.**  
  
  Avaliação desconhecida em `[NOT] LIKE` :  
  
- Se alguma ópera for avaliada como **desconhecida,** então o resultado é **desconhecido.**  
  
  Avaliação desconhecida em `[NOT] IN` :  
  
- Se a ópera esquerda for avaliada como **desconhecida,** então o resultado é **desconhecido.**  
  
  Avaliação desconhecida em **E** operador:  
  
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
  
### <a name="operator-binding-semantics"></a>Semântica de ligação do operador
  
-   Operadores de comparação `>` `>=` como, `<` , , , , e `<=` `!=` `=` seguem a mesma semântica que o operador C# que liga em promoções tipo de dados e conversões implícitas.  
  
-   Operadores aritméticos `+` `-` como, `*` , , , `/` e `%` seguem a mesma semântica que o operador C# que liga em promoções tipo de dados e conversões implícitas.

## <a name="next-steps"></a>Passos seguintes

- [Classe SQLFilter (.Net Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Classe SQLFilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Classe SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
