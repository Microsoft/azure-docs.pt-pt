---
title: Limites de pedido e limitação - Azure Resource Manager
description: Descreve como utilizar a limitação com pedidos do Azure Resource Manager, quando atingiu os limites de subscrição.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: f457b316d9f499f2cab02452c1b03ad07a9aef27
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302835"
---
# <a name="throttling-resource-manager-requests"></a>Limitar pedidos do Resource Manager

Para cada subscrição do Azure e o inquilino, o Resource Manager permite até 12 000 pedidos por hora de leitura e pedidos por hora de escrita de 1200. Esses limites têm como escopo a entidade de segurança (usuário ou aplicativo) que faz as solicitações e a ID da assinatura ou a ID do locatário. Se suas solicitações vierem de mais de uma entidade de segurança, seu limite entre a assinatura ou o locatário será maior que 12.000 e 1.200 por hora.

Pedidos são aplicados a sua subscrição ou o seu inquilino. As solicitações de assinatura são aquelas que envolvem a passagem da sua ID de assinatura, como a recuperação dos grupos de recursos em sua assinatura. Pedidos de inquilino não incluem o ID da subscrição, como a obtenção de localizações do Azure válidas.

Estes limites se aplicam a cada instância do Azure Resource Manager. Existem várias instâncias em cada região do Azure e Azure Resource Manager é implementado para todas as regiões do Azure.  Para que, na prática, os limites são efetivamente muito mais do que estes limites, como utilizador pedidos são normalmente servidos por muitas instâncias diferentes.

Se a sua aplicação ou script atingir estes limites, terá de limitar os seus pedidos. Este artigo mostra como determinar as solicitações restantes que você tem antes de atingir o limite e como responder quando atingir o limite.

Quando atingir o limite, receberá o código de estado HTTP **429 demasiados pedidos**.

O grafo de recursos do Azure limita o número de solicitações para suas operações. As etapas neste artigo para determinar as solicitações restantes e como responder quando o limite é atingido também se aplicam ao grafo de recursos. No entanto, o grafo de recursos define seu próprio limite e a taxa de redefinição. Para obter mais informações, consulte [limitação no grafo de recursos do Azure](../governance/resource-graph/overview.md#throttling).

## <a name="remaining-requests"></a>Pedidos restantes
Pode determinar o número de pedidos restantes, examinando os cabeçalhos de resposta. As solicitações de leitura retornam um valor no cabeçalho para o número de solicitações de leitura restantes. As solicitações de gravação incluem um valor para o número de solicitações de gravação restantes. A tabela seguinte descreve os cabeçalhos de resposta, que pode examinar para esses valores:

| Cabeçalho de resposta | Descrição |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Subscrição de âmbito lê restantes. Este valor é devolvido nas operações de leitura. |
| x-ms-ratelimit-remaining-subscription-writes |Subscrição de âmbito escreve restantes. Este valor é devolvido nas operações de escrita. |
| x-ms-ratelimit-remaining-tenant-reads |No âmbito do inquilino lê restantes |
| x-ms-ratelimit-remaining-tenant-writes |No âmbito do inquilino escreve restantes |
| x-ms-ratelimit-remaining-subscription-resource-requests |Pedidos de tipo de recurso restantes de âmbito da subscrição.<br /><br />Este valor de cabeçalho só é devolvido se um serviço tiver substituído o limite predefinido. Gestor de recursos adiciona esse valor em vez da subscrição leituras ou escritas. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Pedidos de coleção de tipo de recurso restantes de âmbito da subscrição.<br /><br />Este valor de cabeçalho só é devolvido se um serviço tiver substituído o limite predefinido. Este valor fornece o número de pedidos de recolha de restantes (recursos de lista). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Pedidos de tipo de recurso restantes de âmbito do inquilino.<br /><br />Este cabeçalho é adicionado apenas para pedidos no nível de inquilino e apenas se um serviço tiverem substituído o limite predefinido. Gestor de recursos adiciona esse valor em vez do inquilino leituras ou escritas. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Pedidos de coleção de tipo de recurso restantes de âmbito do inquilino.<br /><br />Este cabeçalho é adicionado apenas para pedidos no nível de inquilino e apenas se um serviço tiverem substituído o limite predefinido. |

## <a name="retrieving-the-header-values"></a>Recuperar os valores de cabeçalho
A obter estes valores de cabeçalho no seu código ou script não é diferente de qualquer valor de cabeçalho a obter. 

Por exemplo, no **c#** , recupera o valor de cabeçalho a partir de um **HttpWebResponse** com o nome do objeto **resposta** com o código a seguir:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

Na **PowerShell**, recupera o valor de cabeçalho a partir de uma operação de Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Para obter um exemplo completo do PowerShell, consulte [verificar limites de Gestor de recursos para uma subscrição](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Se quiser ver os pedidos de restantes para depuração, pode fornecer a **-Debug** parâmetro no seu **PowerShell** cmdlet.

```powershell
Get-AzResourceGroup -Debug
```

Que retorna valores de muitos, incluindo o seguinte valor de resposta:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Para obter os limites de escrita, utilize uma operação de escrita: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Que retorna valores de muitos, incluindo os seguintes valores:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

Na **CLI do Azure**, recupera o valor de cabeçalho ao utilizar a opção mais detalhada.

```azurecli
az group list --verbose --debug
```

Que retorna valores de muitos, incluindo os seguintes valores:

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Para obter os limites de escrita, utilize uma operação de escrita: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Que retorna valores de muitos, incluindo os seguintes valores:

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>A aguardar antes de enviar o pedido seguinte
Quando atingir o limite de pedido, o Resource Manager devolve os **429** código de estado HTTP e um **Retry-After** valor no cabeçalho. O **Retry-After** valor Especifica o número de segundos deve aguardar a sua aplicação (ou modo de suspensão) antes de enviar o pedido seguinte. Se enviar um pedido antes do valor de repetição tiver sido decorrido, o pedido não é processado e é devolvido um novo valor de repetição.

## <a name="next-steps"></a>Passos Seguintes

* Para obter um exemplo completo do PowerShell, consulte [verificar limites de Gestor de recursos para uma subscrição](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Para obter mais informações sobre limites e quotas, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md).
* Para saber mais sobre o processamento de solicitações assíncronas do REST, veja [monitorizar operações assíncronas de Azure](resource-manager-async-operations.md).
