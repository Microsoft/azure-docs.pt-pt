---
title: ficheiro de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: 96d8100f2cffcfb001a693575128ce19e742225d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87534142"
---
Antes de atribuir um papel de Azure a um diretor de segurança, determine o âmbito de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o âmbito mais estreito possível.

A lista a seguir descreve os níveis em que pode estender o acesso aos recursos de bolha e fila Azure, começando pelo âmbito mais estreito:

- **Um recipiente individual.** Neste âmbito, uma atribuição de funções aplica-se a todas as bolhas do recipiente, bem como propriedades de contentores e metadados.
- **Uma fila individual.** Neste âmbito, uma atribuição de funções aplica-se a mensagens na fila, bem como propriedades de fila e metadados.
- **A conta de armazenamento.** Neste âmbito, uma atribuição de funções aplica-se a todos os contentores e suas bolhas, ou a todas as filas e suas mensagens.
- **o grupo de recursos.** Neste âmbito, uma atribuição de funções aplica-se a todos os contentores ou filas em todas as contas de armazenamento do grupo de recursos.
- **A assinatura.** Neste âmbito, uma atribuição de funções aplica-se a todos os contentores ou filas em todas as contas de armazenamento em todos os grupos de recursos da subscrição.
- **Um grupo de gestão.** Neste âmbito, uma atribuição de funções aplica-se a todos os contentores ou filas em todas as contas de armazenamento em todos os grupos de recursos em todas as subscrições do grupo de gestão.

Para obter mais informações sobre atribuições e âmbito de funções Azure, consulte [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../articles/role-based-access-control/overview.md)
