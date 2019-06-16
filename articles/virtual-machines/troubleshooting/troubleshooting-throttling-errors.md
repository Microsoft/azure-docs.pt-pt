---
title: Resolução de problemas de limitação erros no Azure | Documentos da Microsoft
description: Limitação de erros, de repetições e de término na computação do Azure.
services: virtual-machines
documentationcenter: ''
author: changov
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: vashan, rajraj, changov
ms.openlocfilehash: efa10f5beae64105857b00b186683d491edb00f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233775"
---
# <a name="troubleshooting-api-throttling-errors"></a>Resolução de problemas de erros de limitação de API 

Pedidos de computação do Azure podem ser otimizados numa subscrição e numa base por região para ajudar com o desempenho geral do serviço. Podemos assegurar que todas as chamadas para o Azure computação fornecedor de recursos (CRP), que gere os recursos no espaço de nomes Microsoft. Compute não excederem a velocidade máxima de pedido de API permitida. Este documento descreve a API de limitação, detalhes sobre como resolver problemas de limitação, e as melhores práticas para evitar a ser limitada.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Limitação de fornecedores de recursos do Azure Resource Manager vs  

Como a porta de entrada para o Azure, o Azure Resource Manager faz a validação de autenticação e a primeira e a limitação de entrada de todos os pedidos de API. Limites de velocidade de chamada do Azure Resource Manager e cabeçalhos de resposta de diagnóstico relacionadas HTTP são descritos [aqui](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-request-limits).
 
Quando um cliente de API do Azure obtém um erro de limitação, o estado HTTP é 429 demasiados pedidos. Para compreender se a limitação de pedidos é feito com o Azure Resource Manager ou um fornecedor de recursos subjacentes como o CRP, Inspecione o `x-ms-ratelimit-remaining-subscription-reads` solicitações GET e `x-ms-ratelimit-remaining-subscription-writes` cabeçalhos de resposta para pedidos de non-GET. Se a contagem de chamadas restantes que está a aproximar-se o 0, foi atingido o limite de chamada geral da subscrição definido pelo Azure Resource Manager. Atividades de todos os clientes de subscrição são contabilizadas em conjunto. Caso contrário, a limitação é proveniente do fornecedor de recursos de destino (a que é abordado o `/providers/<RP>` segmento de URL do pedido). 

## <a name="call-rate-informational-response-headers"></a>Chamar os cabeçalhos de resposta informativa de taxa 

| Cabeçalho                            | Formato do valor                           | Exemplo                               | Descrição                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Contagem de chamadas de API restante para a política de limitação que abrangem o grupo de bucket ou operação de recursos incluindo o destino deste pedido                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | O número de chamada de conta "cobrado" para este pedido HTTP na direção de limite da política aplicável. Isso normalmente é 1. Pedidos de lote, como para dimensionar um conjunto de dimensionamento de máquina virtual, podem cobrar contagens vários. |


Tenha em atenção que um pedido de API pode estar sujeitos a múltiplas políticas de limitação. Haverá um separado `x-ms-ratelimit-remaining-resource` cabeçalho para cada política. 

Aqui está uma resposta de exemplo para eliminar o pedido de conjunto de dimensionamento de máquina virtual.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Detalhes do erro de limitação

O estado HTTP 429 é normalmente utilizado para rejeitar um pedido por um limite de taxa de chamada é alcançado. Uma resposta típica limitação de erro do fornecedor de recursos de computação terá um aspeto semelhante ao seguinte (apenas cabeçalhos relevantes são mostrados):

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

A política com o restante de chamada de contagem de 0 é o que é devolvido o erro de limitação. Nesse caso, que é `HighCostGet30Min`. O formato geral do corpo da resposta é o formato de erro de API do Azure Resource Manager geral (compatível com o OData). O código de erro principal, `OperationNotAllowed`, é um fornecedor de recursos de computação utiliza para comunicar a limitação de erros (entre outros tipos de erros do cliente). O `message` propriedade de erros internos contém uma estrutura JSON serializada com os detalhes da violação limitação.

Conforme ilustrado acima, todos os erros de limitação incluem o `Retry-After` cabeçalho, que fornece o número mínimo de segundos, o cliente deve aguardar antes de repetir o pedido. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Taxa de e limitação de erro de analisador de chamada de API
Uma versão de pré-visualização de um recurso de resolução de problemas está disponível para de API o fornecedor de recursos de computação. Estes cmdlets do PowerShell fornecem estatísticas sobre a taxa de pedidos de API por intervalo de tempo por operação e violações de limitação por grupo de operação (política):
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

As estatísticas de chamada de API podem fornecer uma ótima visão para o comportamento dos clientes de uma subscrição e ativar fácil identificação de padrões de chamada que fazer com que a limitação.

Uma limitação do analyzer por enquanto, é que não conta pedidos para tipos de recurso de disco e o instantâneo (para dar suporte a discos geridos). Uma vez que ele coleta dados de telemetria do CRP, ele também não pode ajudar a identificar erros de limitação do ARM. Mas aqueles podem ser identificados facilmente com base dos cabeçalhos de resposta ARM distintas, como discutido anteriormente.

Os cmdlets do PowerShell está a utilizar uma API de serviço do REST, que pode ser facilmente chamada diretamente por clientes (embora com nenhum suporte formal ainda). Para ver o formato do pedido HTTP, execute os cmdlets com - comutador de depuração ou snoop em sua execução com o Fiddler.


## <a name="best-practices"></a>Melhores práticas 

- Não repetir a erros de API de serviço do Azure, incondicionalmente e/ou imediatamente. É uma ocorrência comum para o código de cliente obter num loop de repetição rápida quando encontrar um erro que não é capaz de repetição. As repetições, eventualmente, irão esgotar o limite permitido de chamada para o grupo da operação de destino e afetar outros clientes da subscrição. 
- Em casos de automatização de API de grande volume, considere a implementação proativa de cliente personalizada limitação quando a contagem de chamada disponíveis para um grupo de operação do destino cai abaixo alguns limiar inferior. 
- Se a monitorização de operações assíncronas, respeitem as sugestões de cabeçalho Retry-After. 
- Se o código de cliente precisa obter informações sobre uma Máquina Virtual específica, consulte essa VM diretamente em vez de listagem de todas as VMs no grupo de recursos que contém ou a subscrição completa e, em seguida, escolher a VM necessária no lado do cliente. 
- Se o código de cliente tem de VMs, discos e instantâneos de uma localização do Azure específica, utilize o formulário com base na localização da consulta em vez de consulta de subscrição de todas as VMs e, em seguida, filtrar por localização no lado do cliente: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` consulta para o fornecedor de recursos de computação regionais pontos de extremidade. 
-   Quando criar ou atualizar recursos da API em particular, VMs e conjuntos de dimensionamento de máquina virtual, é muito mais eficiente para controlar a operação assíncrona retornado até à conclusão que a consulta no URL de recurso em si (com base no `provisioningState`).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre orientações de repetição para outros serviços do Azure, consulte [repita a orientação para serviços específicos](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
