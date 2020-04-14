---
title: Azure CDN governa referência do motor Microsoft Docs
description: Documentação de referência para as regras do Motor De CDN Azure condições e características.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: bda817712faf1f54287e880dc62ef2b08273ff42
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253395"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN da Verizon Premium regras referência do motor

Este artigo enumera descrições detalhadas das condições de jogo disponíveis e das características do motor de [regras](cdn-verizon-premium-rules-engine.md)da Rede de Entrega de Conteúdos Azure (CDN).

O motor de regras foi concebido para ser a autoridade final sobre como tipos específicos de pedidos são processados pelo CDN.

**Utilizações comuns:**

- Anular ou definir uma política de cache personalizada.
- Proteger ou negar pedidos de conteúdo sensível.
- Redirecionamento de pedidos.
- Armazenar dados de registo personalizados.

## <a name="terminology"></a>Terminologia

Uma regra é definida através da utilização de [**expressões condicionais,**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)condições de [**jogo,**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)e [**características.**](cdn-verizon-premium-rules-engine-reference-features.md) Estes elementos são destacados na seguinte ilustração:

 ![Condição de correspondência do CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Sintaxe

A forma como os caracteres especiais são tratados varia de acordo com a forma como uma condição de correspondência ou característica lida com os valores de texto. Uma condição ou característica de correspondência pode interpretar texto de uma das seguintes formas:

1. [**Valores literais**](#literal-values)
2. [**Valores wildcard**](#wildcard-values)
3. [**Expressões regulares**](#regular-expressions)

### <a name="literal-values"></a>Valores literais

O texto interpretado como um valor literal trata todos os caracteres especiais, com exceção do símbolo %, como parte do valor que deve ser igualado. Por outras palavras, uma `\'*'\` condição de correspondência literal definida apenas é satisfeita quando esse valor exato (isto é, `\'*'\`) é encontrado.

Um símbolo percentual é usado para indicar `%20`codificação de URL (por exemplo, ).

### <a name="wildcard-values"></a>Valores wildcard

Texto que é interpretado como um valor wildcard atribui significado adicional a caracteres especiais. A tabela seguinte descreve como o seguinte conjunto de caracteres é interpretado:

Caráter | Descrição
----------|------------
\ | Um backslash é usado para escapar de qualquer um dos caracteres especificados nesta tabela. Uma barra de costas deve ser especificada diretamente antes do carácter especial que deve ser escapado.<br/>Por exemplo, a seguinte sintaxe escapa a um asterisco:`\*`
% | Um símbolo percentual é usado para indicar `%20`codificação de URL (por exemplo, ).
\* | Um asterisco é um wildcard que representa um ou mais caracteres.
Espaço | Um caracteres espaciais indica que uma condição de correspondência pode ser satisfeita por qualquer um dos valores ou padrões especificados.
'valor' | Uma única citação não tem um significado especial. No entanto, um conjunto de citações únicas é usado para indicar que um valor deve ser tratado como um valor literal. Pode ser usado das seguintes formas:<br><br/>- Permite que uma condição de correspondência seja satisfeita sempre que o valor especificado corresponda a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cordas: <br/><br/>/negócios/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/negócio/modelo. **ma**p<br /><br />- Permite que um personagem especial seja especificado como um personagem literal. Por exemplo, pode especificar um personagem de espaço literal, encerrando um personagem espacial dentro de um conjunto de citações individuais (ou seja, `' '` ou `'sample value'`).<br/>- Permite especificar um valor em branco. Especifique um valor em branco especificando um conjunto de cotações individuais (isto é, '').<br /><br/>**Importante:**<br/>- Se o valor especificado não contiver um wildcard, então é automaticamente considerado um valor literal, o que significa que não é necessário especificar um conjunto de cotações únicas.<br/>- Se um backslash não escapar a outro carácter nesta tabela, é ignorado quando é especificado num conjunto de citações únicas.<br/>- Outra forma de especificar um personagem especial como personagem literal `\`é escapar-lhe usando um backslash (isto é, ).

### <a name="regular-expressions"></a>Expressões regulares

Expressões regulares definem um padrão que é procurado dentro de um valor de texto. Notação de expressão regular define significados específicos para uma variedade de símbolos. A tabela seguinte indica como os caracteres especiais são tratados pelas condições de jogo e características que suportam expressões regulares.

Caráter Especial | Descrição
------------------|------------
\ | Um backslash escapa ao personagem o que o segue, o que faz com que esse personagem seja tratado como um valor literal em vez de assumir o seu significado de expressão regular. Por exemplo, a seguinte sintaxe escapa a um asterisco:`\*`
% | O significado de um símbolo percentual depende da sua utilização.<br/><br/> `%{HTTPVariable}`: Esta sintaxe identifica uma variável HTTP.<br/>`%{HTTPVariable%Pattern}`: Esta sintaxe utiliza um símbolo percentual para identificar uma variável HTTP e como delimitador.<br />`\%`: Escapar de um símbolo percentual permite que seja utilizado como um valor `\%20`literal ou indicar codificação de URL (por exemplo, ).
\* | Um asterisco permite que o personagem anterior seja igualado zero ou mais vezes.
Espaço | Um personagem espacial é tipicamente tratado como um personagem literal.
'valor' | As citações individuais são tratadas como caracteres literais. Um conjunto de citações individuais não tem um significado especial.

As condições e características de correspondência que suportam expressões regulares aceitam padrões definidos por Expressões Regulares Compatíveis perl (PCRE).

## <a name="next-steps"></a>Passos seguintes

- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Sobrepor o comportamento do HTTP usando o motor de regras](cdn-verizon-premium-rules-engine.md)
- [Visão geral do CDN azure](cdn-overview.md)
