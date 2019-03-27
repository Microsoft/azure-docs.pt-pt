---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ad4b244b58d741ad45463297df5bd358f3ae9918
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450026"
---
Antes de atribuir uma função RBAC para uma entidade de segurança, determine o âmbito de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que sempre é melhor conceder apenas o âmbito mais estreita possível.

A lista seguinte descreve os níveis em que pode definir o âmbito acesso aos recursos de BLOBs e filas do Azure, começando com o âmbito mais estreita:

- **Um contentor individual.** Este âmbito, uma entidade de segurança tem acesso a todos os blobs no contentor, bem como as propriedades do contentor e metadados.
- **Uma fila individual.** Este âmbito, uma entidade de segurança tem acesso a mensagens em fila, bem como propriedades de fila e metadados.
- **A conta de armazenamento.** Este âmbito, uma entidade de segurança tem acesso a todos os contentores e respetivos blobs ou para todas as filas e suas mensagens.
- **O grupo de recursos.** Este âmbito, uma entidade de segurança tem acesso a todos os contentores ou filas em todas as contas de armazenamento no grupo de recursos.
- **A subscrição.** Este âmbito, uma entidade de segurança tem acesso a todos os contentores ou filas em todas as contas de armazenamento em todos os grupos de recursos na subscrição.
