---
title: Limites e limitação de pedidos
description: Descreve como usar o estrangulamento com pedidos do Gestor de Recursos Azure quando os limites de subscrição foram atingidos.
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4d387749261747eb9ea1ea26629ade4fe8729856
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80239368"
---
# <a name="throttling-resource-manager-requests"></a>Limitação dos pedidos do Resource Manager

Este artigo descreve como o Gestor de Recursos Azure solicita. Mostra como rastrear o número de pedidos que permanecem antes de atingir o limite, e como responder quando atingiu o limite.

O estrangulamento acontece a dois níveis. O Gestor de Recursos Azure acelera os pedidos para a subscrição e o inquilino. Se o pedido estiver sob os limites de estrangulamento para a subscrição e inquilino, o Gestor de Recursos encaminha o pedido para o fornecedor de recursos. O fornecedor de recursos aplica limites de estrangulamento adaptados às suas operações. A imagem que se segue mostra como a aceleração é aplicada como um pedido vai do utilizador ao Gestor de Recursos Azure e ao fornecedor de recursos.

![Pedido de estrangulamento](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Limites de subscrição e inquilino

Todas as operações de nível de subscrição e de nível de inquilino estão sujeitas a limites de estrangulamento. Os pedidos de subscrição são aqueles que envolvem passar o seu ID de subscrição, como recuperar os grupos de recursos na sua subscrição. Os pedidos de inquilinos não incluem o seu ID de subscrição, como a recuperação de localizações válidas do Azure.

Os limites de estrangulamento predefinidos por hora são mostrados na tabela seguinte.

| Âmbito | Operações | Limite |
| ----- | ---------- | ------- |
| Subscrição | lê | 12000 |
| Subscrição | elimina | 15 000 |
| Subscrição | escreve | 1200 |
| Inquilino | lê | 12000 |
| Inquilino | escreve | 1200 |

Estes limites estão circunscritos ao principal de segurança (utilizador ou aplicação) que faz os pedidos e o ID de subscrição ou ID de inquilino. Se os seus pedidos forem provenientes de mais de um principal de segurança, o limite da subscrição ou do inquilino é superior a 12 000 e 1200 por hora.

Estes limites aplicam-se a cada instância do Gestor de Recursos Azure. Existem múltiplas ocorrências em todas as regiões de Azure, e o Azure Resource Manager é implantado em todas as regiões de Azure.  Assim, na prática, os limites são mais elevados do que estes limites. Os pedidos de um utilizador são geralmente tratados por diferentes instâncias do Gestor de Recursos Azure.

## <a name="resource-provider-limits"></a>Limites de prestador de recursos

Os fornecedores de recursos aplicam os seus próprios limites de estrangulamento. Uma vez que o Gestor de Recursos acelera por id principal e por exemplo do Gestor de Recursos, o fornecedor de recursos pode receber mais pedidos do que os limites de predefinição na secção anterior.

Esta secção discute os limites de estrangulamento de alguns fornecedores de recursos amplamente utilizados.

### <a name="storage-throttling"></a>Estrangulamento de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Limitação da rede

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

## <a name="error-code"></a>Código de erro

Quando atinge o limite, recebe o código de estado **HTTP 429 Muitos pedidos**. A resposta inclui um valor **De Retry-After,** que especifica o número de segundos que a sua aplicação deve esperar (ou dormir) antes de enviar o próximo pedido. Se enviar um pedido antes de decorrer o valor da retry, o seu pedido não é processado e um novo valor de retry é devolvido.

Depois de esperar pelo tempo especificado, também pode fechar e reabrir a sua ligação ao Azure. Ao reajustar a ligação, poderá ligar-se a uma instância diferente do Gestor de Recursos Azure.

Se estiver a utilizar um SDK Azure, o SDK pode ter uma configuração de retry automática. Para mais informações, consulte [a orientação de Retry para os serviços Azure.](/azure/architecture/best-practices/retry-service-specific)

Alguns fornecedores de recursos retornam 429 para reportar um problema temporário. O problema pode ser uma condição de sobrecarga que não é causada diretamente pelo seu pedido. Ou, pode representar um erro temporário sobre o estado do recurso-alvo ou do recurso dependente. Por exemplo, o fornecedor de recursos de rede devolve 429 com o código de erro **RetryableErrorDueToAnotherOperation** quando o recurso-alvo é bloqueado por outra operação. Para determinar se o erro provém de estrangulamento ou condição temporária, veja os detalhes do erro na resposta.

## <a name="remaining-requests"></a>Pedidos restantes

Pode determinar o número de pedidos restantes examinando os cabeçalhos de resposta. Leia os pedidos devolver um valor no cabeçalho para o número de pedidos restantes de leitura. Os pedidos de escrita incluem um valor para o número de pedidos de escrita restantes. A tabela seguinte descreve os cabeçalhos de resposta que pode examinar para esses valores:

| Cabeçalho de resposta | Descrição |
| --- | --- |
| x-ms-ratelimit-restante-subscrição-lê |O âmbito de subscrição lê as restantes leituras. Este valor é devolvido nas operações de leitura. |
| x-ms-ratelimit-restante-subscrição-writes |O âmbito de subscrição escreve o restante. Este valor é devolvido em operações de escrita. |
| x-ms-ratelimite-restante-inquilino-lê-lê |Leituras de alcance do inquilino restantes |
| x-ms-ratelimit-remaining-inquilino-writes |O arrendatário escreve o restante |
| x-ms-ratelimit-restante-pedidos de recursos de subscrição |Pedidos de recurso com âmbito de subscrição restantes.<br /><br />Este valor do cabeçalho só é devolvido se um serviço tiver ultrapassado o limite de predefinição. O Gestor de Recursos adiciona este valor em vez das leituras ou escritos de subscrição. |
| x-ms-ratelimit-remaining-subscrição-entidades-ler |Pedidos de recolha de recursos de âmbito de subscrição restantes.<br /><br />Este valor do cabeçalho só é devolvido se um serviço tiver ultrapassado o limite de predefinição. Este valor fornece o número de pedidos de recolha restantes (recursos da lista). |
| x-ms-ratelimit-restante-inquilino-recursos-pedidos |Pedidos de recursos de recursos com âmbito de arrendamento restantes.<br /><br />Este cabeçalho só é adicionado para pedidos ao nível do inquilino, e apenas se um serviço tiver ultrapassado o limite de incumprimento. O Gestor de Recursos adiciona este valor em vez de o inquilino ler ou escrever. |
| x-ms-ratelimit-remaining-inquilino-entidades-recursos-ler |Pedidos de recolha de recursos com âmbito de arrendamento restantes.<br /><br />Este cabeçalho só é adicionado para pedidos ao nível do inquilino, e apenas se um serviço tiver ultrapassado o limite de incumprimento. |

O fornecedor de recursos também pode devolver cabeçalhos de resposta com informações sobre os restantes pedidos. Para obter informações sobre os cabeçalhos de resposta devolvidos pelo fornecedor de recursos computacionais, consulte [os cabeçalhos](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers)de resposta informativo da taxa de chamada .

## <a name="retrieving-the-header-values"></a>Recuperação dos valores do cabeçalho

Recuperar estes valores de cabeçalho no seu código ou script não é diferente de recuperar qualquer valor de cabeçalho. 

Por exemplo, em **C#**, você recupera o valor cabeçalho de um objeto **HttpWebResponse** chamado **resposta** com o seguinte código:

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

O que devolve muitos valores, incluindo o seguinte valor de resposta:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Para obter limites de escrita, use uma operação de escrita: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

O que devolve muitos valores, incluindo os seguintes valores:

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

O que devolve muitos valores, incluindo os seguintes valores:

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

Para obter limites de escrita, use uma operação de escrita: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

O que devolve muitos valores, incluindo os seguintes valores:

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

* Para obter um exemplo completo da PowerShell, consulte limites de gestão de [recursos para uma subscrição](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Para obter mais informações sobre limites e quotas, consulte os limites de subscrição e serviço do [Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md)
* Para saber lidar com pedidos de REST assíncronos, consulte as operações de [Track assynchronous Azure](async-operations.md).
