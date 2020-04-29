---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881339"
---
#### <a name="built-in-log-streaming"></a>Streaming de log incorporado

Utilize `logstream` a opção de começar a receber registos de streaming de uma aplicação de função específica em funcionamento no Azure, como no seguinte exemplo:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream (Fluxo de Métricas em Direto)

Também pode ver o [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) para a sua `--browser` aplicação de funções numa nova janela de navegador, incluindo a opção, como no exemplo seguinte:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
