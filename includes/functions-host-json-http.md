---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ec825bb59840784ca45e8a38caa664c19883fb32
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263316"
---
|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|routePrefix|api|O prefixo de rota que se aplica a todas as rotas. Use uma cadeia de caracteres vazia para remover o prefixo padrão. |
|maxOutstandingRequests|200<sup>*</sup>|O número máximo de solicitações pendentes que são mantidas em um determinado momento. Esse limite inclui solicitações que estão na fila, mas não iniciaram a execução, bem como qualquer execução em andamento. Todas as solicitações de entrada acima desse limite são rejeitadas com uma resposta de 429 "muito ocupado". Isso permite que os chamadores empreguem estratégias de repetição baseadas em tempo, além de ajudar você a controlar as latências de solicitação máximas. Isso controla somente o enfileiramento que ocorre no caminho de execução do host de script. Outras filas, como a fila de solicitações ASP.NET, ainda estarão em vigor e não serão afetadas por essa configuração. <sup>*</sup> O padrão para a versão 1. x é não associado (`-1`). O padrão para a versão 2. x em um plano de consumo é 200. O padrão para a versão 2. x em um plano dedicado não está associado (`-1`).|
|maxConcurrentRequests|100<sup>*</sup>|O número máximo de funções http que serão executadas em paralelo. Isso permite que você controle a simultaneidade, o que pode ajudar a gerenciar a utilização de recursos. Por exemplo, você pode ter uma função http que usa muitos recursos do sistema (memória/CPU/soquetes), de modo que isso cause problemas quando a simultaneidade for muito alta. Ou você pode ter uma função que faça solicitações de saída para um serviço de terceiros, e essas chamadas precisam ser limitadas por taxa. Nesses casos, a aplicação de uma limitação aqui pode ajudar. <sup>*</sup> O padrão para a versão 1. x é não associado (`-1`). O padrão para a versão 2. x em um plano de consumo é 100. O padrão para a versão 2. x em um plano dedicado não está associado (`-1`).|
|dynamicThrottlesEnabled|verdadeiro<sup>*</sup>|Quando habilitado, essa configuração faz com que o pipeline de processamento de solicitações Verifique periodicamente os contadores de desempenho do sistema como conexões/threads/processos/memória/CPU/etc. e, se qualquer um desses contadores estiver acima de um limite alto interno (80%), as solicitações serão rejeitado com uma resposta de 429 "muito ocupado" até que os contadores retornem aos níveis normais. <sup>*</sup> O padrão para a versão 1. x é false. O padrão para a versão 2. x em um plano de consumo é true. O padrão para a versão 2. x em um plano dedicado é false.|
