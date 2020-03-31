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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460519"
---
Antes de atribuir uma função RBAC a um diretor de segurança, determine o alcance de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o âmbito mais estreito possível.

A lista seguinte descreve os níveis a que pode aceder ao blob Azure e aos recursos de fila, começando pelo âmbito mais restrito:

- **Um recipiente individual.** Neste âmbito, uma atribuição de funções aplica-se a todas as bolhas do contentor, bem como propriedades de contentores e metadados.
- **Uma fila individual.** Neste âmbito, uma atribuição de funções aplica-se a mensagens na fila, bem como propriedades de fila e metadados.
- **A conta de armazenamento.** Neste âmbito, uma atribuição de funções aplica-se a todos os contentores e suas bolhas, ou a todas as filas e mensagens.
- **o grupo de recursos.** Neste âmbito, uma atribuição de funções aplica-se a todos os contentores ou filas em todas as contas de armazenamento do grupo de recursos.
- **A assinatura.** Neste âmbito, uma atribuição de funções aplica-se a todos os contentores ou filas em todas as contas de armazenamento em todos os grupos de recursos da subscrição.

> [!IMPORTANT]
> Se a sua subscrição incluir um espaço de nome Azure DataBricks, as funções que são consultadas à subscrição não permitirão o acesso aos dados blob e fila. As funções de âmbito para o grupo de recursos, conta de armazenamento, ou contentor ou fila.     
