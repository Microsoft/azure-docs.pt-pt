---
title: Erros de estrangulamento de resolução de problemas em Azure Microsoft Docs
description: Erros de estrangulamento, tentativas e recuos na Azure Compute.
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
ms.openlocfilehash: f5fbd80fc9a8e519cf8f49ab16d7e747c6a8171b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76045353"
---
# <a name="troubleshooting-api-throttling-errors"></a>Resolver erros de limitação da API 

Os pedidos da Azure Compute podem ser estrangulados numa subscrição e numa base por região para ajudar no desempenho global do serviço. Garantimos que todas as chamadas para o Fornecedor de Recursos da Computação Azure (CRP), que gere recursos sob o nome Microsoft.Compute não excedam a taxa máxima permitida de pedidos de API. Este documento descreve a aceleração da API, detalhes sobre como resolver problemas e as melhores práticas para evitar ser estrangulado.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Estrangulamento por Azure Resource Manager vs Fornecedores de Recursos  

Como porta da frente do Azure, o Gestor de Recursos Azure faz a autenticação e a validação de primeira ordem e o estrangulamento de todos os pedidos de API que chegam. Os limites da taxa de chamada do Gestor de Recursos Azure e os cabeçalhos HTTP de resposta de diagnóstico relacionados são descritos [aqui](https://docs.microsoft.com/azure/azure-resource-manager/management/request-limits-and-throttling).
 
Quando um cliente DaPi Azure obtém um erro de estrangulamento, o estatuto HTTP é de 429 Pedidos Demasiados. Para entender se o estrangulamento do pedido é feito pelo Gestor de Recursos Azure ou por um fornecedor de recursos subjacente, como o CRP, inspecione os `x-ms-ratelimit-remaining-subscription-reads` pedidos de GET e `x-ms-ratelimit-remaining-subscription-writes` os cabeçalhos de resposta para pedidos não GET. Se a contagem de chamadas restante se aproximar de 0, o limite geral de chamada da subscrição definido pelo Gestor de Recursos Azure foi atingido. As atividades de todos os clientes de subscrição são contadas em conjunto. Caso contrário, a aceleração provém do fornecedor de recursos-alvo `/providers/<RP>` (o abordado pelo segmento do URL de pedido). 

## <a name="call-rate-informational-response-headers"></a>Cabeçalhos de resposta informativa da taxa de chamada 

| Cabeçalho                            | Formato de valor                           | Exemplo                               | Descrição                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-restante-recurso |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Contagem de chamadas restantes da API para a política de estrangulamento que cobre o balde de recursos ou grupo de operação, incluindo o alvo deste pedido                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | O número de chamadas conta "cobrado" para este pedido http para o limite da política aplicável. Este é mais tipicamente 1. Os pedidos de lote, tais como para escalar um conjunto de escala de máquina virtual, podem carregar várias contagens. |


Note que um pedido de API pode ser sujeito a múltiplas políticas de estrangulamento. Haverá um `x-ms-ratelimit-remaining-resource` cabeçalho separado para cada política. 

Aqui está uma resposta da amostra para eliminar o pedido de conjunto de escala de máquina virtual.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Detalhes do erro de estrangulamento

O estatuto de 429 HTTP é geralmente utilizado para rejeitar um pedido porque um limite de taxa de chamada é atingido. Uma resposta típica de erro de estrangulamento do Provedor de Recursos Compute será como o exemplo abaixo (apenas são mostrados cabeçalhos relevantes):

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

A política com a contagem de chamadas restante de 0 é a que se deve ao qual o erro de estrangulamento é devolvido. Neste caso, `HighCostGet30Min`isto é. O formato geral do corpo de resposta é o formato de erro aPI geral do Gestor de Recursos Azure (conformante com OData). O código de `OperationNotAllowed`erro principal, é o único Que o Fornecedor de Recursos computacionais usa para reportar erros de estrangulamento (entre outros tipos de erros do cliente). A `message` propriedade do(s) erro interno contém uma estrutura JSON serializada com os detalhes da violação do estrangulamento.

Como ilustrado acima, cada erro de `Retry-After` estrangulamento inclui o cabeçalho, que fornece o número mínimo de segundos que o cliente deve esperar antes de voltar a experimentar o pedido. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>Taxa de chamada da API e analisador de erros de estrangulamento
Uma versão de pré-visualização de uma funcionalidade de resolução de problemas está disponível para a API do fornecedor de recursos Compute. Estes cmdlets PowerShell fornecem estatísticas sobre a taxa de pedido de API por intervalo de tempo por operação e violações estranguladas por grupo de operação (política):
-   [Export-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

As estatísticas de chamadas da API podem fornecer uma grande visão sobre o comportamento do(s) cliente(s) de uma subscrição e permitir uma fácil identificação dos padrões de chamada que causam estrangulamento.

Uma limitação do analisador por enquanto é que não conta pedidos para tipos de recursos de disco e instantâneo (em suporte a discos geridos). Uma vez que recolhe dados da telemetria da CRP, também não pode ajudar a identificar erros de estrangulamento da ARM. Mas estes podem ser facilmente identificados com base nos distintos cabeçalhos de resposta arm, como discutido anteriormente.

Os cmdlets PowerShell estão usando um serviço REST API, que pode ser facilmente chamado diretamente pelos clientes (embora ainda sem suporte formal). Para ver o formato de pedido HTTP, execute os cmdlets com o interruptor -Debug ou bise na sua execução com o Fiddler.


## <a name="best-practices"></a>Melhores práticas 

- Não tente voltar a tentar erros de Serviço Azure API incondicionalmente e/ou imediatamente. Uma ocorrência comum é que o código do cliente entre num ciclo de repetição rápida ao encontrar um erro que não é retentor. As tentativas acabarão por esgotar o limite de chamada permitido para o grupo da operação alvo e afetar outros clientes da subscrição. 
- Em casos de automação aPI de grande volume, considere implementar a auto-estrangulamento proativa do lado do cliente quando a contagem de chamadas disponível para um grupo de operação-alvo descer abaixo de algum limiar baixo. 
- Ao rastrear operações de asincronização, respeite as dicas do cabeçalho Retry-After. 
- Se o código do cliente necessitar de informações sobre uma máquina virtual em particular, questione que a VM diretamente em vez de listar todos os VMs no grupo de recursos contendo ou em toda a subscrição e, em seguida, escolher o VM necessário do lado do cliente. 
- Se o código do cliente precisar de VMs, discos e instantâneos de uma localização específica do Azure, utilize a forma baseada na `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` localização da consulta em vez de consultar todos os VMs de subscrição e, em seguida, filtrar por localização do lado do cliente: consulta aos pontos finais regionais do Fornecedor de Recursos computacional. 
-   Ao criar ou atualizar recursos API em particular, VMs e conjuntos de escala de máquinas virtuais, é muito mais eficiente `provisioningState`acompanhar a operação de sincronia devolvida até à conclusão do que fazer as sondagens sobre o próprio URL de recursos (com base no ).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a orientação de retry para outros serviços em Azure, consulte [a orientação de Retry para serviços específicos](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
