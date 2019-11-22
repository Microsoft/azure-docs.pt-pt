---
title: Solucionar problemas de gateway e conexões de VNET – API REST do Azure
titleSuffix: Azure Network Watcher
description: Esta página explica como solucionar problemas de gateways de rede virtual e conexões com o observador de rede do Azure usando REST
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: e4d5f195-b839-4394-94ef-a04192766e55
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: 9b3898a7c4cd09b59da0fc167b758199119793eb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277787"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Solucionar problemas de gateway de rede virtual e conexões usando o observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI do Azure](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

O observador de rede fornece muitos recursos que se relacionam com a compreensão de seus recursos de rede no Azure. Um desses recursos é a solução de problemas de recursos. A solução de problemas de recursos pode ser chamada por meio do portal, do PowerShell, da CLI ou da API REST. Quando chamado, o observador de rede inspeciona a integridade de um gateway de rede virtual ou uma conexão e retorna suas descobertas.

Este artigo orienta você pelas diferentes tarefas de gerenciamento que estão disponíveis atualmente para solução de problemas de recursos.

- [**Solucionar problemas de um gateway de rede virtual**](#troubleshoot-a-virtual-network-gateway)
- [**Solucionar problemas de conexão**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Antes de começar

ARMclient é usado para chamar a API REST usando o PowerShell. ARMClient é encontrado no Chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que você já seguiu as etapas em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

Para obter uma lista de tipos de gateway com suporte, visite [tipos de gateway com suporte](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Descrição geral

A solução de problemas do observador de rede fornece a capacidade de solucionar problemas que surgem com gateways e conexões de rede virtual. Quando uma solicitação é feita para a solução de problemas de recursos, os logs são consultados e inspecionados. Quando a inspeção for concluída, os resultados serão retornados. As solicitações de API de solução de problemas são solicitações de execução longa, o que pode levar vários minutos para retornar um resultado. Os logs são armazenados em um contêiner em uma conta de armazenamento.

## <a name="log-in-with-armclient"></a>Fazer logon com ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Solucionar problemas de um gateway de rede virtual


### <a name="post-the-troubleshoot-request"></a>POSTAR a solicitação de solução de problemas

O exemplo a seguir consulta o status de um gateway de rede virtual.

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

Como essa operação está em execução longa, o URI para consultar a operação e o URI para o resultado é retornado no cabeçalho de resposta, conforme mostrado na seguinte resposta:

**Valores importantes**

* **Azure-AsyncOperation** -essa propriedade contém o URI para consultar a operação de solução de problemas assíncronos
* **Local** -essa propriedade contém o URI onde os resultados são quando a operação é concluída

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

### <a name="query-the-async-operation-for-completion"></a>Consultar a operação assíncrona para conclusão

Use o URI de operações para consultar o progresso da operação, conforme mostrado no exemplo a seguir:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Enquanto a operação estiver em andamento, a resposta mostrará **InProgress** como visto no exemplo a seguir:

```json
{
  "status": "InProgress"
}
```

Quando a operação for concluída, o status será alterado para **êxito**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Obter os resultados

Depois que o status retornado for **bem-sucedido**, chame um método Get no URI operationResult para recuperar os resultados.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

As respostas a seguir são exemplos de uma resposta de degradação típica retornada ao consultar os resultados da solução de problemas de um gateway. Consulte [noções básicas sobre os resultados](#understanding-the-results) para obter esclarecimentos sobre o que as propriedades na resposta significam.

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


## <a name="troubleshoot-connections"></a>Solucionar problemas de conexões

O exemplo a seguir consulta o status de uma conexão.

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
> A operação de solução de problemas não pode ser executada em paralelo em uma conexão e seus gateways correspondentes. A operação deve ser concluída antes de executá-la no recurso anterior.

Como essa é uma transação de longa execução, no cabeçalho de resposta, o URI para consultar a operação e o URI para o resultado é retornado conforme mostrado na seguinte resposta:

**Valores importantes**

* **Azure-AsyncOperation** -essa propriedade contém o URI para consultar a operação de solução de problemas assíncronos
* **Local** -essa propriedade contém o URI onde os resultados são quando a operação é concluída

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

### <a name="query-the-async-operation-for-completion"></a>Consultar a operação assíncrona para conclusão

Use o URI de operações para consultar o progresso da operação, conforme mostrado no exemplo a seguir:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Enquanto a operação estiver em andamento, a resposta mostrará **InProgress** como visto no exemplo a seguir:

```json
{
  "status": "InProgress"
}
```

Quando a operação for concluída, o status será alterado para **êxito**.

```json
{
  "status": "Succeeded"
}
```

As respostas a seguir são exemplos de uma resposta típica retornada ao consultar os resultados da solução de problemas de uma conexão.

### <a name="retrieve-the-results"></a>Obter os resultados

Depois que o status retornado for **bem-sucedido**, chame um método Get no URI operationResult para recuperar os resultados.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

As respostas a seguir são exemplos de uma resposta típica retornada ao consultar os resultados da solução de problemas de uma conexão.

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

## <a name="understanding-the-results"></a>Compreendendo os resultados

O texto da ação fornece orientação geral sobre como resolver o problema. Se uma ação puder ser executada para o problema, será fornecido um link com diretrizes adicionais. No caso em que não há nenhuma orientação adicional, a resposta fornece a URL para abrir um caso de suporte.  Para obter mais informações sobre as propriedades da resposta e o que está incluído, visite [visão geral de solução de problemas do observador de rede](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre como baixar arquivos de contas de armazenamento do Azure, consulte Introdução ao [armazenamento de BLOBs do Azure usando o .net](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é Gerenciador de Armazenamento. Mais informações sobre Gerenciador de Armazenamento podem ser encontradas aqui no seguinte link: [Gerenciador de armazenamento](https://storageexplorer.com/)

## <a name="next-steps"></a>Passos seguintes

Se as configurações tiverem sido alteradas para interromper a conectividade VPN, consulte [gerenciar grupos de segurança de rede](../virtual-network/manage-network-security-group.md) para rastrear o grupo de segurança de rede e as regras de segurança que podem estar em questão.
