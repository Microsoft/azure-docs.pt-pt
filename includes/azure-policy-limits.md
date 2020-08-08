---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: 08183cc2cbe2fce83bc7347da22308931b99dcc1
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88011001"
---
Há uma contagem máxima para cada tipo de objeto para Azure Policy. Uma entrada de _Âmbito_ significa a subscrição ou o [grupo de gestão](../articles/governance/management-groups/overview.md).

| Onde | O quê | Contagem máxima |
|---|---|---|
| Âmbito | Definições de política | 500 |
| Âmbito | Definições de iniciativa | 200 |
| Inquilino | Definições de iniciativa | 2.500 |
| Âmbito | Atribuições de política ou iniciativa | 200 |
| Definição de política | Parâmetros | 20 |
| Definição de iniciativa | Políticas | 1000 |
| Definição de iniciativa | Parâmetros | 100 |
| Atribuições de política ou iniciativa | Exclusões (notScopes) | 400 |
| Regra política | Condições aninhadas | 512 |
| Tarefa de reparação | Recursos | 500 |
