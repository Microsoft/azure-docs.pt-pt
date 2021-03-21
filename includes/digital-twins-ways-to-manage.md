---
author: baanders
description: incluir arquivo para Azure Digital Twins - formas de gerir caso
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 02f6c59a76a3fdb7bd4360570b29d7b40a1aff8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102473771"
---
Este artigo destaca como completar diferentes operações de gestão utilizando as [Gémeas Digitais Azure .NET (C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management). Também pode criar essas mesmas chamadas de gestão utilizando os outros SDKs de língua descritos em [*Como-a-: Use as APIs e SDKs de gémeos digitais Azure*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Lembre-se que todos os métodos SDK vêm em versões sincronizadas e assíncronos. Para chamadas de paging, os métodos async regressam `AsyncPageable<T>` enquanto as versões sincronizadas regressam `Pageable<T>` .

Outra opção de gestão é chamar as [**APIs de REPOUSO**](/rest/api/azure-digitaltwins/) Azure Digital Twins para esta área de tópico diretamente, através de um cliente REST como o Carteiro. Para obter instruções sobre como [*fazê-lo, consulte Como fazer: Faça pedidos com o Carteiro*](../articles/digital-twins/how-to-use-postman.md).

Finalmente, pode completar as mesmas operações de gestão utilizando o **CLI** das Gémeas Digitais Azure. Para saber mais sobre a utilização do CLI, consulte [*Como-a-fazer: Use o CLI das Gémeas Digitais Azure*](../articles/digital-twins/how-to-use-cli.md).