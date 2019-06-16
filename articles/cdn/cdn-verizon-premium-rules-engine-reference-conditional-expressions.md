---
title: Expressões condicionais do motor de regras de CDN do Azure da Verizon Premium | Documentos da Microsoft
description: Funcionalidades e as condições de correspondência do motor de regras de documentação de referência para a CDN do Azure da Verizon Premium.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: c6e49e6fbc0c541ce9a8cd903eb313d61413257c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481538"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>CDN do Azure de expressões condicionais do motor de regras de Premium da Verizon

Este artigo apresenta uma lista de descrições detalhadas das expressões condicionais para a rede de entrega de conteúdos (CDN) do Azure [motor de regras](cdn-verizon-premium-rules-engine.md).

A primeira parte de uma regra é a expressão condicional.

Expressão condicional | Descrição
-----------------------|-------------
IF | Uma expressão IF é sempre uma parte da primeira instrução numa regra. Como todas as outras expressões condicionais, essa instrução IF tem de ser associada a uma correspondência. Se nenhum expressões condicionais adicionais são definidas, essa correspondência determina o critério que têm de ser cumprido antes de um conjunto de recursos pode ser aplicado a um pedido.
AND IF | Uma expressão IF e só pode ser adicionada após os seguintes tipos de expressões: se, e se do condicional. Ele indica que existe outra condição de que tem de ser cumprida para a instrução IF inicial.
ELSE IF| Uma expressão ou se especifica uma condição de alternativa que têm de ser cumprida antes de um conjunto de funcionalidades específicas a esta declaração ou se maior. A presença de uma instrução ou se indica o fim da instrução anterior. A expressão condicional apenas que pode ser colocada após uma instrução ou se é outra instrução ou se. Isso significa que uma instrução ou se só pode ser utilizada para especificar uma única condição adicional que tem de ser cumpridos.

**Exemplo**: ![Condição de correspondência CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Uma regra subsequente pode substituir as ações especificadas por uma regra anterior.
   > Exemplo: Uma regra abrangente protege todos os pedidos através da autenticação baseada em tokens. Outra regra pode ser criada diretamente abaixo da mesma para realizar uma exceção para determinados tipos de pedidos.

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral da CDN do Azure](cdn-overview.md)
- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Substituir o comportamento HTTP padrão usando o mecanismo de regras](cdn-verizon-premium-rules-engine.md)