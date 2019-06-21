---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 518c57bc3327511b70deef143826f2a1b9df8639
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183929"
---
Antes de atribuir uma função RBAC para uma entidade de segurança, determine o âmbito de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que sempre é melhor conceder apenas o âmbito mais estreita possível.

A lista seguinte descreve os níveis em que pode definir o âmbito acesso aos recursos de BLOBs e filas do Azure, começando com o âmbito mais estreita:

- **Um contentor individual.** Este âmbito, uma atribuição de função se aplica a todos os blobs no contentor, bem como as propriedades do contentor e metadados.
- **Uma fila individual.** Este âmbito, uma atribuição de função aplica-se para mensagens na fila, bem como propriedades de fila e metadados.
- **A conta de armazenamento.** Este âmbito, uma atribuição de função aplica-se a todos os contentores e respetivos blobs ou para todas as filas e suas mensagens.
- **O grupo de recursos.** Este âmbito, uma atribuição de função aplica-se a todos os contentores ou filas em todas as contas de armazenamento no grupo de recursos.
- **A subscrição.** Este âmbito, uma atribuição de função aplica-se a todos os contentores ou filas em todas as contas de armazenamento em todos os grupos de recursos na subscrição.

> [!IMPORTANT]
> Se a sua subscrição inclui um espaço de nomes do Azure DataBricks, funções atribuídas no âmbito da subscrição serão impedidas de conceder acesso a dados de BLOBs e filas.
