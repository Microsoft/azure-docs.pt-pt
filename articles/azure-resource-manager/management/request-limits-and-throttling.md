---
title: Limites e limitação de pedidos
description: Descreve como utilizar a limitação com pedidos do Azure Resource Manager, quando atingiu os limites de subscrição.
ms.topic: conceptual
ms.date: 10/26/2019
ms.custom: seodec18
ms.openlocfilehash: eabc621ce02d4f30c5efb5bcef2635ea0e8dbcb2
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944042"
---
# <a name="throttling-resource-manager-requests"></a>Limitar pedidos do Resource Manager

Este artigo descreve como o Gestor de Recursos Azure solicita. Mostra como rastrear o número de pedidos que permanecem antes de atingir o limite, e como responder quando atingiu o limite.

O estrangulamento acontece a dois níveis. O Gestor de Recursos Azure acelera os pedidos para a subscrição e o inquilino. Se o pedido estiver sob os limites de estrangulamento para a subscrição e inquilino, o Gestor de Recursos encaminha o pedido para o fornecedor de recursos. O fornecedor de recursos aplica limites de estrangulamento adaptados às suas operações. A imagem que se segue mostra como a aceleração é aplicada como um pedido vai do utilizador ao Gestor de Recursos Azure e ao fornecedor de recursos.

![Pedido de estrangulamento](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Limites de subscrição e inquilino

Todas as operações de nível de subscrição e de nível de inquilino estão sujeitas a limites de estrangulamento. Os pedidos de subscrição são aqueles que envolvem passar o seu ID de subscrição, como recuperar os grupos de recursos na sua subscrição. Pedidos de inquilino não incluem o ID da subscrição, como a obtenção de localizações do Azure válidas.

Os limites de estrangulamento predefinidos por hora são mostrados na tabela seguinte.

| Âmbito | Operações | Limite |
| ----- | ---------- | ------- |
| Subscrição | lê | 12000 |
| Subscrição | elimina | 15 000 |
| Subscrição | escreve | 1200 |
| Inquilino | lê | 12000 |
| Inquilino | escreve | 1200 |

Estes limites estão circunscritos ao principal de segurança (utilizador ou aplicação) que faz os pedidos e o ID de subscrição ou ID de inquilino. Se os seus pedidos forem provenientes de mais de um principal de segurança, o limite da subscrição ou do inquilino é superior a 12 000 e 1200 por hora.

Estes limites se aplicam a cada instância do Azure Resource Manager. Existem várias instâncias em cada região do Azure e Azure Resource Manager é implementado para todas as regiões do Azure.  Assim, na prática, os limites são mais elevados do que estes limites. Os pedidos de um utilizador são geralmente tratados por diferentes instâncias do Gestor de Recursos Azure.

## <a name="resource-provider-limits"></a>Limites de prestador de recursos

Os fornecedores de recursos aplicam os seus próprios limites de estrangulamento. Uma vez que o Gestor de Recursos acelera por id principal e por exemplo do Gestor de Recursos, o fornecedor de recursos pode receber mais pedidos do que os limites de predefinição na secção anterior.

Esta secção discute os limites de estrangulamento de alguns fornecedores de recursos amplamente utilizados.

### <a name="storage-throttling"></a>Estrangulamento de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Estrangulamento da rede

O fornecedor de recursos Microsoft.Network aplica os seguintes limites de aceleração:

| Operação | Limite |
| --------- | ----- |
| escrever / eliminar (PUT) | 1000 por 5 minutos |
| ler (GET) | 10000 por 5 minutos |

### <a name="compute-throttling"></a>Estrangulamento computacional

Para obter informações sobre os limites de estrangulamento das operações de computação, consulte erros de estrangulamento da [API de resolução de problemas - Compute](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md).

Para verificar as instâncias de máquinas virtuais dentro de um conjunto de escala de máquina virtual, utilize as operações de [conjuntos](/rest/api/compute/virtualmachinescalesetvms)de escala de máquina virtual . Por exemplo, utilize os [VMs conjuntos](/rest/api/compute/virtualmachinescalesetvms/list) de escala de máquina virtual - Lista com parâmetros para verificar o estado de alimentação das instâncias das máquinas virtuais. Esta API reduz o número de pedidos.

### <a name="azure-resource-graph-throttling"></a>Estrangulamento do gráfico de recursos azure

[O Azure Resource Graph](../../governance/resource-graph/overview.md) limita o número de pedidos às suas operações. As etapas deste artigo para determinar os pedidos restantes e como responder quando o limite é atingido também se aplicam ao Graph de Recursos. No entanto, o Resource Graph define o seu próprio limite e taxa de reset. Para mais informações, consulte [os cabeçalhos](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers)de estrangulamento do Resource Graph .

## <a name="request-increase"></a>Aumento de pedidos

Às vezes, os limites do acelerador podem ser aumentados. Para ver se os limites de estrangulamento para o seu cenário podem ser aumentados, crie um pedido de apoio. Os detalhes do seu padrão de chamada serão avaliados.

## <a name="error-code"></a>Código de erro

Quando atinge o limite, recebe o código de estado **HTTP 429 Muitos pedidos**. A resposta inclui um valor **De Retry-After,** que especifica o número de segundos que a sua aplicação deve esperar (ou dormir) antes de enviar o próximo pedido. Se enviar um pedido antes do valor de repetição tiver sido decorrido, o pedido não é processado e é devolvido um novo valor de repetição.

Depois de esperar pelo tempo especificado, também pode fechar e reabrir a sua ligação ao Azure. Ao reajustar a ligação, poderá ligar-se a uma instância diferente do Gestor de Recursos Azure.

Se estiver a utilizar um SDK Azure, o SDK pode ter uma configuração de retry automática. Para mais informações, consulte [a orientação de Retry para os serviços Azure.](/azure/architecture/best-practices/retry-service-specific)

Alguns fornecedores de recursos retornam 429 para reportar um problema temporário. O problema pode ser uma condição de sobrecarga que não é causada diretamente pelo seu pedido. Ou, pode representar um erro temporário sobre o estado do recurso-alvo ou do recurso dependente. Por exemplo, o fornecedor de recursos de rede devolve 429 com o código de erro **RetryableErrorDueToAnotherOperation** quando o recurso-alvo é bloqueado por outra operação. Para determinar se o erro provém de estrangulamento ou condição temporária, veja os detalhes do erro na resposta.

## <a name="remaining-requests"></a>Pedidos restantes

Pode determinar o número de pedidos restantes, examinando os cabeçalhos de resposta. Leia os pedidos devolver um valor no cabeçalho para o número de pedidos restantes de leitura. Os pedidos de escrita incluem um valor para o número de pedidos de escrita restantes. A tabela seguinte descreve os cabeçalhos de resposta, que pode examinar para esses valores:

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

O fornecedor de recursos também pode devolver cabeçalhos de resposta com informações sobre os restantes pedidos. Para obter informações sobre os cabeçalhos de resposta devolvidos pelo fornecedor de recursos computacionais, consulte [os cabeçalhos](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers)de resposta informativo da taxa de chamada .

## <a name="retrieving-the-header-values"></a>Recuperar os valores de cabeçalho

A obter estes valores de cabeçalho no seu código ou script não é diferente de qualquer valor de cabeçalho a obter. 

Por exemplo, **C#** em , você recupera o valor cabeçalho de um objeto **HttpWebResponse** chamado **resposta** com o seguinte código:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

No **PowerShell,** recupera-se o valor do cabeçalho de uma operação Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Para obter um exemplo completo da PowerShell, consulte limites de gestão de [recursos para uma subscrição](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Se quiser ver os restantes pedidos de depuração, pode fornecer o parâmetro **-Debug** no seu cmdlet **PowerShell.**

```powershell
Get-AzResourceGroup -Debug
```

Que retorna valores de muitos, incluindo o seguinte valor de resposta:

```output
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

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

No **Azure CLI,** recupera-se o valor do cabeçalho utilizando a opção mais verbosa.

```azurecli
az group list --verbose --debug
```

Que retorna valores de muitos, incluindo os seguintes valores:

```output
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

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Passos Seguintes

* Para obter um exemplo completo da PowerShell, consulte limites de gestão de [recursos para uma subscrição](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Para obter mais informações sobre limites e quotas, consulte os limites de subscrição e serviço do [Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md)
* Para saber lidar com pedidos de REST assíncronos, consulte as operações de [Track assynchronous Azure](async-operations.md).
