---
title: Regras padrão referência do motor para Azure CDN / Microsoft Docs
description: Documentação de referência para condições de correspondência e ações no motor de regras standard para Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: b272426f865636640e0a2fafde46cbebbe6eb363
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327498"
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
- Pedidos Processados: $0,60 por milhão de pedidos
- As primeiras 5 regras permanecerão livres

## <a name="syntax"></a>Syntax

A forma como os caracteres especiais são tratados numa regra varia com base na forma como diferentes condições de correspondência e ações lidam com valores de texto. Uma condição ou ação de correspondência pode interpretar texto de uma das seguintes formas:

- [Valores literais](#literal-values)
- [Valores wildcard](#wildcard-values)


### <a name="literal-values"></a>Valores literais

Texto que é interpretado como um valor literal trata todos os caracteres *especiais, exceto o símbolo %* como parte do valor que deve ser combinado em uma regra. Por exemplo, uma condição de correspondência literal definida `'*'` só é satisfeita quando o valor exato é `'*'` encontrado.

Um sinal por cento é usado para indicar codificação de URL (por exemplo, `%20` ).

### <a name="wildcard-values"></a>Valores wildcard

Atualmente apoiamos o personagem wildcard na Condição de **Jogo urlPath** no motor de regras padrão. O \* personagem é um wildcard que representa um ou mais caracteres. 

## <a name="next-steps"></a>Passos seguintes

- [Condições de jogo no motor de regras standard](cdn-standard-rules-engine-match-conditions.md)
- [Ações no motor de regras Standard](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS com o Motor de regras standard](cdn-standard-rules-engine.md)
- [Visão geral do Azure CDN](cdn-overview.md)
