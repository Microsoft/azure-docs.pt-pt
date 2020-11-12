---
author: baanders
description: incluir arquivo para Azure Digital Twins - formas de gerir caso
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 47de00780fb259e7476c16dea9b7c63bc512a211
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533910"
---
Este artigo destaca como completar diferentes operações de gestão utilizando as [Gémeas Digitais Azure .NET (C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true). Também pode criar essas mesmas chamadas de gestão utilizando os outros SDKs de língua descritos em [*Como-a-: Use as APIs e SDKs de gémeos digitais Azure*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Lembre-se que todos os métodos SDK vêm em versões sincronizadas e assíncronos. Para chamadas de paging, os métodos async regressam `AsyncPageable<T>` enquanto as versões sincronizadas regressam `Pageable<T>` .

Outra opção de gestão é chamar diretamente as [**APIs**](/rest/api/azure-digitaltwins/) de REST dos Gémeos Digitais Azure para esta área de tema.

Finalmente, pode completar as mesmas operações de gestão utilizando o **CLI** das Gémeas Digitais Azure. Para saber mais sobre a utilização do CLI, consulte [*Como-a-fazer: Use o CLI das Gémeas Digitais Azure*](../articles/digital-twins/how-to-use-cli.md).