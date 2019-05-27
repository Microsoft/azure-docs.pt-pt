---
title: incluir ficheiro
description: incluir ficheiro
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 57cec39bde460c6079091490acf541761c61e003
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119287"
---
Existe um número máximo de cada tipo de objeto do Azure Policy. Uma entrada de _Âmbito_ significa a subscrição ou o [grupo de gestão](../articles/governance/management-groups/overview.md).

| Onde | O quê | Contagem máxima |
|---|---|---|
| Scope | Definições de política | 250 |
| Scope | Definições de iniciativa | 100 |
| Inquilino | Definições de iniciativa | 1,000 |
| Scope | Atribuições de política ou iniciativa | 100 |
| Definição de política | Parâmetros | 20 |
| Definição de iniciativa | Políticas | 100 |
| Definição de iniciativa | Parâmetros | 100 |
| Atribuições de política ou iniciativa | Exclusões (notScopes) | 250 |
| Regra de política | Condicionais aninhadas | 512 |
