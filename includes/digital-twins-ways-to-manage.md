---
author: baanders
description: incluir arquivo para Azure Digital Twins - formas de gerir caso
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 1f66101fc1231be2e5ce36dc348b1ca850113867
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303769"
---
Este artigo destaca como completar diferentes operações de gestão utilizando as [Gémeas Digitais Azure .NET (C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management). Também pode criar essas mesmas chamadas de gestão utilizando os outros SDKs de língua descritos em [*Como-a-: Use as APIs e SDKs de gémeos digitais Azure*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Lembre-se que todos os métodos SDK vêm em versões sincronizadas e assíncronos. Para chamadas de paging, os métodos async regressam `AsyncPageable<T>` enquanto as versões sincronizadas regressam `Pageable<T>` .

Outra opção de gestão é chamar as [**APIs de REPOUSO**](/rest/api/azure-digitaltwins/) Azure Digital Twins para esta área de tópico diretamente, através de um cliente REST como o Carteiro. Para obter instruções sobre como [*fazê-lo, consulte Como fazer: Faça pedidos com o Carteiro*](../articles/digital-twins/how-to-use-postman.md).

Finalmente, pode completar as mesmas operações de gestão utilizando o **CLI** das Gémeas Digitais Azure. Para saber mais sobre a utilização do CLI, consulte [*Como-a-fazer: Use o CLI das Gémeas Digitais Azure*](../articles/digital-twins/how-to-use-cli.md).