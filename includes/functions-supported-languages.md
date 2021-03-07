---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d09d69db33cadee1b5a214e9cb2f09a508f37242
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431856"
---
|Linguagem                                 |1.x         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.Quadro NET 4.7)|GA (.NET Core 2.2<sup>1</sup>)| GA (.NET Core 3.1)<br/>[Pré-visualização (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (Nó 6)|GA (Nó 10 & 8)| GA (Nó 12 & 10)<br />Pré-visualização (Nó 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.Quadro NET 4.7)|GA (.NET Core 2.2<sup>1</sup>)| GA (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|N/D|GA (Java 8)| GA (Java 11 & 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |N/D|GA (PowerShell Core 6)| GA (PowerShell 7 & Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|N/D|GA (Python 3.7 & 3.6)| GA (Python 3.8, 3.7, & 3.6) <br/> Pré-visualização (Python 3.9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |N/D|GA<sup>2</sup>| GA<sup>2</sup> |


<sup>1</sup> .NET as aplicações da biblioteca de classe NET que visam a versão runtime 2.x podem agora ser executadas em .NET Core 3.1 no modo de compatibilidade .NET Core 2.x. Para saber mais, consulte [as considerações de Funções v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> Suportado através da transpilagem para JavaScript.

Consulte o artigo-guia de desenvolvimento específico da linguagem para obter mais detalhes sobre versões linguísticas suportadas.   
Para obter informações sobre as alterações planeadas no suporte linguístico, consulte [o roteiro do Azure](https://azure.microsoft.com/roadmap/?tag=functions).
