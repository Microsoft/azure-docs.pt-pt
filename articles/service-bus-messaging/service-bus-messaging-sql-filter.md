---
title: Referência de sintaxe de sqlfilter do barramento de serviço do Azure | Microsoft Docs
description: Detalhes sobre a gramática de sqlfilter.
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
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "60591797"
---
# <a name="sqlfilter-syntax"></a>Sintaxe de SQLFilter

Um objeto sqlfilter é uma instância da [classe sqlfilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)e representa uma expressão de filtro baseada em linguagem SQL que é avaliada em um [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Um sqlfilter dá suporte a um subconjunto do padrão SQL-92.  
  
 Este tópico lista detalhes sobre a gramática do sqlfilter.  
  
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
  
-   `<scope>`é uma cadeia de caracteres opcional que indica o `<property_name>`escopo do. Os valores válidos `sys` são `user`ou. O `sys` valor indica o escopo do `<property_name>` sistema em que é um nome de propriedade pública da [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`indica o escopo do `<property_name>` usuário, em que é uma chave do dicionário da [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . `user`escopo será o escopo padrão se `<scope>` não for especificado.  
  
## <a name="remarks"></a>Observações

Uma tentativa de acessar uma propriedade de sistema inexistente é um erro, enquanto uma tentativa de acessar uma propriedade de usuário não existente não é um erro. Em vez disso, uma propriedade de usuário não existente é avaliada internamente como um valor desconhecido. Um valor desconhecido é tratado especialmente durante a avaliação do operador.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentos  

 `<regular_identifier>`é uma cadeia de caracteres representada pela seguinte expressão regular:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Essa gramática significa qualquer cadeia de caracteres que comece com uma letra e seja seguida por um ou mais sublinhado/letra/dígito.  
  
`[:IsLetter:]`significa qualquer caractere Unicode Categorizado como uma letra Unicode. `System.Char.IsLetter(c)`retorna `true` se`c` é uma letra Unicode.  
  
`[:IsDigit:]`significa qualquer caractere Unicode Categorizado como um dígito decimal. `System.Char.IsDigit(c)`retorna `true` se`c` é um dígito Unicode.  
  
Um `<regular_identifier>` não pode ser uma palavra-chave reservada.  
  
`<delimited_identifier>`é qualquer cadeia de caracteres que esteja entre colchetes à esquerda/direita ([]). Um colchete direito é representado como dois colchetes à direita. Veja a seguir exemplos de `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>`é qualquer cadeia de caracteres que esteja entre aspas duplas. Um sinal de aspas duplas no identificador é representado como duas aspas duplas. Não é recomendável usar identificadores entre aspas porque ele pode ser facilmente confundido com uma constante de cadeia de caracteres. Use um identificador delimitado, se possível. Veja a seguir um exemplo de `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>padrão  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações
  
`<pattern>`deve ser uma expressão avaliada como uma cadeia de caracteres. Ele é usado como um padrão para o operador LIKE.      Ele pode conter os seguintes caracteres curinga:  
  
-   `%`:  Qualquer cadeia de zero ou mais caracteres.  
  
-   `_`: Qualquer caractere único.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Observações  

`<escape_char>`deve ser uma expressão avaliada como uma cadeia de caracteres de comprimento 1. Ele é usado como um caractere de escape para o operador LIKE.  
  
 Por exemplo, `property LIKE 'ABC\%' ESCAPE '\'` corresponde `ABC%` em vez de uma cadeia de caracteres `ABC`que começa com.  
  
## <a name="constant"></a>constante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentos  
  
-   `<integer_constant>`é uma cadeia de números que não são colocados entre aspas e não contêm pontos decimais. Os valores são armazenados como `System.Int64` internamente e seguem o mesmo intervalo.  
  
     Estes são exemplos de constantes longas:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`é uma cadeia de números que não são colocados entre aspas e que contêm um ponto decimal. Os valores são armazenados como `System.Double` internamente e seguem o mesmo intervalo/precisão.  
  
     Em uma versão futura, esse número pode ser armazenado em um tipo de dados diferente para dar suporte à semântica numérica exata, portanto, você não deve confiar no fato de que o `System.Double` tipo `<decimal_constant>`de dados subjacente é para.  
  
     Veja a seguir exemplos de constantes decimais:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`é um número escrito em notação científica. Os valores são armazenados como `System.Double` internamente e seguem o mesmo intervalo/precisão. Veja a seguir exemplos de constantes de número aproximado:  
  
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

As constantes booleanas são representadas pelas palavras-chave **true** ou **false**. Os valores são armazenados como `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Observações  

As constantes de cadeia de caracteres são colocadas entre aspas simples e incluem caracteres Unicode válidos. Uma aspa simples inserida em uma constante de cadeia de caracteres é representada como duas aspas simples.  
  
## <a name="function"></a>função  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Observações
  
A `newid()` função retorna um **System. GUID** gerado pelo `System.Guid.NewGuid()` método.  
  
A `property(name)` função retorna o valor da propriedade referenciada por `name`. O `name` valor pode ser qualquer expressão válida que retorne um valor de cadeia de caracteres.  
  
## <a name="considerations"></a>Considerações
  
Considere a seguinte [](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) semântica sqlfilter:  
  
-   Os nomes de propriedade não diferenciam maiúsculas de minúsculas.  
  
-   Os operadores C# seguem a semântica de conversão implícita sempre que possível.  
  
-   As propriedades do sistema são propriedades públicas expostas em instâncias [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .  
  
    Considere a seguinte `IS [NOT] NULL` semântica:  
  
    -   `property IS NULL`é avaliado como `true` se a propriedade não existir ou o valor da propriedade for. `null`  
  
### <a name="property-evaluation-semantics"></a>Semântica de avaliação de propriedade  
  
- Uma tentativa de avaliar uma propriedade de sistema inexistente gera [](/dotnet/api/microsoft.servicebus.messaging.filterexception) uma exceção de FilterException.  
  
- Uma propriedade que não existe é avaliada internamente como **desconhecida**.  
  
  Avaliação desconhecida em operadores aritméticos:  
  
- Para operadores binários, se o lado esquerdo e/ou direito dos operandos for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
- Para operadores unários, se um operando for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida em operadores de comparação binária:  
  
- Se o lado esquerdo e/ou direito dos operandos for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida em `[NOT] LIKE`:  
  
- Se qualquer operando for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida em `[NOT] IN`:  
  
- Se o operando esquerdo for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida no operador **and** :  
  
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
  
 Avaliação desconhecida no operador **or** :  
  
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
  
### <a name="operator-binding-semantics"></a>Semântica de associação de operador
  
-   Operadores `>`de comparação, como `>=` `<`, `<=`,, C# , e`=` seguem a mesma semântica que a associação de operador em promoções de tipo de dados e conversões implícitas. `!=`  
  
-   Operadores `+`aritméticos, como `-` `*`, `/`,, e `%` seguem a mesma semântica que a C# Associação de operador em promoções de tipo de dados e conversões implícitas.

## <a name="next-steps"></a>Passos seguintes

- [Classe sqlfilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Classe sqlfilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Classe sqlruleaction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
