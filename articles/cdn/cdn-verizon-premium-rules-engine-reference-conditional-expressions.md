---
title: Expressões condicionais para o mecanismo de regras da CDN do Azure-Verizon Premium
description: A documentação de referência para a CDN do Azure do mecanismo de regras do Verizon Premium combina condições e recursos.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: d28bda28894477845c2a050666b3b4dd332b7d50
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082962"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Expressões condicionais do mecanismo de regras do Azure CDN do Verizon Premium

Este artigo lista descrições detalhadas das expressões condicionais para o [mecanismo de regras](cdn-verizon-premium-rules-engine.md)da CDN (rede de distribuição de conteúdo) do Azure.

A primeira parte de uma regra é a expressão condicional.

Expressão condicional | Descrição
-----------------------|-------------
QUE | Uma expressão IF sempre faz parte da primeira instrução em uma regra. Assim como todas as outras expressões condicionais, essa instrução IF deve ser associada a uma correspondência. Se nenhuma expressão condicional adicional for definida, essa correspondência determinará o critério que deve ser atendido antes que um conjunto de recursos possa ser aplicado a uma solicitação.
E SE | Uma expressão AND IF só pode ser adicionada após os seguintes tipos de expressões condicionais: IF e IF. Isso indica que há outra condição que deve ser atendida para a instrução IF inicial.
CASO CONTRÁRIO, SE| Uma expressão ELSE IF especifica uma condição alternativa que deve ser atendida antes de um conjunto de recursos específicos para essa instrução IF ocorrer. A presença de uma instrução ELSE IF indica o final da instrução anterior. A única expressão condicional que pode ser colocada após uma instrução ELSE IF é outra instrução ELSE IF. Isso significa que uma instrução ELSE IF só pode ser usada para especificar uma única condição adicional que deve ser atendida.

**Exemplo**: ![condição de correspondência da CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Uma regra subsequente pode substituir as ações especificadas por uma regra anterior.
   > Exemplo: uma regra catch-all protege todas as solicitações por meio da autenticação baseada em token. Outra regra pode ser criada diretamente abaixo dela para fazer uma exceção para determinados tipos de solicitações.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-verizon-premium-rules-engine.md)