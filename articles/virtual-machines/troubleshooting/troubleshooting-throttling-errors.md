---
title: Solucionando problemas de erros de limitação no Azure | Microsoft Docs
description: Limitação de erros, novas tentativas e retirada na computação do Azure.
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
ms.openlocfilehash: db1c6e8e4f1e98db08d5f7ff0ef218fa42d25860
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103308"
---
# <a name="troubleshooting-api-throttling-errors"></a>Solucionando problemas de erros de limitação de API 

As solicitações de computação do Azure podem ser limitadas em uma assinatura e em uma base por região para ajudar com o desempenho geral do serviço. Garantimos que todas as chamadas para o CRP (provedor de recursos de computação) do Azure, que gerencia recursos no namespace Microsoft. Compute, não exceda a taxa máxima permitida de solicitação de API. Este documento descreve a limitação da API, detalhes sobre como solucionar problemas de limitação e práticas recomendadas para evitar a limitação.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Limitação por Azure Resource Manager provedores de recursos vs  

Como a porta de frente para o Azure, Azure Resource Manager faz a autenticação e a validação de primeiro pedido e a limitação de todas as solicitações de API de entrada. Azure Resource Manager limites de taxa de chamada e cabeçalhos HTTP de resposta de diagnóstico relacionados são descritos [aqui](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-request-limits).
 
Quando um cliente de API do Azure recebe um erro de limitação, o status HTTP é 429 muitas solicitações. Para entender se a limitação da solicitação é feita por Azure Resource Manager ou por um provedor de recursos subjacente como a `x-ms-ratelimit-remaining-subscription-reads` CRP, inspecione o `x-ms-ratelimit-remaining-subscription-writes` para obter solicitações de Get e cabeçalhos de resposta para solicitações que não são de obtenção. Se a contagem de chamadas restante estiver se aproximando de 0, o limite de chamada geral da assinatura definido por Azure Resource Manager foi atingido. As atividades por todos os clientes de assinatura são contadas em conjunto. Caso contrário, a limitação é proveniente do provedor de recursos de destino (aquele abordado `/providers/<RP>` pelo segmento da URL de solicitação). 

## <a name="call-rate-informational-response-headers"></a>Cabeçalhos de resposta informativos de taxa de chamada 

| Cabeçalho                            | Formato do valor                           | Exemplo                               | Descrição                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Contagem de chamadas de API restante para a política de limitação que abrange o Bucket de recursos ou o grupo de operações, incluindo o destino desta solicitação                                                                   |
| x-MS-Request-encargos               | ```<count>```                             | 1                                     | O número de chamadas conta "cobrado" para essa solicitação HTTP para o limite da política aplicável. Geralmente, isso é 1. Solicitações em lote, como para dimensionar um conjunto de dimensionamento de máquinas virtuais, podem cobrar várias contagens. |


Observe que uma solicitação de API pode estar sujeita a várias políticas de limitação. Haverá um cabeçalho separado `x-ms-ratelimit-remaining-resource` para cada política. 

Aqui está uma resposta de exemplo para excluir a solicitação do conjunto de dimensionamento de máquinas virtuais.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Detalhes do erro de limitação

O status HTTP 429 é comumente usado para rejeitar uma solicitação porque um limite de taxa de chamada é atingido. Uma resposta de erro de limitação típica do provedor de recursos de computação se parecerá com o exemplo abaixo (somente os cabeçalhos relevantes são mostrados):

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

A política com a contagem de chamadas restante de 0 é a devida à qual o erro de limitação é retornado. Nesse caso, isso é `HighCostGet30Min`. O formato geral do corpo da resposta é o formato de erro de API Azure Resource Manager geral (compatível com OData). O código de erro principal `OperationNotAllowed`,, é o que um provedor de recursos de computação usa para relatar erros de limitação (entre outros tipos de erros de cliente). A `message` propriedade dos erros internos contém uma estrutura JSON serializada com os detalhes da violação de limitação.

Conforme ilustrado acima, cada erro de limitação `Retry-After` inclui o cabeçalho, que fornece o número mínimo de segundos que o cliente deve aguardar antes de repetir a solicitação. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Taxa de chamadas da API e acelerador de erros de limitação
Uma versão de visualização de um recurso de solução de problemas está disponível para a API do provedor de recursos de computação. Esses cmdlets do PowerShell fornecem estatísticas sobre a taxa de solicitação de API por intervalo de tempo por operação e violações de limitação por grupo de operação (política):
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

As estatísticas de chamada à API podem fornecer uma ótima percepção do comportamento dos clientes de uma assinatura e permitir a fácil identificação de padrões de chamada que causam limitação.

Uma limitação do analisador durante o tempo é que ele não conta solicitações de tipos de recursos de instantâneo e disco (em suporte a discos gerenciados). Como ele coleta dados da telemetria do CRP, também não pode ajudar a identificar erros de limitação do ARM. Mas eles podem ser identificados facilmente com base nos cabeçalhos de resposta do ARM distintivo, conforme discutido anteriormente.

Os cmdlets do PowerShell estão usando uma API de serviço REST, que pode ser facilmente chamada diretamente por clientes (embora sem suporte formal ainda). Para ver o formato de solicitação HTTP, execute os cmdlets com a opção-debug ou o rastreamento em sua execução com o Fiddler.


## <a name="best-practices"></a>Melhores práticas 

- Não repita os erros da API de serviço do Azure de forma incondicional e/ou imediatamente. Uma ocorrência comum é o código do cliente entrar em um loop de repetição rápido ao encontrar um erro que não é capaz de tentar novamente. As repetições eventualmente esgotarão o limite de chamadas permitido para o grupo da operação de destino e afetarão outros clientes da assinatura. 
- Em casos de automação de API de alto volume, considere a possibilidade de implementar a autolimitação do lado do cliente proativo quando a contagem de chamadas disponível para um grupo de operações de destino cair abaixo de um limite baixo. 
- Ao rastrear operações assíncronas, respeite as dicas de cabeçalho Retry-After. 
- Se o código do cliente precisar de informações sobre uma máquina virtual específica, consulte a VM diretamente em vez de listar todas as VMs no grupo de recursos que a contém ou toda a assinatura e, em seguida, escolher a VM necessária no lado do cliente. 
- Se o código do cliente precisar de VMs, discos e instantâneos de um local específico do Azure, use o formulário baseado na localização da consulta em vez de consultar todas as VMs de assinatura e, em seguida `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` , filtrar por local no lado do cliente: consulta para o provedor de recursos de computação regional extremidade. 
-   Ao criar ou atualizar recursos de API em particular, VMs e conjuntos de dimensionamento de máquinas virtuais, é muito mais eficiente rastrear a operação assíncrona retornada para conclusão do que a sondagem na própria URL do `provisioningState`recurso (com base no).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as diretrizes de repetição para outros serviços no Azure, consulte [diretrizes de repetição para serviços específicos](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
