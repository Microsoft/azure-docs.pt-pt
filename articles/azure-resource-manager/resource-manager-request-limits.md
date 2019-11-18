---
title: Limitação e limites de solicitação
description: Descreve como utilizar a limitação com pedidos do Azure Resource Manager, quando atingiu os limites de subscrição.
ms.topic: conceptual
ms.date: 10/26/2019
ms.custom: seodec18
ms.openlocfilehash: b47943d69d209f5a0406c293b5a24c6ac0ad0c10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150467"
---
# <a name="throttling-resource-manager-requests"></a>Limitar pedidos do Resource Manager

Este artigo descreve como Azure Resource Manager limita as solicitações. Ele mostra como acompanhar o número de solicitações que permanecem antes de atingir o limite e como responder quando você atingir o limite.

A limitação ocorre em dois níveis. Azure Resource Manager limita as solicitações para a assinatura e o locatário. Se a solicitação estiver sob os limites de limitação para a assinatura e o locatário, o Resource Manager roteia a solicitação para o provedor de recursos. O provedor de recursos aplica limites de limitação que são adaptados para suas operações. A imagem a seguir mostra como a limitação é aplicada, uma vez que uma solicitação passa do usuário para Azure Resource Manager e o provedor de recursos.

![Limitação de solicitação](./media/resource-manager-request-limits/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Limites de assinatura e locatário

Todas as operações de nível de assinatura e de locatário estão sujeitas a limites de limitação. As solicitações de assinatura são aquelas que envolvem a passagem da sua ID de assinatura, como a recuperação dos grupos de recursos em sua assinatura. Pedidos de inquilino não incluem o ID da subscrição, como a obtenção de localizações do Azure válidas.

Os limites de limitação padrão por hora são mostrados na tabela a seguir.

| Âmbito | Operações | Limite |
| ----- | ---------- | ------- |
| Subscrição | pareça | 12000 |
| Subscrição | excluído | 15 000 |
| Subscrição | registra | 1200 |
| Inquilino | pareça | 12000 |
| Inquilino | registra | 1200 |

Estes limites estão circunscritos ao principal de segurança (utilizador ou aplicação) que faz os pedidos e o ID de subscrição ou ID de inquilino. Se os seus pedidos forem provenientes de mais de um principal de segurança, o limite da subscrição ou do inquilino é superior a 12 000 e 1200 por hora.

Estes limites se aplicam a cada instância do Azure Resource Manager. Existem várias instâncias em cada região do Azure e Azure Resource Manager é implementado para todas as regiões do Azure.  Portanto, na prática, os limites são maiores que esses limites. As solicitações de um usuário geralmente são tratadas por instâncias diferentes do Azure Resource Manager.

## <a name="resource-provider-limits"></a>Limites do provedor de recursos

Os provedores de recursos aplicam seus próprios limites de limitação. Como o Resource Manager limita-se pela ID principal e por instância do Resource Manager, o provedor de recursos pode receber mais solicitações do que os limites padrão na seção anterior.

Esta seção aborda os limites de limitação de alguns provedores de recursos amplamente usados.

### <a name="storage-throttling"></a>Limitação de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Limitação de rede

O provedor de recursos Microsoft. Network aplica os seguintes limites de limitação:

| Operação | Limite |
| --------- | ----- |
| gravação/exclusão (PUT) | 1000 por 5 minutos |
| leitura (GET) | 10000 por 5 minutos |

### <a name="compute-throttling"></a>Limitação de computação

Para obter informações sobre limites de limitação para operações de computação, consulte [Solucionando problemas de erros de limitação de API-Compute](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md).

Para verificar as instâncias de máquina virtual em um conjunto de dimensionamento de máquinas virtuais, use as [operações de conjuntos de dimensionamento de máquinas virtuais](/rest/api/compute/virtualmachinescalesetvms). Por exemplo, use o [conjunto de dimensionamento de máquinas virtuais VMs-List](/rest/api/compute/virtualmachinescalesetvms/list) com parâmetros para verificar o estado de energia de instâncias de máquina virtual. Essa API reduz o número de solicitações.

### <a name="azure-resource-graph-throttling"></a>Limitação do grafo de recursos do Azure

O grafo de recursos do Azure limita o número de solicitações para suas operações. As etapas neste artigo para determinar as solicitações restantes e como responder quando o limite é atingido também se aplicam ao grafo de recursos. No entanto, o grafo de recursos define seu próprio limite e a taxa de redefinição. Para obter mais informações, veja [Limitação no Azure Resource Graph](../governance/resource-graph/overview.md#throttling).

## <a name="request-increase"></a>Aumento da solicitação

Às vezes, os limites de limitação podem ser aumentados. Para ver se os limites de limitação para seu cenário podem ser aumentados, crie uma solicitação de suporte. Os detalhes do seu padrão de chamada serão avaliados.

## <a name="error-code"></a>Código de erro

Quando atingir o limite, receberá o código de estado HTTP **429 demasiados pedidos**. A resposta inclui um valor **Retry-After** , que especifica o número de segundos que o aplicativo deve aguardar (ou dormir) antes de enviar a próxima solicitação. Se enviar um pedido antes do valor de repetição tiver sido decorrido, o pedido não é processado e é devolvido um novo valor de repetição.

Depois de aguardar o tempo especificado, você também pode fechar e reabrir sua conexão com o Azure. Ao redefinir a conexão, você pode se conectar a uma instância diferente do Azure Resource Manager.

Se você estiver usando um SDK do Azure, o SDK poderá ter uma configuração de repetição automática. Para obter mais informações, consulte [diretrizes de repetição para serviços do Azure](/azure/architecture/best-practices/retry-service-specific).

Alguns provedores de recursos retornam 429 para relatar um problema temporário. O problema pode ser uma condição de sobrecarga que não é causada diretamente por sua solicitação. Ou pode representar um erro temporário sobre o estado do recurso de destino ou do recurso dependente. Por exemplo, o provedor de recursos de rede retorna 429 com o código de erro **RetryableErrorDueToAnotherOperation** quando o recurso de destino está bloqueado por outra operação. Para determinar se o erro é proveniente da limitação ou de uma condição temporária, exiba os detalhes do erro na resposta.

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

O provedor de recursos também pode retornar cabeçalhos de resposta com informações sobre as solicitações restantes. Para obter informações sobre cabeçalhos de resposta retornados pelo provedor de recursos de computação, consulte [cabeçalhos de resposta informativo de taxa de chamada](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers).

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

## <a name="next-steps"></a>Passos seguintes

* Para obter um exemplo completo do PowerShell, consulte [verificar limites de Gestor de recursos para uma subscrição](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Para obter mais informações sobre limites e quotas, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md).
* Para saber mais sobre o processamento de solicitações assíncronas do REST, veja [monitorizar operações assíncronas de Azure](resource-manager-async-operations.md).
