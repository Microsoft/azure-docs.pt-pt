---
title: Expressões condicionais para Azure CDN - Motor de regras Verizon Premium
description: Documentação de referência para O CDN Azure da Verizon Premium regras condições e características do motor.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 25b3c1a011e39adea651a6daa1d4ab9aa3a9d186
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253514"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN da Verizon Premium governa expressões condicionais do motor

Este artigo enumera descrições detalhadas do Motor de [Regras](cdn-verizon-premium-rules-engine.md)da Rede de Entrega de Conteúdos Azure (CDN).

A primeira parte de uma regra é a Expressão Condicional.

Expressão Condicional | Descrição
-----------------------|-------------
IF | Uma expressão IF é sempre parte da primeira declaração de uma regra. Como todas as outras expressões condicionais, esta declaração if deve ser associada a uma correspondência. Se não forem definidas expressões condicionais adicionais, esta partida determina o critério que deve ser cumprido antes de um conjunto de características poder ser aplicado a um pedido.
E SE | Uma expressão e se só pode ser adicionada após os seguintes tipos de expressões condicional:IF,AND IF. Indica que há outra condição que deve ser satisfeita para a declaração inicial de IF.
OUTRA SE| Uma expressão ELSE IF especifica uma condição alternativa que deve ser satisfeita antes de um conjunto de características específicas desta declaração DO ELSE IF. A presença de uma declaração DO IF indica o fim da declaração anterior. A única expressão condicional que pode ser colocada após uma declaração do ELSE IF é outra declaração DO ELSE IF. Isto significa que uma declaração DO ELSE IF só pode ser utilizada para especificar uma única condição adicional que deve ser satisfeita.

**Exemplo** ![: Condição de correspondência do CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Uma regra subsequente pode anular as ações especificadas por uma regra anterior.
   > Exemplo: Uma regra catch-all assegura todos os pedidos através da Autenticação Baseada em Token. Outra regra pode ser criada diretamente abaixo dela para abrir uma exceção para certos tipos de pedidos.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do CDN azure](cdn-overview.md)
- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Sobrepor o comportamento do HTTP usando o motor de regras](cdn-verizon-premium-rules-engine.md)