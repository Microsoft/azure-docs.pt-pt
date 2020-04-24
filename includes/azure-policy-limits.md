---
title: incluir ficheiro
description: incluir ficheiro
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: 04e607296478520298a2febab61a7edac911a59c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131992"
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
| Tarefa de reparação | Recursos | 500 |
