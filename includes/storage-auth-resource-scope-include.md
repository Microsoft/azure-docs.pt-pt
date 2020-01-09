---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460519"
---
Antes de atribuir uma função de RBAC a uma entidade de segurança, determine o escopo de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que é sempre melhor conceder apenas o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode fazer o escopo de acesso a recursos de BLOB e fila do Azure, começando com o escopo mais estreito:

- **Um contêiner individual.** Nesse escopo, uma atribuição de função se aplica a todos os BLOBs no contêiner, bem como propriedades e metadados do contêiner.
- **Uma fila individual.** Nesse escopo, uma atribuição de função se aplica a mensagens na fila, bem como propriedades de fila e metadados.
- **A conta de armazenamento.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres e seus BLOBs, ou a todas as filas e suas mensagens.
- **O grupo de recursos.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento no grupo de recursos.
- **A assinatura.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento em todos os grupos de recursos na assinatura.

> [!IMPORTANT]
> Se sua assinatura incluir um namespace do Azure databricks, as funções que têm o escopo para a assinatura não concederão acesso aos dados de BLOB e de fila. Em vez disso, as funções de escopo para o grupo de recursos, a conta de armazenamento ou o contêiner ou a fila.     
