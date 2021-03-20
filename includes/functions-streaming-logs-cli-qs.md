---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93425149"
---
Executar o seguinte comando para visualizar perto de [registos](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)de streaming em tempo real :

```console
func azure functionapp logstream <APP_NAME> 
```

Numa janela de terminal separada ou no browser, volte a ligar para a função remota. Um registo verboso da execução da função em Azure é mostrado no terminal. 