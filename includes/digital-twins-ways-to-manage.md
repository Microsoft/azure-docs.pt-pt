---
author: baanders
description: incluir arquivo para Azure Digital Twins - formas de gerir caso
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 887d185249f96b5d3be4aab6a96aa3c6c4a85690
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231417"
---
Este artigo destaca como completar diferentes operações de gestão utilizando as [Gémeas Digitais Azure .NET (C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true). Também pode criar essas mesmas chamadas de gestão utilizando os outros SDKs de língua descritos em [*Como-a-: Use as APIs e SDKs de gémeos digitais Azure*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Lembre-se que todos os métodos SDK vêm em versões sincronizadas e assíncronos. Para chamadas de paging, os métodos async regressam `AsyncPageable<T>` enquanto as versões sincronizadas regressam `Pageable<T>` .

Outra opção de gestão é chamar as [**APIs de REPOUSO**](/rest/api/azure-digitaltwins/) Azure Digital Twins para esta área de tópico diretamente, através de um cliente REST como o Carteiro. Para obter instruções sobre como [*fazê-lo, consulte Como fazer: Faça pedidos com o Carteiro*](../articles/digital-twins/how-to-use-postman.md).

Finalmente, pode completar as mesmas operações de gestão utilizando o **CLI** das Gémeas Digitais Azure. Para saber mais sobre a utilização do CLI, consulte [*Como-a-fazer: Use o CLI das Gémeas Digitais Azure*](../articles/digital-twins/how-to-use-cli.md).