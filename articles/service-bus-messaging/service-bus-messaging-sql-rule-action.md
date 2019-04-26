---
title: Referência de sintaxe de SQLRuleAction no Azure | Documentos da Microsoft
description: Detalhes sobre a gramática de SQLRuleAction.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: 0f9365b72da1cec81eed82756097d32b1d72ca71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307483"
---
# <a name="sqlruleaction-syntax"></a>Sintaxe de SQLRuleAction

R *SqlRuleAction* é uma instância da [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) classe e representa conjunto de ações de escritas em linguagem SQL com base numa sintaxe que é executada em relação a um [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Este artigo apresenta detalhes sobre a gramática de ação de regra SQL.  
  
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
  
-   `<scope>` é uma cadeia de caracteres opcional que indica o escopo do `<property_name>`. Os valores válidos são `sys` ou `user`. O `sys` valor indica o âmbito do sistema em que `<property_name>` é um nome de propriedade pública da [BrokeredMessage classe](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica o âmbito de utilizador onde `<property_name>` é uma chave do [BrokeredMessage classe](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) dicionário. `user` o escopo é o âmbito de predefinição se `<scope>` não for especificado.  
  
### <a name="remarks"></a>Observações  

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
  
 Isso significa que qualquer cadeia de caracteres que começa com uma letra e é seguida por uma ou mais um caráter de sublinhado/letra/dígitos.  
  
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
  
     Seguem-se exemplos de constantes de longas:  
  
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
  
Booleanos constantes são representados por palavras-chave `TRUE` ou `FALSE`. Os valores são armazenados como `System.Boolean`.  
  
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

- CONJUNTO é utilizado para criar uma nova propriedade ou Atualize o valor de uma propriedade existente.
- REMOVER é usado para remover uma propriedade.
- CONJUNTO realiza a conversão implícita de se for possível quando o tipo de expressão e o tipo de propriedade existentes são diferentes.
- Ação falha se as propriedades do sistema não existente foram referenciadas.
- Ação não falha se as propriedades de usuário inexistentes foram referenciadas.
- Uma propriedade de usuário inexistentes é avaliada como "Desconhecido" internamente, seguindo a mesma semântica [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) ao avaliar a operadores.

## <a name="next-steps"></a>Passos Seguintes

- [Classe de SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Classe de SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
