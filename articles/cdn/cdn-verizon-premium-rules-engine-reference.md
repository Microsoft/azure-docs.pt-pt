---
title: Referência do motor de regras CDN do Azure | Documentos da Microsoft
description: Funcionalidades e as condições de correspondência do motor de regras de documentação de referência para a CDN do Azure.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593155"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>CDN do Azure da referência do motor de regras de Premium da Verizon

Este artigo apresenta uma lista de descrições detalhadas das condições de correspondência disponíveis e dos recursos de rede do Azure da entrega de conteúdos (CDN) [motor de regras](cdn-verizon-premium-rules-engine.md).

O mecanismo de regras foi concebido para ser a autoridade final em tipos específicos de pedidos são processados pela CDN.

**Utilizações comuns**:

- Substituir ou definir uma política de cache personalizada.
- Proteger ou negar pedidos para conteúdo confidencial.
- Redirecionar pedidos.
- Store dados de registo personalizado.

## <a name="terminology"></a>Terminologia

Uma regra é definida através da utilização de [ **expressões condicionais**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [ **coincidam com condições**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), e [ **funcionalidades**](cdn-verizon-premium-rules-engine-reference-features.md). Esses elementos estão realçados na ilustração seguinte:

 ![Condição de correspondência CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Sintaxe

A maneira na qual os carateres especiais são tratados varia de acordo com a como uma condição de correspondência ou funcionalidade lida com valores de texto. Uma condição de correspondência ou funcionalidade pode interpretar o texto em uma das seguintes formas:

1. [**Valores literais**](#literal-values)
2. [**Valores de caráter universal**](#wildcard-values)
3. [**Expressões regulares**](#regular-expressions)

### <a name="literal-values"></a>Valores literais

Texto que é interpretado como um valor literal trata todos os caracteres especiais, com exceção do símbolo %, como parte do valor que deve corresponder. Em outras palavras, um literal corresponde à condição definida como `\'*'\` for satisfeita apenas quando que exact valor (ou seja, `\'*'\`) foi encontrado.

Um símbolo de percentagem é utilizado para indicar a codificação do URL (por exemplo, `%20`).

### <a name="wildcard-values"></a>Valores de caráter universal

Texto que é interpretado como um valor de caráter universal atribui o significado adicional para carateres especiais. A tabela seguinte descreve como o seguinte conjunto de caracteres é interpretado:

Caráter | Descrição
----------|------------
\ | Uma barra invertida é utilizada para escapar qualquer um dos carateres especificados nesta tabela. Tem de especificar uma barra invertida diretamente antes dos carateres especiais que devem ser escritos.<br/>Por exemplo, a seguinte sintaxe escapes um asterisco: `\*`
% | Um símbolo de percentagem é utilizado para indicar a codificação do URL (por exemplo, `%20`).
\* | Um asterisco é um caráter universal que representa um ou mais carateres.
Espaço | Um caractere de espaço indica que uma condição de correspondência pode ser atendida por qualquer um dos valores especificados ou padrões.
'Valor' | Uma aspa não tem um significado especial. No entanto, um conjunto de aspas simples é usado para indicar que um valor deve ser tratado como um valor literal. Ele pode ser usado das seguintes formas:<br><br/>-Permite que uma condição de correspondência de ser cumpridos sempre que o valor especificado corresponde a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer um dos seguintes cadeias de caracteres: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />-Permite que um caractere especial seja especificado como um caráter literal. Por exemplo, pode especificar um caractere de espaço literal colocando um caractere de espaço dentro de um conjunto de aspas simples (ou seja, `' '` ou `'sample value'`).<br/>-Permite que um valor em branco ser especificado. Especifique um valor em branco, especificando um conjunto de aspas simples (ou seja, ').<br /><br/>**Importante:**<br/>– Se o valor especificado não contém um caráter universal, em seguida, é automaticamente considerado um valor literal, o que significa que não é necessário especificar um conjunto de aspas simples.<br/>– Se uma barra invertida de escape não outro caráter nesta tabela, ela é ignorada se for especificado dentro de um conjunto de aspas simples.<br/>-Outra maneira de especificar um caráter especial, como um caráter literal é para o escape-lo com uma barra invertida (ou seja, `\`).

### <a name="regular-expressions"></a>Expressões regulares

Expressões regulares definem um padrão que é procurado dentro de um valor de texto. A notação de expressão regular define significados específicos a uma variedade de símbolos. A tabela seguinte indica os carateres especiais como são tratadas pelo condições de correspondência e funcionalidades que oferecem suporte a expressões regulares.

Caráter especial | Descrição
------------------|------------
\ | Uma barra invertida escapes o caráter seguinte it, que faz com que esse caractere deve ser tratada como um valor literal em vez de adotar no seu significado de expressão regular. Por exemplo, a seguinte sintaxe escapes um asterisco: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | An asterisk allows the preceding character to be matched zero or more times.
Space | A space character is typically treated as a literal character.
'value' | Single quotes are treated as literal characters. A set of single quotes does not have special meaning.

## Next steps

- [Rules engine match conditions](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)
- [Azure CDN overview](cdn-overview.mdO significado de um símbolo de percentagem depende da sua utilização. Docs
descr`%{HTTPVariable}`: Essa sintaxe identifica uma variável de HTTP.match`%{HTTPVariable%Pattern}`: Essa sintaxe utiliza um símbolo de percentagem para identificar um HTTP variável e como delimitador.1/2019`\``: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\`20`).f the available match conditions and features for the Azure Content Delivery Network (CDN) [rules engine](cdn-verizon-premium-rules-engine.md).

The rules engine is designed to be the final authority on how specific types of requests are processed by the CDN.

**Common uses**:

- Override or define a custom cache policy.
- Secure or deny requests for sensitive content.
- Redirect requests.
- Store custom log data.

## Terminology

A rule is defined through the use of [**conditional expressions**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**match conditions**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), and [**features**](cdn-verizon-premium-rules-engine-reference-features.md). These elements are highlighted in the following illustration:

 ![CDN match condition](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## Syntax

The manner in which special characters are treated varies according to how a match condition or feature handles text values. A match condition or feature may interpret text in one of the following ways:

1. [**Literal values**](#literal-values)
2. [**Wildcard values**](#wildcard-values)
3. [**Regular expressions**](#regular-expressions)

### Literal values

Text that is interpreted as a literal value treats all special characters, with the exception of the % symbol, as a part of the value that must be matched. In other words, a literal match condition set to `\'*'\` is only satisfied when that exact value (that is, `\'*'\`) is found.

A percentage symbol is used to indicate URL encoding (for example, `%20`).

### Wildcard values

Text that is interpreted as a wildcard value assigns additional meaning to special characters. The following table describes how the following set of characters is interpreted:

Character | Description
----------|------------
\ | A backslash is used to escape any of the characters specified in this table. A backslash must be specified directly before the special character that should be escaped.<br/>For example, the following syntax escapes an asterisk: `\*`
% | A percentage symbol is used to indicate URL encoding (for example, `%20`).
\* | An asterisk is a wildcard that represents one or more characters.
Space | A space character indicates that a match condition may be satisfied by either of the specified values or patterns.
'value' | A single quote does not have special meaning. However, a set of single quotes is used to indicate that a value should be treated as a literal value. It can be used in the following ways:<br><br/>- It allows a match condition to be satisfied whenever the specified value matches any portion of the comparison value.  For example, `'ma'` would match any of the following strings: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- It allows a special character to be specified as a literal character. For example, you may specify a literal space character by enclosing a space character within a set of single quotes (that is, `' '` or `'sample value'`).<br/>- It allows a blank value to be specified. Specify a blank value by specifying a set of single quotes (that is, '').<br /><br/>**Important:**<br/>- If the specified value does not contain a wildcard, then it is automatically considered a literal value, which means that it is not necessary to specify a set of single quotes.<br/>- If a backslash does not escape another character in this table, it is ignored when it is specified within a set of single quotes.<br/>- Another way to specify a special character as a literal character is to escape it using a backslash (that is, `\`).

### Regular expressions

Regular expressions define a pattern that is searched for within a text value. Regular expression notation defines specific meanings to a variety of symbols. The following table indicates how special characters are treated by match conditions and features that support regular expressions.

Special Character | Description
------------------|------------
\ | A backslash escapes the character the follows it, which causes that character to be treated as a literal value instead of taking on its regular expression meaning. For example, the following syntax escapes an asterisk: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | Um asterisco permite que o caractere anterior corresponder zero ou mais vezes.
Espaço | Normalmente, um caractere de espaço é tratado como um caráter literal.
'Valor' | Aspas são tratadas como caracteres literais. Um conjunto de aspas não tem um significado especial.

## <a name="next-steps"></a>Passos Seguintes

- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Substituir comportamento HTTP utilizando o mecanismo de regras](cdn-verizon-premium-rules-engine.md)
- [Descrição geral da CDN do Azure](cdn-overview.md)