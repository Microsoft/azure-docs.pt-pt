---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 5abefd55aa06f53bc3b437b29a2582b3e2239a65
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131618"
---
```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|routePrefix|api|O prefixo de rota que se aplica a todas as rotas. Utilize uma cadeia vazia para remover o prefixo predefinido. |
|maxOutstandingRequests|200*|O número máximo de pedidos pendentes que são mantidos num determinado momento. Este limite inclui pedidos que são colocados em fila, mas não tem iniciado a execução, bem como qualquer em execuções de progresso. Quaisquer pedidos excede este limite são rejeitados com uma resposta de "(servidor ocupado) 429. Que permite que os chamadores empregar as estratégias de repetição com base no tempo e também ajuda a controlar latências máximo do pedido. Isto apenas controla a colocação em fila que ocorre dentro do caminho de execução do host de script. Outras filas, como a fila de solicitações ASP.NET continuarão a estar em vigor e não são afetadas por esta definição. * A predefinição para a versão 1.x é não vinculado. A predefinição para a versão 2.x num plano de consumo é 200. A predefinição para a versão 2.x num plano dedicado são independentes.|
|maxConcurrentRequests|100 *|O número máximo de funções de http que será executado em paralelo. Isso permite que controle simultaneidade, que pode ajudar a gerir a utilização de recursos. Por exemplo, pode ter uma função de http que utiliza muitos recursos do sistema (memória/cpu/sockets), de modo que ele faz com que problemas quando simultaneidade for demasiado elevada. Ou poderá ter uma função que faça pedidos de saída para um serviço de terceiros, e essas chamadas tem de ser o limite de taxa. Nestes casos, aplicar uma limitação aqui pode ajudar. * A predefinição para a versão 1.x é não vinculado. A predefinição para a versão 2.x num plano de consumo é 100. A predefinição para a versão 2.x num plano dedicado são independentes.|
|dynamicThrottlesEnabled|TRUE *|Quando ativada, esta definição as causas o pipeline de processamento do pedido para verificar periodicamente o desempenho do sistema contadores, como ligações/threads/processos/memória/cpu/etc. e se qualquer um desses contadores são através de um limiar de elevada incorporado (80%), solicita irão ser rejeitada com uma resposta de "(servidor ocupado) 429 até que o counter(s) regressar ao níveis normais. * A predefinição para a versão 1.x é false. A predefinição para a versão 2.x num plano de consumo é verdadeiro. A predefinição para a versão 2.x num plano dedicado é false.|
