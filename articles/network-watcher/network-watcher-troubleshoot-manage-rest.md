---
title: Troubleshoot VNET Gateway and Connections - Azure REST API
titleSuffix: Azure Network Watcher
description: Esta página explica como resolver problemas de Gateways e Conexões de Rede Virtual com O Observador da Rede Azure usando o REST
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: ab9f7fd95d7081b66e05dfd3d6a5ef47eb3c4053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840677"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Portal de rede virtual de resolução de problemas e conexões usando o Vigilante da Rede Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

O Network Watcher fornece muitas capacidades no que diz respeito à compreensão dos seus recursos de rede em Azure. Uma destas capacidades é a resolução de problemas de recursos. A resolução de problemas de recursos pode ser chamada através do portal PowerShell, CLI ou REST API. Quando chamado, o Observador de Rede inspeciona a saúde de um Gateway de Rede Virtual ou de uma Ligação e devolve as suas descobertas.

Este artigo leva-o através das diferentes tarefas de gestão que estão atualmente disponíveis para resolução de problemas de recursos.

- [**Resolução de problemas um portal de rede virtual**](#troubleshoot-a-virtual-network-gateway)
- [**Resolução de problemas uma ligação**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Antes de começar

O ARMclient é usado para chamar a API REST usando powerShell. ARMClient é encontrado em chocolate na [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que já seguiu os passos na [Create a Network Watcher](network-watcher-create.md) para criar um Observador de Rede.

Para uma lista de visitas de tipos de gateway suportados, os tipos de [Gateway suportados](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Descrição geral

A resolução de problemas do Network Watcher fornece os problemas de resolução de problemas que surgem com gateways e ligações da Rede Virtual. Quando é feito um pedido para a resolução de problemas de recursos, os registos são consultados e inspecionados. Quando a inspeção estiver concluída, os resultados são devolvidos. Os pedidos de API são pedidos de longa duração, que podem demorar vários minutos a devolver um resultado. Os registos são guardados num contentor numa conta de armazenamento.

## <a name="log-in-with-armclient"></a>Iniciar sessão com a ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Resolução de problemas um portal de rede virtual


### <a name="post-the-troubleshoot-request"></a>POST o pedido de resolução de problemas

O exemplo que se segue questiona o estado de um gateway da Rede Virtual.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Uma vez que esta operação está em curso há muito tempo, o URI para consulta da operação e o URI para o resultado é devolvido no cabeçalho de resposta, como mostra a seguinte resposta:

**Valores Importantes**

* **Azure-AsyncOperation** - Esta propriedade contém o URI para consultar a operação de resolução de problemas de Async
* **Localização** - Esta propriedade contém o URI onde os resultados estão quando a operação está concluída

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Consulta da operação de asincronização para conclusão

Utilize as operações URI para consultar o progresso da operação, como se pode ver no seguinte exemplo:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Enquanto a operação está em curso, a resposta mostra **inProgress** como visto no seguinte exemplo:

```json
{
  "status": "InProgress"
}
```

Quando a operação estiver concluída, o estado muda para **Succeed .**

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Obter os resultados

Uma vez que o estado devolvido seja **bem sucedido,** ligue para um método GET na operaçãoResultURI para recuperar os resultados.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

As seguintes respostas são exemplos de uma resposta típica degradada devolvida ao consultar os resultados da resolução de problemas de uma porta de entrada. Veja [compreender os resultados](#understanding-the-results) para obter esclarecimentos sobre o que significam os imóveis na resposta.

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Ligações de resolução de problemas

O exemplo que se segue questiona o estado de uma Ligação.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> A operação de resolução de problemas não pode ser executada paralelamente numa Ligação e nos respetivos portões. A operação deve ser concluída antes de a executar no recurso anterior.

Uma vez que se trata de uma transação de longa duração, no cabeçalho de resposta, o URI para consulta da operação e o URI para o resultado é devolvido como mostra a seguinte resposta:

**Valores Importantes**

* **Azure-AsyncOperation** - Esta propriedade contém o URI para consultar a operação de resolução de problemas de Async
* **Localização** - Esta propriedade contém o URI onde os resultados estão quando a operação está concluída

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Consulta da operação de asincronização para conclusão

Utilize as operações URI para consultar o progresso da operação, como se pode ver no seguinte exemplo:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Enquanto a operação está em curso, a resposta mostra **inProgress** como visto no seguinte exemplo:

```json
{
  "status": "InProgress"
}
```

Quando a operação estiver concluída, o estado muda para **Succeed .**

```json
{
  "status": "Succeeded"
}
```

As seguintes respostas são exemplos de uma resposta típica devolvida ao consultar os resultados de resolução de problemas de uma Ligação.

### <a name="retrieve-the-results"></a>Obter os resultados

Uma vez que o estado devolvido seja **bem sucedido,** ligue para um método GET na operaçãoResultURI para recuperar os resultados.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

As seguintes respostas são exemplos de uma resposta típica devolvida ao consultar os resultados de resolução de problemas de uma Ligação.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Compreender os resultados

O texto de ação fornece orientações gerais sobre a forma de resolver a questão. Se for possível tomar uma medida para a questão, é fornecida uma ligação com orientações adicionais. No caso de não existir orientação adicional, a resposta fornece o url para abrir um caso de suporte.  Para mais informações sobre as propriedades da resposta e o que está incluído, visite a visão geral do [Network Watcher Troubleshoot](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre o download de ficheiros de contas de armazenamento azure, consulte o Get started com o [armazenamento Azure Blob utilizando .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é o Storage Explorer. Mais informações sobre o Storage Explorer podem ser encontradas aqui no seguinte link: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Passos seguintes

Se foram alteradas as definições que impedem a conectividade VPN, consulte [a Manage Network Security Groups](../virtual-network/manage-network-security-group.md) para localizar o grupo de segurança da rede e as regras de segurança que possam estar em causa.
