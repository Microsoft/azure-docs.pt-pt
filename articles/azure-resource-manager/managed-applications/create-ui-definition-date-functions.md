---
title: Criar funções de data de definição de UI
description: Descreve as funções a utilizar ao trabalhar com valores de data.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098191"
---
# <a name="createuidefinition-date-functions"></a>Funções de data createUiDefinition

As funções a utilizar ao trabalhar com valores de data.

## <a name="addhours"></a>addHours

Adiciona um número integral de horas à hora de tempo especificada.

O seguinte exemplo `"1991-01-01T00:59:59.000Z"` regressa:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

Adiciona um número integral de minutos à estada de tempo especificada.

O seguinte exemplo `"1991-01-01T00:00:59.000Z"` regressa:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
Adiciona um número integral de segundos à estada de tempo especificada.

O seguinte exemplo `"1991-01-01T00:00:00.000Z"` regressa:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

Devolve uma cadeia no formato ISO 8601 da data e hora atuais no computador local.

O seguinte exemplo poderia `"1990-12-31T23:59:59.000Z"` voltar:

```json
"[utcNow()]"
```

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução ao Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](../management/overview.md).
