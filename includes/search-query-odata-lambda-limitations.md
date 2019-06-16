---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079642"
---
| Tipo de dados | Funcionalidades permitidas em expressões lambda com `any` | Funcionalidades permitidas em expressões lambda com `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Tudo, exceto `search.ismatch` e `search.ismatchscoring` | Mesmo |
| `Collection(Edm.String)` | Comparações com `eq` ou `search.in` <br/><br/> Combinando as subexpressões com `or` | Comparações com `ne` ou `not search.in()` <br/><br/> Combinando as subexpressões com `and` |
| `Collection(Edm.Boolean)` | Comparações com `eq` ou `ne` | Mesmo |
| `Collection(Edm.GeographyPoint)` | Usando `geo.distance` com `lt` ou `le` <br/><br/> `geo.intersects` <br/><br/> Combinando as subexpressões com `or` | Usando `geo.distance` com `gt` ou `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Combinando as subexpressões com `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Comparações usando `eq`, `ne`, `lt`, `gt`, `le`, ou `ge` <br/><br/> A combinação de comparações com outras as subexpressões usando `or` <br/><br/> A combinação de comparações, exceto `ne` com outras as subexpressões usando `and` <br/><br/> Expressões através de combinações de `and` e `or` no [Disjunctive Normal formulário (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Comparações usando `eq`, `ne`, `lt`, `gt`, `le`, ou `ge` <br/><br/> A combinação de comparações com outras as subexpressões usando `and` <br/><br/> A combinação de comparações, exceto `eq` com outras as subexpressões usando `or` <br/><br/> Expressões através de combinações de `and` e `or` no [Conjunctive Normal formulário (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
