---
title: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/10/2021
ms.author: tamram
ms.openlocfilehash: 483f5853c321eee4ac6d10543f0e360a0a5e54b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373760"
---
Antes de atribuir um papel de Azure RBAC a um diretor de segurança, determine o âmbito de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o âmbito mais estreito possível. As funções de Azure RBAC definidas num âmbito mais amplo são herdadas pelos recursos abaixo deles.

A lista a seguir descreve os níveis em que pode estender o acesso aos recursos de bolha e fila Azure, começando pelo âmbito mais estreito:

- **Um recipiente individual.** Neste âmbito, uma atribuição de funções aplica-se a todas as bolhas do recipiente, bem como propriedades de contentores e metadados.
- **Uma fila individual.** Neste âmbito, uma atribuição de funções aplica-se a mensagens na fila, bem como propriedades de fila e metadados.
- **A conta de armazenamento.** Neste âmbito, uma atribuição de funções aplica-se a todos os contentores e suas bolhas, ou a todas as filas e suas mensagens.
- **o grupo de recursos.** Neste âmbito, uma atribuição de funções aplica-se a todos os contentores ou filas em todas as contas de armazenamento do grupo de recursos.
- **A assinatura.** Neste âmbito, uma atribuição de funções aplica-se a todos os contentores ou filas em todas as contas de armazenamento em todos os grupos de recursos da subscrição.
- **Um grupo de gestão.** Neste âmbito, uma atribuição de funções aplica-se a todos os contentores ou filas em todas as contas de armazenamento em todos os grupos de recursos em todas as subscrições do grupo de gestão.

Para obter mais informações sobre atribuições e âmbito de funções Azure, consulte [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../articles/role-based-access-control/overview.md)
