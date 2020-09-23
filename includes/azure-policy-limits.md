---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/22/2020
ms.author: dacoulte
ms.openlocfilehash: 2707523947cae77dd800dd5f560ebe1c47f3893a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905974"
---
Há uma contagem máxima para cada tipo de objeto para Azure Policy. Uma entrada de _Âmbito_ significa a subscrição ou o [grupo de gestão](../articles/governance/management-groups/overview.md).

| Onde | O quê | Contagem máxima |
|---|---|---|
| Âmbito | Definições de política | 500 |
| Âmbito | Definições de iniciativa | 200 |
| Inquilino | Definições de iniciativa | 2.500 |
| Âmbito | Atribuições de política ou iniciativa | 200 |
| Âmbito | Isenções | 1000 |
| Definição de política | Parâmetros | 20 |
| Definição de iniciativa | Políticas | 1000 |
| Definição de iniciativa | Parâmetros | 100 |
| Atribuições de política ou iniciativa | Exclusões (notScopes) | 400 |
| Regra política | Condições aninhadas | 512 |
| Tarefa de reparação | Recursos | 500 |
