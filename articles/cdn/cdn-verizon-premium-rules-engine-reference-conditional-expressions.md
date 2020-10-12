---
title: Expressões condicionais para Azure CDN - Verizon Premium regras motor
description: Documentação de referência para Azure CDN da Verizon Premium regras condições e funcionalidades de correspondência do motor.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 25b3c1a011e39adea651a6daa1d4ab9aa3a9d186
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81253514"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN da Verizon Premium regras expressões condicionais do motor

Este artigo enumera descrições detalhadas das Expressões Condicionais para o [Motor](cdn-verizon-premium-rules-engine.md)de Regras da Rede de Entrega de Conteúdos Azure (CDN).

A primeira parte de uma regra é a Expressão Condicional.

Expressão Condicional | Descrição
-----------------------|-------------
IF | Uma expressão IF é sempre uma parte da primeira declaração de uma regra. Como todas as outras expressões condicionais, esta declaração IF deve ser associada a uma correspondência. Se não forem definidas expressões condicionais adicionais, esta partida determina o critério que deve ser cumprido antes de um conjunto de funcionalidades poder ser aplicado a um pedido.
E SE | Uma expressão E IF só pode ser adicionada após os seguintes tipos de expressões condicionais:IF,E IF. Indica que há outra condição que deve ser cumprida para a declaração inicial do IF.
SENÃO SE| Uma expressão MAIS SE especifica uma condição alternativa que deve ser satisfeita antes de um conjunto de características específicas a esta declaração IF MAIS ocorre. A presença de uma declaração IF INDICA O Fim da Declaração Anterior. A única expressão condicional que pode ser colocada após uma declaração IF ELSE é outra declaração IF. Isto significa que uma declaração IF ELSE só pode ser usada para especificar uma única condição adicional que tem de ser cumprida.

**Exemplo**: ![ Condição de correspondência CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Uma regra subsequente pode anular as ações especificadas por uma regra anterior.
   > Exemplo: Uma regra catch-all protege todos os pedidos através Token-Based Autenticação. Outra regra pode ser criada diretamente abaixo dela para abrir uma exceção para certos tipos de pedidos.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do Azure CDN](cdn-overview.md)
- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Regras condições de correspondência do motor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Comportamento HTTP predefinido que predefinindo usando o motor de regras](cdn-verizon-premium-rules-engine.md)