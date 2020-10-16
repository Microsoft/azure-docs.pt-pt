---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91606033"
---
Há uma contagem máxima para cada tipo de objeto para Azure Policy. Para definições, uma entrada de _Scope_ significa o grupo de [gestão](../articles/governance/management-groups/overview.md) ou subscrição.
Para atribuições e isenções, uma entrada de _Scope_ significa o grupo de [gestão,](../articles/governance/management-groups/overview.md)subscrição, grupo de recursos ou recurso individual.

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
