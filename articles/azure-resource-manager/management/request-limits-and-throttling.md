---
title: Limites e limitação de pedidos
description: Descreve como usar o estrangulamento com os pedidos do Azure Resource Manager quando os limites de subscrição foram atingidos.
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4d387749261747eb9ea1ea26629ade4fe8729856
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80239368"
---
# <a name="throttling-resource-manager-requests"></a>Limitação dos pedidos do Resource Manager

Este artigo descreve como o Azure Resource Manager acelera os pedidos. Mostra como rastrear o número de pedidos que permanecem antes de atingir o limite, e como responder quando se chega ao limite.

A aceleração acontece a dois níveis. Azure Resource Manager acelera os pedidos para a subscrição e inquilino. Se o pedido estiver abaixo dos limites de estrangulamento para a subscrição e inquilino, o Gestor de Recursos encaminha o pedido ao fornecedor de recursos. O fornecedor de recursos aplica limites de estrangulamento adaptados às suas operações. A imagem que se segue mostra como o estrangulamento é aplicado à medida que um pedido vai do utilizador para o Azure Resource Manager e o fornecedor de recursos.

![Pedido de estrangulamento](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Limites de assinatura e inquilino

Todas as operações de nível de subscrição e de nível de inquilino estão sujeitas a limites de estrangulamento. Os pedidos de subscrição são aqueles que envolvem a passagem do seu ID de subscrição, como a recuperação dos grupos de recursos na sua subscrição. Os pedidos do inquilino não incluem o seu ID de subscrição, como a recuperação de localizações válidas do Azure.

Os limites de estrangulamento por hora por padrão são indicados na tabela seguinte.

| Âmbito | Operações | Limite |
| ----- | ---------- | ------- |
| Subscrição | lê | 12000 |
| Subscrição | apaga | 15 000 |
| Subscrição | escreve | 1200 |
| Inquilino | lê | 12000 |
| Inquilino | escreve | 1200 |

Estes limites estão circunscritos ao principal de segurança (utilizador ou aplicação) que faz os pedidos e o ID de subscrição ou ID de inquilino. Se os seus pedidos forem provenientes de mais de um principal de segurança, o limite da subscrição ou do inquilino é superior a 12 000 e 1200 por hora.

Estes limites aplicam-se a cada instância do Gestor de Recursos Azure. Existem múltiplas instâncias em todas as regiões do Azure, e o Azure Resource Manager é implantado em todas as regiões do Azure.  Assim, na prática, os limites são superiores a estes limites. Os pedidos de um utilizador são geralmente tratados por diferentes instâncias do Azure Resource Manager.

## <a name="resource-provider-limits"></a>Limites do fornecedor de recursos

Os fornecedores de recursos aplicam os seus próprios limites de estrangulamento. Uma vez que o Gestor de Recursos acelera pelo ID principal e por exemplo do Gestor de Recursos, o fornecedor de recursos pode receber mais pedidos do que os limites de incumprimento na secção anterior.

Esta secção discute os limites de estrangulamento de alguns fornecedores de recursos amplamente utilizados.

### <a name="storage-throttling"></a>Limitação de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Limitação da rede

O fornecedor de recursos Microsoft.Network aplica os seguintes limites de aceleração:

| Operação | Limite |
| --------- | ----- |
| escrever / apagar (PUT) | 1000 por 5 minutos |
| ler (GET) | 10000 por 5 minutos |

### <a name="compute-throttling"></a>Estrangulamento computacional

Para obter informações sobre os limites de estrangulamento para operações de computação, consulte [erros de estrangulamento da API de resolução de problemas - Compute](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md).

Para verificar as instâncias da máquina virtual dentro de um conjunto de escala de máquina virtual, utilize as [operações de conjuntos de escala de máquina virtual](/rest/api/compute/virtualmachinescalesetvms). Por exemplo, utilize os [VMs de escala de máquina virtual - Lista](/rest/api/compute/virtualmachinescalesetvms/list) com parâmetros para verificar o estado de potência das instâncias da máquina virtual. Esta API reduz o número de pedidos.

### <a name="azure-resource-graph-throttling"></a>Aceleração do gráfico de recursos Azure

[O Azure Resource Graph](../../governance/resource-graph/overview.md) limita o número de pedidos às suas operações. Os passos deste artigo para determinar os pedidos restantes e como responder quando o limite é atingido também se aplicam ao Gráfico de Recursos. No entanto, o Gráfico de Recursos define o seu próprio limite e taxa de reset. Para obter mais informações, consulte [os cabeçalhos de estrangulamento do Gráfico de Recurso](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

## <a name="error-code"></a>Código de erro

Quando atingir o limite, recebe o código de estado HTTP **429 Demasiados pedidos**. A resposta inclui um valor **Retry-After,** que especifica o número de segundos que a sua aplicação deve esperar (ou dormir) antes de enviar o próximo pedido. Se enviar um pedido antes de decorrer o valor de retíria, o seu pedido não é processado e um novo valor de relemissão é devolvido.

Depois de esperar por tempo especificado, também pode fechar e reabrir a sua ligação ao Azure. Ao reiniciar a ligação, pode ligar-se a uma instância diferente do Gestor de Recursos Azure.

Se estiver a utilizar um Azure SDK, o SDK pode ter uma configuração de relagem automática. Para mais informações, consulte [a orientação da Retry para os serviços da Azure.](/azure/architecture/best-practices/retry-service-specific)

Alguns fornecedores de recursos devolvem 429 para reportar um problema temporário. O problema pode ser uma condição de sobrecarga que não é diretamente causada pelo seu pedido. Ou pode representar um erro temporário sobre o estado do recurso-alvo ou recurso dependente. Por exemplo, o fornecedor de recursos de rede devolve 429 com o código de erro **RetripableErrorDueToAnotherOperation** quando o recurso-alvo é bloqueado por outra operação. Para determinar se o erro provém de estrangulamento ou de uma condição temporária, consulte os detalhes do erro na resposta.

## <a name="remaining-requests"></a>Pedidos remanescentes

Pode determinar o número de pedidos restantes examinando os cabeçalhos de resposta. Os pedidos de leitura devolvem um valor no cabeçalho para o número de pedidos restantes de leitura. Os pedidos de escrita incluem um valor para o número de pedidos de escrita restantes. A tabela a seguir descreve os cabeçalhos de resposta que pode examinar para estes valores:

| Cabeçalho de resposta | Descrição |
| --- | --- |
| x-ms-ratelimit-restante-subscrição-reads |A subscrição lê as leituras restantes. Este valor é devolvido em operações de leitura. |
| x-ms-ratelimit-restante-subscrição-writes |A subscrição de âmbito escreve o restante. Este valor é devolvido em operações de escrita. |
| x-ms-ratelimit-remaining-tenant-reads |Inquilino âmbito leituras restantes |
| x-ms-ratelimit-remaining-tenant-writes |Inquilino com âmbito escritos restantes |
| x-ms-ratelimit-remaining-subscrição-pedidos de recursos |Pedidos de tipo de recurso de âmbito de subscrição restantes.<br /><br />Este valor do cabeçalho só é devolvido se um serviço ultrapassar o limite por defeito. O Gestor de Recursos adiciona este valor em vez das leituras ou escritas de subscrição. |
| x-ms-ratelimit-remaining-subscrição-recursos-entidades-ler |Pedidos de recolha de tipo de recurso de assinaturas restantes.<br /><br />Este valor do cabeçalho só é devolvido se um serviço ultrapassar o limite por defeito. Este valor fornece o número de pedidos restantes de recolha (recursos de lista). |
| x-ms-ratelimit-remaining-inquilino-recursos-pedidos |O inquilino examinou os pedidos do tipo de recurso restantes.<br /><br />Este cabeçalho só é adicionado para pedidos ao nível do inquilino, e apenas se um serviço tiver ultrapassado o limite de incumprimento. O Gestor de Recursos adiciona este valor em vez do inquilino ler ou escrever. |
| x-ms-ratelimit-remaining-inquilino-recursos-entidades-ler |O inquilino examinou os pedidos de recolha do tipo de recurso restantes.<br /><br />Este cabeçalho só é adicionado para pedidos ao nível do inquilino, e apenas se um serviço tiver ultrapassado o limite de incumprimento. |

O fornecedor de recursos também pode devolver cabeçalhos de resposta com informações sobre os pedidos restantes. Para obter informações sobre os cabeçalhos de resposta devolvidos pelo fornecedor de recursos Compute, consulte [os cabeçalhos de resposta informativa da taxa de chamada](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Recuperação dos valores do cabeçalho

Recuperar estes valores do cabeçalho no seu código ou script não é diferente de recuperar qualquer valor do cabeçalho. 

Por exemplo, em **C#**, obtém-se o valor do cabeçalho a partir de um objeto **httpWebResponse** denominado **resposta** com o seguinte código:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

Em **PowerShell,** recupera o valor do cabeçalho de uma operação Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Para obter um exemplo completo do PowerShell, consulte [limites do gestor de recursos para uma subscrição](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Se quiser ver os restantes pedidos de depurar, pode fornecer o parâmetro **-Debug** no seu cmdlet **PowerShell.**

```powershell
Get-AzResourceGroup -Debug
```

Que devolve muitos valores, incluindo o seguinte valor de resposta:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Para obter limites de escrita, utilize uma operação de escrita: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Que devolve muitos valores, incluindo os seguintes valores:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

Em **Azure CLI,** recupere o valor do cabeçalho utilizando a opção mais verbosa.

```azurecli
az group list --verbose --debug
```

Que devolve muitos valores, incluindo os seguintes valores:

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

Para obter limites de escrita, utilize uma operação de escrita: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Que devolve muitos valores, incluindo os seguintes valores:

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

## <a name="next-steps"></a>Passos seguintes

* Para obter um exemplo completo do PowerShell, consulte [limites do gestor de recursos para uma subscrição](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Para obter mais informações sobre limites e quotas, consulte [os limites de subscrição e serviços, quotas e restrições de subscrição da Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)
* Para aprender a lidar com pedidos assíncronos de REST, consulte [as operações de Azure assíncrona .](async-operations.md)
