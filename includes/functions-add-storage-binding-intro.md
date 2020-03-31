---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/29/2019
ms.author: glenga
ms.openlocfilehash: f16852cdc18053a3a8e375dc6f14e39bb069afef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72329623"
---
As Funções Azure permitem ligar os serviços azure e outros recursos a funções sem ter de escrever o seu próprio código de integração. Estas *encadernações*, que representam tanto a entrada como a saída, são declaradas dentro da definição de função. Os dados de enlaces são fornecidos à função como parâmetros. Um *gatilho* é um tipo especial de encadernação de entrada. Embora uma função tenha apenas um gatilho, pode ter várias ligações de entrada e saída. Para saber mais, consulte as [Funções Azure desencadeia e encaderna conceitos.](../articles/azure-functions/functions-triggers-bindings.md)