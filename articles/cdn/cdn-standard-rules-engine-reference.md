---
title: Regras padrão referência do motor para Azure CDN / Microsoft Docs
description: Documentação de referência para condições de correspondência e ações no motor de regras standard para Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/22/2020
ms.author: allensu
ms.openlocfilehash: 6260a4b78197329e020bebaa3bc08db5ad792086
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559302"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Referência do motor de regras standard para a CDN do Azure

No [motor de regras Standard](cdn-standard-rules-engine.md) para Azure Content Delivery Network (Azure CDN), uma regra consiste numa ou mais condições de correspondência e numa ação. Este artigo fornece descrições detalhadas das condições e funcionalidades de jogo que estão disponíveis no motor de regras Standard para Azure CDN.

O motor de regras foi concebido para ser a autoridade final sobre a forma como tipos específicos de pedidos são processados pela Standard Azure CDN.

**Utilizações comuns para as regras:**

- Sobrepor ou definir uma política de cache personalizada.
- Redirecione os pedidos.
- Modificar os cabeçalhos de pedido e resposta HTTP.

## <a name="terminology"></a>Terminologia

Para definir uma regra no motor de regras, defina condições e [ações de](cdn-standard-rules-engine-actions.md) [jogo:](cdn-standard-rules-engine-match-conditions.md)

 ![Estrutura de regras do Azure CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Cada regra pode ter até dez condições de jogo e cinco ações. Cada ponto final Azure CDN pode ter até 25 regras. 

Incluído neste limite é uma *regra global padrão.* A regra global não tem condições de jogo; ações que são definidas numa regra global sempre desencadeada.

## <a name="limits-and-pricing"></a>Limites e preços 

Cada ponto final Azure CDN pode ter até 25 regras. Cada regra pode ter até dez condições de jogo e cinco ações. Os preços do Motor de Regras seguem as dimensões abaixo: 
- Regras: $1 por regra por mês 
- Pedidos Processados: $0,60 por milhão de requets
- As primeiras 5 regras permanecerão livres

## <a name="syntax"></a>Syntax

A forma como os caracteres especiais são tratados numa regra varia com base na forma como diferentes condições de correspondência e ações lidam com valores de texto. Uma condição ou ação de correspondência pode interpretar texto de uma das seguintes formas:

- [Valores literais](#literal-values)
- [Valores wildcard](#wildcard-values)


### <a name="literal-values"></a>Valores literais

Texto que é interpretado como um valor literal trata todos os caracteres *especiais, exceto o símbolo %* como parte do valor que deve ser combinado em uma regra. Por exemplo, uma condição de correspondência literal definida `'*'` só é satisfeita quando o valor exato é `'*'` encontrado.

Um sinal por cento é usado para indicar codificação de URL (por exemplo, `%20` ).

### <a name="wildcard-values"></a>Valores wildcard

Texto que é interpretado como um valor wildcard atribui significado adicional a caracteres especiais. A tabela a seguir descreve como caracteres especiais específicos são interpretados no motor de regras Standard:

Caráter | Descrição
----------|------------
\ | Uma pestana é usada para escapar a qualquer um dos caracteres especificados nesta tabela. Uma pestana deve ser especificada diretamente antes do carácter especial que deve ser escapado. Por exemplo, a seguinte sintaxe escapa a um asterisco:`\*`
% | Um sinal por cento é usado para indicar codificação de URL (por exemplo, `%20` ).
\* | Um asterisco é um wildcard que representa um ou mais caracteres.
espaço | Um caractere espacial indica que uma condição de correspondência pode ser satisfeita por qualquer um dos valores ou padrões especificados.
aspas únicas | Uma única marca de citação não tem um significado especial. No entanto, um conjunto de aspas únicas indica que um valor deve ser tratado como um valor literal. As aspas individuais podem ser utilizadas das seguintes formas:<ul><li>Para permitir que uma condição de jogo seja satisfeita sempre que o valor especificado corresponda a qualquer parte do valor de comparação.  Por exemplo, `'ma'` corresponderia a qualquer uma das seguintes cordas: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template. **ma**p</li></ul><li>Para permitir que um personagem especial seja especificado como um personagem literal. Por exemplo, pode especificar um personagem de espaço literal, encerrando um personagem espacial num conjunto de aspas únicas `' '` (ou `'<sample value>'` ).</li><li>Para permitir a especificação de um valor em branco. Especificar um valor em branco especificando um conjunto de aspas únicas **('»**).</li></ul>**Importante:**<br /><ul><li>Se o valor especificado não contiver um wildcard, o valor é automaticamente considerado um valor literal. Não precisa especificar um conjunto de aspas únicas por um valor literal.</li><li>Se uma pestana não for usada para escapar de outro personagem nesta tabela, o retrocesso é ignorado quando é especificado num conjunto de aspas únicas.</li><li>Outra forma de especificar um personagem especial como personagem literal é escapar-lhe usando um backslash `\` ().</li></ul>

## <a name="next-steps"></a>Próximos passos

- [Condições de jogo no motor de regras standard](cdn-standard-rules-engine-match-conditions.md)
- [Ações no motor de regras Standard](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS com o Motor de regras standard](cdn-standard-rules-engine.md)
- [Visão geral do Azure CDN](cdn-overview.md)
