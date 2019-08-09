---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881339"
---
#### <a name="built-in-log-streaming"></a>Streaming de log interno

Use a `logstream` opção para começar a receber logs de streaming de um aplicativo de funções específico em execução no Azure, como no exemplo a seguir:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Você também pode exibir o [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) para seu aplicativo de funções em uma nova janela do navegador, `--browser` incluindo a opção, como no exemplo a seguir:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
