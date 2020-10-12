---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68881339"
---
#### <a name="built-in-log-streaming"></a>Transmissão em fluxo de registos incorporada

Utilize a opção `logstream` para começar a receber registos de transmissão em fluxo de uma aplicação de funções específica em execução no Azure, como no exemplo seguinte:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Também pode ver o [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) para a sua aplicação de funções numa nova janela do browser, incluindo a opção `--browser`, como no exemplo seguinte:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
