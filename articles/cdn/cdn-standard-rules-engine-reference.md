---
title: Regras padrão referência do motor para Azure CDN [ Microsoft Docs
description: Documentação de referência para condições de correspondência e ações no motor de regras Standard para a Rede de Entrega de Conteúdos Azure (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: aa401150ee7a0f02e809ad702b8247e18081c8a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171569"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Regras padrão referência do motor para Azure CDN

No [motor standard regras](cdn-standard-rules-engine.md) para a Rede de Entrega de Conteúdos Azure (Azure CDN), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições e características da correspondência que estão disponíveis no motor standard regras para O CDN Azure.

O motor de regras foi concebido para ser a autoridade final sobre como tipos específicos de pedidos são processados pela Standard Azure CDN.

**Utilizações comuns para as regras:**

- Anular ou definir uma política de cache personalizada.
- Redirecionamento de pedidos.
- Modificar os cabeçalhos de pedido e resposta http.

## <a name="terminology"></a>Terminologia

Para definir uma regra no motor de regras, defina condições e [ações](cdn-standard-rules-engine-actions.md) [de correspondência:](cdn-standard-rules-engine-match-conditions.md)

 ![Estrutura de regras do CDN Azure](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Cada regra pode ter até quatro condições de jogo e três ações. Cada ponto final do Azure CDN pode ter até cinco regras. 

Incluído no atual limite de cinco regras para um ponto final de CDN Azure é uma *regra global*padrão . A regra global não tem condições de correspondência, e as ações que são definidas numa regra global sempre desencadeiam.

## <a name="syntax"></a>Sintaxe

A forma como os caracteres especiais são tratados numa regra varia com base na forma como diferentes condições de correspondência e ações lidam com valores de texto. Uma condição ou ação de correspondência pode interpretar texto de uma das seguintes formas:

- [Valores literais](#literal-values)
- [Valores wildcard](#wildcard-values)


### <a name="literal-values"></a>Valores literais

O texto que é interpretado como um valor literal trata todos os caracteres especiais exceto *o símbolo %* como parte do valor que deve ser igualado numa regra. Por exemplo, uma condição `'*'` de correspondência literal definida `'*'` só é satisfeita quando o valor exato é encontrado.

Um sinal por cento é usado para `%20`indicar codificação de URL (por exemplo, ).

### <a name="wildcard-values"></a>Valores wildcard

Texto que é interpretado como um valor wildcard atribui significado adicional a caracteres especiais. A tabela que se segue descreve como caracteres especiais específicos são interpretados no motor de regras Standard:

Caráter | Descrição
----------|------------
\ | Um backslash é usado para escapar de qualquer um dos caracteres especificados nesta tabela. Uma barra de costas deve ser especificada diretamente antes do carácter especial que deve ser escapado. Por exemplo, a seguinte sintaxe escapa a um asterisco:`\*`
% | Um sinal por cento é usado para `%20`indicar codificação de URL (por exemplo, ).
\* | Um asterisco é um wildcard que representa um ou mais caracteres.
espaço | Um caracteres espaciais indica que uma condição de correspondência pode ser satisfeita por qualquer um dos valores ou padrões especificados.
únicas aspas | Uma única marca de citação não tem um significado especial. No entanto, um conjunto de marcas únicas indica que um valor deve ser tratado como um valor literal. As aspas únicas podem ser utilizadas das seguintes formas:<ul><li>Para permitir que uma condição de jogo seja satisfeita sempre que o valor especificado corresponda a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cordas: <ul><li>/negócios/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/negócio/modelo. **ma**p</li></ul><li>Para permitir que um personagem especial seja especificado como um personagem literal. Por exemplo, pode especificar um personagem de espaço literal, encerrando um`' '` `'<sample value>'`personagem espacial num conjunto de únicas marcas de aspas (ou).</li><li>Para permitir especificar um valor em branco. Especifique um valor em branco especificando um conjunto de marcas únicas **(»).**</li></ul>**Importante:**<br /><ul><li>Se o valor especificado não contiver um wildcard, o valor é automaticamente considerado um valor literal. Não é necessário especificar um conjunto de aspas únicas para um valor literal.</li><li>Se um backslash não é usado para escapar a outro personagem nesta mesa, o backslash é ignorado quando é especificado num conjunto de aspas únicas.</li><li>Outra forma de especificar um personagem especial como um personagem`\`literal é escapar-lhe usando um backslash ( ).</li></ul>

## <a name="next-steps"></a>Passos seguintes

- [Condições de jogo no motor de regras Standard](cdn-standard-rules-engine-match-conditions.md)
- [Ações no motor standard regras](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS com o Motor de regras standard](cdn-standard-rules-engine.md)
- [Visão geral do CDN azure](cdn-overview.md)
