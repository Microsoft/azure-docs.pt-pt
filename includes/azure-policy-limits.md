---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: e9faea1d5913a19dfdeff662e26992529dc1b22d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84466956"
---
Há uma contagem máxima para cada tipo de objeto para Azure Policy. Uma entrada de _Âmbito_ significa a subscrição ou o [grupo de gestão](../articles/governance/management-groups/overview.md).

| Onde | O quê | Contagem máxima |
|---|---|---|
| Âmbito | Definições de política | 500 |
| Âmbito | Definições de iniciativa | 100 |
| Inquilino | Definições de iniciativa | 2.500 |
| Âmbito | Atribuições de política ou iniciativa | 100 |
| Definição de política | Parâmetros | 20 |
| Definição de iniciativa | Políticas | 100 |
| Definição de iniciativa | Parâmetros | 100 |
| Atribuições de política ou iniciativa | Exclusões (notScopes) | 400 |
| Regra política | Condições aninhadas | 512 |
| Tarefa de reparação | Recursos | 500 |
