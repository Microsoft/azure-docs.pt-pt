---
title: Resolução de problemas de estrangulamento em Azure Microsoft Docs
description: Erros de estrangulamento, retração e recuo no Azure Compute.
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: changov
ms.reviewer: vashan, rajraj
ms.openlocfilehash: b1cc8a43423ecd33218948aaa001fc34877eac60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074280"
---
# <a name="troubleshooting-api-throttling-errors"></a>Resolver erros de limitação da API 

Os pedidos do Azure Compute podem ser acelerados numa subscrição e numa base por região para ajudar no desempenho global do serviço. Garantimos que todas as chamadas para o Azure Compute Resource Provider (CRP), que gere recursos sob o microsoft.Compute namespace não excedem a taxa máxima de pedido de API permitida. Este documento descreve o estrangulamento da API, detalhes sobre como resolver problemas de estrangulamento, e as melhores práticas para evitar ser estrangulado.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Throttling por Azure Resource Manager vs Fornecedores de Recursos  

Como porta da frente para a Azure, o Azure Resource Manager faz a validação de autenticação e de primeira ordem e o estrangulamento de todos os pedidos de API que chegam. Os limites de taxa de chamada do Gestor de Recursos Azure e os cabeçalhos HTTP de resposta de diagnóstico relacionados são descritos [aqui.](../../azure-resource-manager/management/request-limits-and-throttling.md)
 
Quando um cliente AZure API obtém um erro de estrangulamento, o estado HTTP é 429 Pedidos demasiados. Para entender se o estrangulamento do pedido é feito pelo Azure Resource Manager ou por um fornecedor de recursos subjacente como o CRP, inspecione os `x-ms-ratelimit-remaining-subscription-reads` pedidos get e `x-ms-ratelimit-remaining-subscription-writes` cabeçalhos de resposta para pedidos não GET. Se a contagem de chamadas restante se aproximar de 0, o limite geral de chamada definido pelo Azure Resource Manager foi atingido. As atividades de todos os clientes por subscrição são contadas em conjunto. Caso contrário, o estrangulamento provém do fornecedor de recursos-alvo (o abordado pelo `/providers/<RP>` segmento do URL de pedido). 

## <a name="call-rate-informational-response-headers"></a>Cabeçalhos informativos de resposta informativa de taxa de chamada 

| Cabeçalho                            | Formato de valor                           | Exemplo                               | Descrição                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-restante-recurso |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Contagem de chamadas restantes da API para a política de estrangulamento que cobre o balde de recursos ou grupo de operação, incluindo o alvo deste pedido                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | O número de chamadas conta "cobrado" para este pedido HTTP para o limite da apólice aplicável. Este é mais tipicamente 1. Os pedidos de lote, tais como para escalar um conjunto de escala de máquina virtual, podem carregar várias contagens. |


Note que um pedido de API pode ser submetido a múltiplas políticas de estrangulamento. Haverá um `x-ms-ratelimit-remaining-resource` cabeçalho separado para cada política. 

Aqui está uma resposta de amostra para eliminar o pedido de conjunto de escala de máquina virtual.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Detalhes de erro de estrangulamento

O estatuto 429 HTTP é geralmente utilizado para rejeitar um pedido porque um limite de taxa de chamada é atingido. Uma resposta típica de erro de estrangulamento do Fornecedor de Recursos Compute será semelhante ao exemplo abaixo (apenas são mostrados cabeçalhos relevantes):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

A política com a contagem de chamadas restantes de 0 é a que é devido à qual o erro de estrangulamento é devolvido. Neste caso, isto `HighCostGet30Min` é. O formato geral do corpo de resposta é o formato de erro AZure Resource Manager API (conformante com OData). O código de erro principal, `OperationNotAllowed` é o que o Compute Resource Provider utiliza para reportar erros de estrangulamento (entre outros tipos de erros do cliente). A `message` propriedade do(s) erro interior(s) contém uma estrutura JSON serializada com os detalhes da violação de estrangulamento.

Como ilustrado acima, cada erro de estrangulamento inclui o `Retry-After` cabeçalho, que fornece o número mínimo de segundos que o cliente deve esperar antes de voltar a julgar o pedido. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Taxa de chamada da API e analisador de erros de estrangulamento
Uma versão de pré-visualização de uma funcionalidade de resolução de problemas está disponível para a API do fornecedor de recursos compute. Estes cmdlets PowerShell fornecem estatísticas sobre a taxa de pedido de API por intervalo de tempo por operação e violações de estrangulamento por grupo de operação (política):
-   [Exportação-AzLogAnalyticRequestRateByInterval](/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Exportação-AzLogAnalyticThrottledRequest](/powershell/module/az.compute/export-azloganalyticthrottledrequest)

As estatísticas de chamadas da API podem fornecer uma grande visão sobre o comportamento do cliente(s) de uma subscrição e permitir uma identificação fácil de padrões de chamada que causam estrangulamento.

Uma limitação do analisador por enquanto é que não conta pedidos de tipos de recursos de disco e instantâneo (em suporte a discos geridos). Uma vez que recolhe dados da telemetria da CRP, também não pode ajudar a identificar erros de estrangulamento da ARM. Mas estes podem ser facilmente identificados com base nos distintos cabeçalhos de resposta da ARM, como discutido anteriormente.

Os cmdlets PowerShell estão a utilizar uma API de serviço REST, que pode ser facilmente chamada diretamente pelos clientes (embora ainda sem suporte formal). Para ver o formato de pedido HTTP, executa os cmdlets com -Debug switch ou bisbilhota a sua execução com o Violinista.


## <a name="best-practices"></a>Melhores práticas 

- Não relemque os erros de API de serviço Azure incondicionalmente e/ou imediatamente. Uma ocorrência comum é que o código do cliente entre num ciclo rápido de retagem quando se depara com um erro que não é retível. As retrações acabarão por esgotar o limite de chamada permitido para o grupo da operação-alvo e impactar outros clientes da subscrição. 
- Em casos de automação de API de grande volume, considere implementar o auto-estrangulamento do lado do cliente proactivo quando a contagem de chamadas disponíveis para um grupo de operação alvo cai abaixo de algum limiar baixo. 
- Ao rastrear as operações de async, respeite as dicas de cabeçalho Retry-After. 
- Se o código do cliente precisar de informações sobre uma máquina virtual em particular, consulta-se que vM diretamente em vez de listar todos os VMs no grupo de recursos contendo ou toda a subscrição e, em seguida, escolher o VM necessário do lado do cliente. 
- Se o código do cliente necessitar de VMs, discos e instantâneos a partir de uma localização específica do Azure, utilize a forma baseada na localização da consulta em vez de consultar todos os VMs de subscrição e, em seguida, filtrar por localização do lado do cliente: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` consulta aos pontos finais regionais do Fornecedor de Recursos Compute. 
-   Ao criar ou atualizar recursos da API em particular, VMs e conjuntos de escala de máquinas virtuais, é muito mais eficiente acompanhar a operação de assíon devolvido até à conclusão do que fazer sondagens sobre o próprio URL de recursos (com base no `provisioningState` ).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a orientação de retíria para outros serviços em Azure, consulte [a orientação da Retry para serviços específicos](/azure/architecture/best-practices/retry-service-specific)
