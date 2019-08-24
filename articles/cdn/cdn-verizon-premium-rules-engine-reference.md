---
title: Referência do mecanismo de regras da CDN do Azure | Microsoft Docs
description: Documentação de referência para condições e recursos de correspondência do mecanismo de regras da CDN do Azure.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aab93204c850223756f28a56ea550f912e28e0d2
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996760"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Referência do mecanismo de regras da CDN do Azure da Verizon Premium

Este artigo lista descrições detalhadas das condições e dos recursos de correspondência disponíveis para o [mecanismo de regras](cdn-verizon-premium-rules-engine.md)da CDN (rede de distribuição de conteúdo) do Azure.

O mecanismo de regras foi projetado para ser a autoridade final sobre como tipos específicos de solicitações são processados pela CDN.

**Usos comuns**:

- Substituir ou definir uma política de cache Personalizada.
- Proteger ou negar solicitações de conteúdo confidencial.
- Redirecionar solicitações.
- Armazene dados de log personalizados.

## <a name="terminology"></a>Terminologia

Uma regra é definida por meio do uso de [**expressões condicionais**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**condições de correspondência**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)e [**recursos**](cdn-verizon-premium-rules-engine-reference-features.md). Esses elementos são realçados na ilustração a seguir:

 ![Condição de correspondência da CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Sintaxe

A maneira como os caracteres especiais são tratados varia de acordo com a forma como uma condição de correspondência ou recurso lida com valores de texto. Uma condição de correspondência ou recurso pode interpretar o texto de uma das seguintes maneiras:

1. [**Valores literais**](#literal-values)
2. [**Valores curinga**](#wildcard-values)
3. [**Expressões regulares**](#regular-expressions)

### <a name="literal-values"></a>Valores literais

O texto que é interpretado como um valor literal trata todos os caracteres especiais, com exceção do símbolo%, como parte do valor que deve ser correspondido. Em outras palavras, uma condição de correspondência literal definida `\'*'\` como só será satisfeita quando esse valor exato (isto é `\'*'\`,) for encontrado.

Um símbolo de porcentagem é usado para indicar a codificação de URL ( `%20`por exemplo,).

### <a name="wildcard-values"></a>Valores curinga

O texto que é interpretado como um valor de caractere curinga atribui significado adicional a caracteres especiais. A tabela a seguir descreve como o seguinte conjunto de caracteres é interpretado:

Caráter | Descrição
----------|------------
\ | Uma barra invertida é usada para escapar qualquer um dos caracteres especificados nesta tabela. Uma barra invertida deve ser especificada diretamente antes do caractere especial que deve ter escape.<br/>Por exemplo, a sintaxe a seguir escapa um asterisco:`\*`
% | Um símbolo de porcentagem é usado para indicar a codificação de URL ( `%20`por exemplo,).
\* | Um asterisco é um caractere curinga que representa um ou mais caracteres.
Espaço | Um caractere de espaço indica que uma condição de correspondência pode ser satisfeita por um dos valores ou padrões especificados.
valor | Uma aspa simples não tem significado especial. No entanto, um conjunto de aspas simples é usado para indicar que um valor deve ser tratado como um valor literal. Ele pode ser usado das seguintes maneiras:<br><br/>-Permite que uma condição de correspondência seja satisfeita sempre que o valor especificado corresponder a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cadeias de caracteres: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template. **ma**p<br /><br />-Permite que um caractere especial seja especificado como um caractere literal. Por exemplo, você pode especificar um caractere de espaço literal ao colocar um caractere de espaço dentro de um conjunto de aspas simples (ou `' '` seja `'sample value'`, ou).<br/>-Permite que um valor em branco seja especificado. Especifique um valor em branco especificando um conjunto de aspas simples (ou seja, ' ').<br /><br/>**Importante:**<br/>-Se o valor especificado não contiver um caractere curinga, ele será automaticamente considerado como um valor literal, o que significa que não é necessário especificar um conjunto de aspas simples.<br/>-Se uma barra invertida não escapar de outro caractere nesta tabela, ela será ignorada quando for especificada dentro de um conjunto de aspas simples.<br/>-Outra maneira de especificar um caractere especial como um caractere literal é escapar dele usando uma barra invertida (ou seja, `\`).

### <a name="regular-expressions"></a>Expressões regulares

Expressões regulares definem um padrão que é pesquisado dentro de um valor de texto. A notação de expressão regular define significações específicas para uma variedade de símbolos. A tabela a seguir indica como os caracteres especiais são tratados por condições de correspondência e recursos que dão suporte a expressões regulares.

Caractere especial | Descrição
------------------|------------
\ | Uma barra invertida escapa o caractere do seguinte, o que faz com que esse caractere seja tratado como um valor literal em vez de assumir seu significado de expressão regular. Por exemplo, a sintaxe a seguir escapa um asterisco:`\*`
% | O significado de um símbolo de porcentagem depende de seu uso.<br/><br/> `%{HTTPVariable}`: Essa sintaxe identifica uma variável HTTP.<br/>`%{HTTPVariable%Pattern}`: Essa sintaxe usa um símbolo de porcentagem para identificar uma variável HTTP e como um delimitador.<br />`\%`: Escapar um símbolo de porcentagem permite que ele seja usado como um valor literal ou para indicar a codificação de URL (por `\%20`exemplo,).
\* | Um asterisco permite que o caractere anterior seja correspondido zero ou mais vezes.
Espaço | Um caractere de espaço normalmente é tratado como um caractere literal.
valor | Aspas simples são tratadas como caracteres literais. Um conjunto de aspas simples não tem significado especial.

As condições de correspondência e os recursos que oferecem suporte a expressões regulares aceitam padrões definidos por PCRE (expressões regulares compatíveis com Perl).

## <a name="next-steps"></a>Passos Seguintes

- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Substituir o comportamento HTTP usando o mecanismo de regras](cdn-verizon-premium-rules-engine.md)
- [Visão geral da CDN do Azure](cdn-overview.md)
