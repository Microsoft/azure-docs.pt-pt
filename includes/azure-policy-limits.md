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
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554050"
---
Existe um número máximo de cada tipo de objeto do Azure Policy. Uma entrada de _Âmbito_ significa a subscrição ou o [grupo de gestão](../articles/governance/management-groups/overview.md).

| Onde | O quê | Contagem máxima |
|---|---|---|
| Âmbito | Definições de política | 250 |
| Âmbito | Definições de iniciativa | 100 |
| Inquilino | Definições de iniciativa | 1,000 |
| Âmbito | Atribuições de política ou iniciativa | 100 |
| Definição de política | Parâmetros | 20 |
| Definição de iniciativa | Políticas | 100 |
| Definição de iniciativa | Parâmetros | 100 |
| Atribuições de política ou iniciativa | Exclusões (notScopes) | 250 |
| Regra de política | Condicionais aninhadas | 512 |
