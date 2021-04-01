---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93425149"
---
Executar o seguinte comando para visualizar perto de [registos](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)de streaming em tempo real :

```console
func azure functionapp logstream <APP_NAME> 
```

Numa janela de terminal separada ou no browser, volte a ligar para a função remota. Um registo verboso da execução da função em Azure é mostrado no terminal. 