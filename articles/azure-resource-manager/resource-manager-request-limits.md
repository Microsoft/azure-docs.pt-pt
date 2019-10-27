---
title: Limites de solicitação e limitação-Azure Resource Manager
description: Descreve como usar a limitação com solicitações de Azure Resource Manager quando os limites de assinatura forem atingidos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 7d53e5749385499113d0dc5261398561d82347a0
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965557"
---
# <a name="throttling-resource-manager-requests"></a>Limitação de solicitações do Gerenciador de recursos

Este artigo descreve como Azure Resource Manager limita as solicitações. Ele mostra como acompanhar o número de solicitações que permanecem antes de atingir o limite e como responder quando você atingir o limite.

A limitação ocorre em dois níveis. Azure Resource Manager limita as solicitações para a assinatura e o locatário. Se a solicitação estiver sob os limites de limitação para a assinatura e o locatário, o Resource Manager roteia a solicitação para o provedor de recursos. O provedor de recursos aplica limites de limitação que são adaptados para suas operações. A imagem a seguir mostra como a limitação é aplicada, uma vez que uma solicitação passa do usuário para Azure Resource Manager e o provedor de recursos.

![Limitação de solicitação](./media/resource-manager-request-limits/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Limites de assinatura e locatário

Todas as operações de nível de assinatura e de locatário estão sujeitas a limites de limitação. As solicitações de assinatura são aquelas que envolvem a passagem da sua ID de assinatura, como a recuperação dos grupos de recursos em sua assinatura. As solicitações de locatário não incluem sua ID de assinatura, como recuperar locais válidos do Azure.

Os limites de limitação padrão por hora são mostrados na tabela a seguir.

| Âmbito | Operations | Limite |
| ----- | ---------- | ------- |
| Subscrição | pareça | 12000 |
| Subscrição | excluído | 15 000 |
| Subscrição | registra | 1200 |
| Inquilino | pareça | 12000 |
| Inquilino | registra | 1200 |

Estes limites estão circunscritos ao principal de segurança (utilizador ou aplicação) que faz os pedidos e o ID de subscrição ou ID de inquilino. Se os seus pedidos forem provenientes de mais de um principal de segurança, o limite da subscrição ou do inquilino é superior a 12 000 e 1200 por hora.

Esses limites se aplicam a cada instância de Azure Resource Manager. Há várias instâncias em cada região do Azure e Azure Resource Manager é implantada em todas as regiões do Azure.  Portanto, na prática, os limites são maiores que esses limites. As solicitações de um usuário geralmente são tratadas por instâncias diferentes do Azure Resource Manager.

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

Quando atingir o limite, você receberá o código de status HTTP **429 número excessivo de solicitações**. A resposta inclui um valor **Retry-After** , que especifica o número de segundos que o aplicativo deve aguardar (ou dormir) antes de enviar a próxima solicitação. Se você enviar uma solicitação antes que o valor de repetição tenha decorrido, sua solicitação não será processada e um novo valor de nova tentativa será retornado.

Depois de aguardar o tempo especificado, você também pode fechar e reabrir sua conexão com o Azure. Ao redefinir a conexão, você pode se conectar a uma instância diferente do Azure Resource Manager.

Se você estiver usando um SDK do Azure, o SDK poderá ter uma configuração de repetição automática. Para obter mais informações, consulte [diretrizes de repetição para serviços do Azure](/azure/architecture/best-practices/retry-service-specific).

Alguns provedores de recursos retornam 429 para relatar um problema temporário. O problema pode ser uma condição de sobrecarga que não é causada diretamente por sua solicitação. Ou pode representar um erro temporário sobre o estado do recurso de destino ou do recurso dependente. Por exemplo, o provedor de recursos de rede retorna 429 com o código de erro **RetryableErrorDueToAnotherOperation** quando o recurso de destino está bloqueado por outra operação. Para determinar se o erro é proveniente da limitação ou de uma condição temporária, exiba os detalhes do erro na resposta.

## <a name="remaining-requests"></a>Solicitações restantes

Você pode determinar o número de solicitações restantes examinando os cabeçalhos de resposta. As solicitações de leitura retornam um valor no cabeçalho para o número de solicitações de leitura restantes. As solicitações de gravação incluem um valor para o número de solicitações de gravação restantes. A tabela a seguir descreve os cabeçalhos de resposta que você pode examinar para esses valores:

| Cabeçalho de resposta | Descrição |
| --- | --- |
| x-MS-ratelimit-restante-assinatura-leituras |Leituras com escopo de assinatura restantes. Esse valor é retornado em operações de leitura. |
| x-MS-ratelimit-restante-assinatura-gravações |Gravações no escopo da assinatura restantes. Esse valor é retornado em operações de gravação. |
| x-MS-ratelimit-restantes-locatário-leituras |Leituras no escopo do locatário restantes |
| x-MS-ratelimit-restantes-locatário-gravações |Gravações no escopo do locatário restantes |
| x-MS-ratelimit-restantes-Subscription-Resource-requests |Solicitações de tipo de recurso com escopo de assinatura restantes.<br /><br />Esse valor de cabeçalho só será retornado se um serviço tiver substituído o limite padrão. O Gerenciador de recursos adiciona esse valor em vez das leituras ou gravações da assinatura. |
| x-MS-ratelimit-restante-assinatura-recurso-entidades-leitura |Solicitações de coleção de tipo de recurso com escopo de assinatura restantes.<br /><br />Esse valor de cabeçalho só será retornado se um serviço tiver substituído o limite padrão. Esse valor fornece o número de solicitações de coleta restantes (listar recursos). |
| x-MS-ratelimit-restantes-locatário-Resource-requests |Solicitações de tipo de recurso no escopo do locatário restantes.<br /><br />Esse cabeçalho só é adicionado para solicitações no nível do locatário e somente se um serviço tiver substituído o limite padrão. O Gerenciador de recursos adiciona esse valor em vez das leituras ou gravações do locatário. |
| x-MS-ratelimit-restantes-locatário-Resource-Entities-Read |Solicitações de coleta de tipo de recurso no escopo do locatário restantes.<br /><br />Esse cabeçalho só é adicionado para solicitações no nível do locatário e somente se um serviço tiver substituído o limite padrão. |

O provedor de recursos também pode retornar cabeçalhos de resposta com informações sobre as solicitações restantes. Para obter informações sobre cabeçalhos de resposta retornados pelo provedor de recursos de computação, consulte [cabeçalhos de resposta informativo de taxa de chamada](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Recuperando os valores de cabeçalho

Recuperar esses valores de cabeçalho em seu código ou script não é diferente de recuperar qualquer valor de cabeçalho. 

Por exemplo, no **C#** , você recupera o valor do cabeçalho de um objeto **HttpWebResponse** chamado **Response** com o seguinte código:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

No **PowerShell**, você recupera o valor do cabeçalho de uma operação Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Para obter um exemplo completo do PowerShell, consulte [verificar limites do Gerenciador de recursos para uma assinatura](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Se você quiser ver as solicitações restantes de depuração, poderá fornecer o parâmetro **-debug** em seu cmdlet do **PowerShell** .

```powershell
Get-AzResourceGroup -Debug
```

Que retorna muitos valores, incluindo o seguinte valor de resposta:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Para obter limites de gravação, use uma operação de gravação: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Que retorna muitos valores, incluindo os seguintes valores:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

No **CLI do Azure**, você recupera o valor do cabeçalho usando a opção mais detalhada.

```azurecli
az group list --verbose --debug
```

Que retorna muitos valores, incluindo os seguintes valores:

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

Para obter limites de gravação, use uma operação de gravação: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Que retorna muitos valores, incluindo os seguintes valores:

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

* Para obter um exemplo completo do PowerShell, consulte [verificar limites do Gerenciador de recursos para uma assinatura](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Para obter mais informações sobre limites e cotas, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
* Para saber mais sobre como lidar com solicitações REST assíncronas, consulte [rastrear operações assíncronas do Azure](resource-manager-async-operations.md).
