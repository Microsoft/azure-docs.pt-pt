---
title: Referência de sintaxe SQLRuleAction em Azure Service Bus
description: Este artigo fornece uma referência para a sintaxe SQLRuleAction. As ações são escritas na sintaxe baseada em linguagem SQL que é realizada contra uma mensagem intermediada.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 61fa6e046b4d4a0ba91bf8608c846755026d07ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341580"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>Referência de sintaxe SQLRuleAction para Azure Service Bus

A *SqlRuleAction* é um exemplo da classe [SqlRuleAction,](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) e representa um conjunto de ações escritas na sintaxe baseada em linguagem SQL que é realizada contra uma [MediaedMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Este artigo lista detalhes sobre a gramática de ação da regra SQL.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
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
  
-   `<scope>` é uma cadeia opcional que indica o alcance do `<property_name>` . Valores válidos são `sys` ou `user` . O `sys` valor indica o âmbito do sistema onde é um nome de propriedade pública da Classe `<property_name>` [MediaedMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`indica o âmbito do utilizador onde `<property_name>` é uma chave do dicionário Da Classe [DeMetragem.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) `user` o âmbito de aplicação é o âmbito padrão se `<scope>` não for especificado.  
  
### <a name="remarks"></a>Observações  

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
  
 Isto significa que qualquer cadeia que comece com uma letra e seja seguida por um ou mais sublinhado/letra/dígito.  
  
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
  
     Seguem-se exemplos de longas constantes:  
  
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
  
As constantes booleanas são representadas pelas palavras-chave `TRUE` ou `FALSE` . Os valores são armazenados como `System.Boolean` .  
  
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

- O SET é utilizado para criar um novo imóvel ou atualizar o valor de um imóvel existente.
- Remove é usado para remover uma propriedade.
- O SET realiza conversão implícita se possível quando o tipo de expressão e o tipo de propriedade existente são diferentes.
- A ação falha se as propriedades do sistema inexistentes forem referenciadas.
- A ação não falha se as propriedades de utilizador inexistentes forem referenciadas.
- Uma propriedade utilizadora inexistente é avaliada como "Desconhecida" internamente, seguindo a mesma semântica que o [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) ao avaliar os operadores.

## <a name="next-steps"></a>Passos seguintes

- [Classe SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Classe SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
