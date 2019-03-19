---
title: Referência do motor de regras CDN do Azure | Documentos da Microsoft
description: Funcionalidades e as condições de correspondência do motor de regras de documentação de referência para a CDN do Azure.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 3163b33f69f4cc2d6cd4127253c7b6fadfddd6b0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994238"
---
# <a name="azure-cdn-rules-engine-reference"></a>Referência do motor de regras CDN do Azure
Este artigo apresenta uma lista de descrições detalhadas das condições de correspondência disponíveis e dos recursos de rede do Azure da entrega de conteúdos (CDN) [motor de regras](cdn-rules-engine.md).

O mecanismo de regras foi concebido para ser a autoridade final em tipos específicos de pedidos são processados pela CDN.

**Utilizações comuns**:

- Substituir ou definir uma política de cache personalizada.
- Proteger ou negar pedidos para conteúdo confidencial.
- Redirecionar pedidos.
- Store dados de registo personalizado.

## <a name="terminology"></a>Terminologia
Uma regra é definida através da utilização de [ **expressões condicionais**](cdn-rules-engine-reference-conditional-expressions.md), [ **coincidam com condições**](cdn-rules-engine-reference-match-conditions.md), e [ **funcionalidades**](cdn-rules-engine-reference-features.md). Esses elementos estão realçados na ilustração seguinte:

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
% | O significado de um símbolo de percentagem depende da sua utilização.<br/><br/> `%{HTTPVariable}`: Essa sintaxe identifica uma variável de HTTP.<br/>`%{HTTPVariable%Pattern}`: Essa sintaxe utiliza um símbolo de percentagem para identificar um HTTP variável e como delimitador.<br />`\%`: Um símbolo de percentagem de carateres de escape permite a ser utilizado como um valor literal ou para indicar a codificação do URL (por exemplo, `\%20`).
\* | Um asterisco permite que o caractere anterior corresponder zero ou mais vezes. 
Espaço | Normalmente, um caractere de espaço é tratado como um caráter literal. 
'Valor' | Aspas são tratadas como caracteres literais. Um conjunto de aspas não tem um significado especial.


## <a name="next-steps"></a>Passos Seguintes
* [Condições de correspondência do motor de regras](cdn-rules-engine-reference-match-conditions.md)
* [Expressões condicionais do motor de regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Funcionalidades do motor de regras](cdn-rules-engine-reference-features.md)
* [Substituir comportamento HTTP utilizando o mecanismo de regras](cdn-rules-engine.md)
* [Descrição geral da CDN do Azure](cdn-overview.md)
