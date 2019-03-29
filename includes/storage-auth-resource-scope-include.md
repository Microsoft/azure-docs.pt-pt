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
ms.openlocfilehash: a50eb45291ada23f55057f3c440c5b8b23cc4bce
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622642"
---
Antes de atribuir uma função RBAC para uma entidade de segurança, determine o âmbito de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que sempre é melhor conceder apenas o âmbito mais estreita possível.

A lista seguinte descreve os níveis em que pode definir o âmbito acesso aos recursos de BLOBs e filas do Azure, começando com o âmbito mais estreita:

- **Um contentor individual.** Este âmbito, uma entidade de segurança tem acesso a todos os blobs no contentor, bem como as propriedades do contentor e metadados.
- **Uma fila individual.** Este âmbito, uma entidade de segurança tem acesso a mensagens em fila, bem como propriedades de fila e metadados.
- **A conta de armazenamento.** Este âmbito, uma entidade de segurança tem acesso a todos os contentores e respetivos blobs ou para todas as filas e suas mensagens.
- **O grupo de recursos.** Este âmbito, uma entidade de segurança tem acesso a todos os contentores ou filas em todas as contas de armazenamento no grupo de recursos.
- **A subscrição.** Este âmbito, uma entidade de segurança tem acesso a todos os contentores ou filas em todas as contas de armazenamento em todos os grupos de recursos na subscrição.

> [!IMPORTANT]
> Se a sua subscrição inclui um espaço de nomes do Azure DataBricks, funções atribuídas no âmbito da subscrição serão impedidas de conceder acesso a dados de BLOBs e filas.