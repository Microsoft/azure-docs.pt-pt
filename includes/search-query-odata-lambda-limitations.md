---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80272626"
---
| Tipo de dados | Características permitidas em expressões lambda com `any` | Características permitidas em expressões lambda com `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Tudo menos `search.ismatch` e `search.ismatchscoring` | Mesma |
| `Collection(Edm.String)` | Comparações com `eq` ou `search.in` <br/><br/> Combinando subexpressões com `or` | Comparações com `ne` ou `not search.in()` <br/><br/> Combinando subexpressões com `and` |
| `Collection(Edm.Boolean)` | Comparações com `eq` ou `ne` | Mesma |
| `Collection(Edm.GeographyPoint)` | Usando `geo.distance` com `lt` ou `le` <br/><br/> `geo.intersects` <br/><br/> Combinando subexpressões com `or` | Usando `geo.distance` com `gt` ou `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Combinando subexpressões com `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Comparações `eq` `ne` utilizando, `lt` , , , `gt` , `le` ou `ge` <br/><br/> Combinando comparações com outras subexpressões usando `or` <br/><br/> Combinação de comparações, exceto `ne` com outras subexpressões que utilizam `and` <br/><br/> Expressões utilizando combinações de `and` e em formulário normal `or` [disjuntivo (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Comparações `eq` `ne` utilizando, `lt` , , , `gt` , `le` ou `ge` <br/><br/> Combinando comparações com outras subexpressões usando `and` <br/><br/> Combinação de comparações, exceto `eq` com outras subexpressões que utilizam `or` <br/><br/> Expressões utilizando combinações de `and` e em formulário normal `or` [conjuntivo (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
