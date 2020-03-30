---
title: incluir ficheiro
description: incluir ficheiro
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/25/2020
ms.author: dacoulte
ms.openlocfilehash: 19b43c857058b3af50db3bc1b42ec1ae558ee884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272855"
---
Há uma contagem máxima para cada tipo de objeto para a Política Azure. Uma entrada de _Âmbito_ significa a subscrição ou o [grupo de gestão](../articles/governance/management-groups/overview.md).

| Onde | O quê | Contagem máxima |
|---|---|---|
| Âmbito | Definições de política | 500 |
| Âmbito | Definições de iniciativa | 100 |
| Inquilino | Definições de iniciativa | 1,000 |
| Âmbito | Atribuição de políticas ou iniciativas | 100 |
| Definição de política | Parâmetros | 20 |
| Definição de iniciativa | Políticas | 100 |
| Definição de iniciativa | Parâmetros | 100 |
| Atribuição de políticas ou iniciativas | Exclusões (notScopes) | 400 |
| Regra política | Condicionalismos aninhados | 512 |
| Tarefa de reparação | Recursos | 1000 |
