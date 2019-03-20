---
title: Resolver problemas de Gateway de rede Virtual e ligações com o observador de rede do Azure - REST | Documentos da Microsoft
description: Esta página explica como resolver problemas de Gateways de rede Virtual e ligações com o observador de rede do Azure com REST
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e4d5f195-b839-4394-94ef-a04192766e55
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 0889476d84bbeaf620d9fc952efe102601a1eaf8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57857849"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Resolver problemas de gateway de rede Virtual e ligações com o observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI do Azure](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

Observador de rede oferece muitos recursos que diz respeito à Noções básicas sobre os recursos de rede no Azure. Um desses recursos é o recurso de resolução de problemas. Resolução de problemas de recursos pode ser chamada através do portal, o PowerShell, a CLI ou a REST API. Quando chamado, o observador de rede inspeciona o estado de funcionamento de um Gateway de rede Virtual ou uma ligação e devolve conclusões.

Este artigo orienta-o por tarefas de gestão diferentes que estão atualmente disponíveis para a resolução de problemas de recursos.

- [**Resolver problemas relacionados com um gateway de rede Virtual**](#troubleshoot-a-virtual-network-gateway)
- [**Resolver problemas de uma ligação**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Antes de começar

ARMclient é utilizada para chamar a API de REST com o PowerShell. ARMClient encontra-se no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

Para obter uma lista de visita de tipos de gateway suportados [tipos de Gateway suportados](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Descrição geral

Solução de problemas de observador de rede fornece a capacidade de resolver os problemas que surgem com ligações e gateways de rede Virtual. Quando é efetuado um pedido para o recurso de resolução de problemas, registos estão a consultar e inspecioná-lo. Quando a inspeção estiver concluída, os resultados são devolvidos. Os pedidos de resolução de problemas API são longos executando solicitações, o que podem demorar vários minutos para retornar um resultado. Os registos são armazenados num contentor numa conta de armazenamento.

## <a name="log-in-with-armclient"></a>Inicie sessão com ARMClient

```PowerShell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Resolver problemas relacionados com um gateway de rede Virtual


### <a name="post-the-troubleshoot-request"></a>PUBLICAR o pedido de resolução de problemas

O exemplo seguinte consulta o estado de um gateway de rede Virtual.

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

Uma vez que esta operação é longa em execução, o URI para consultar a operação e o URI para o resultado é devolvido no cabeçalho de resposta, conforme mostrado na seguinte resposta:

**Valores importantes**

* **Azure-AsyncOperation** -esta propriedade contém o URI para o assíncrono de resolver problemas relacionados com a operação de consulta
* **Localização** -esta propriedade contém o URI onde os resultados são quando a operação for concluída

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

### <a name="query-the-async-operation-for-completion"></a>A operação assíncrona para a conclusão de consulta

Utilize as operações de URI para a consulta para conhecer o progresso da operação, como visto no exemplo a seguir:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Enquanto a operação estiver em curso, a resposta mostra **InProgress** como visto no exemplo a seguir:

```json
{
  "status": "InProgress"
}
```

Quando a operação for concluída o estado muda para **bem-sucedido**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Obter os resultados

Assim que o estado devolvido é **bem-sucedido**, chamar um método de introdução sobre a operationresult ainda URI para recuperar os resultados.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

As respostas seguintes são exemplos de uma resposta de degradação típica devolvido ao consultar os resultados de um gateway de resolução de problemas. Ver [Noções básicas sobre os resultados](#understanding-the-results) para obter esclarecimentos adicionais sobre o que significa que as propriedades na resposta.

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


## <a name="troubleshoot-connections"></a>Resolver problemas de ligações

O exemplo seguinte consulta o estado de uma ligação.

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
> A operação de resolução de problemas não pode ser executada em paralelo numa conexão e seus gateways correspondentes. A operação tem de concluir antes de executá-lo no recurso anterior.

Como se trata de uma transação de longa execução, no cabeçalho de resposta, o URI para consultar a operação e o URI para o resultado é devolvido como mostrado na seguinte resposta:

**Valores importantes**

* **Azure-AsyncOperation** -esta propriedade contém o URI para o assíncrono de resolver problemas relacionados com a operação de consulta
* **Localização** -esta propriedade contém o URI onde os resultados são quando a operação for concluída

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

### <a name="query-the-async-operation-for-completion"></a>A operação assíncrona para a conclusão de consulta

Utilize as operações de URI para a consulta para conhecer o progresso da operação, como visto no exemplo a seguir:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Enquanto a operação estiver em curso, a resposta mostra **InProgress** como visto no exemplo a seguir:

```json
{
  "status": "InProgress"
}
```

Quando a operação estiver concluída, o estado muda para **bem-sucedido**.

```json
{
  "status": "Succeeded"
}
```

As respostas seguintes são exemplos de uma resposta típica devolvido ao consultar os resultados de uma ligação de resolução de problemas.

### <a name="retrieve-the-results"></a>Obter os resultados

Assim que o estado devolvido é **bem-sucedido**, chamar um método de introdução sobre a operationresult ainda URI para recuperar os resultados.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

As respostas seguintes são exemplos de uma resposta típica devolvido ao consultar os resultados de uma ligação de resolução de problemas.

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

## <a name="understanding-the-results"></a>Noções básicas sobre os resultados

O texto de ação fornece orientações gerais sobre como resolver o problema. Se uma ação possa ser executada para o problema, é fornecida uma ligação com orientações adicionais. No caso em que não existe nenhuma orientação adicional, a resposta fornece o url para abrir um incidente de suporte.  Para obter mais informações sobre as propriedades de resposta e o que está incluído, visite [descrição geral de resolução de problemas de observador de rede](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre o download de arquivos de contas de armazenamento do azure, consulte [introdução ao armazenamento de Blobs do Azure com o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser utilizada é o Explorador de armazenamento. Obter mais informações sobre o Explorador de armazenamento podem ser encontradas aqui na seguinte hiperligação: [Explorador de armazenamento](https://storageexplorer.com/)

## <a name="next-steps"></a>Passos Seguintes

Se foram alteradas as definições de conectividade VPN que stop, veja [gerir grupos de segurança de rede](../virtual-network/manage-network-security-group.md) rastrear as regras de segurança e de grupo de segurança de rede que podem ser em questão.
