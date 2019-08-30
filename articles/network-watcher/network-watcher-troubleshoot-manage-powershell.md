---
title: Solucionar problemas de gateway de rede virtual do Azure e conexões – PowerShell | Microsoft Docs
description: Esta página explica como usar o cmdlet do PowerShell para solucionar problemas do observador de rede do Azure
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: 40d576a980bd66fea44f9f8e4935fab3d777e4c8
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163863"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Solucionar problemas de gateway de rede virtual e conexões usando o PowerShell do observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI do Azure](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

O observador de rede fornece muitos recursos que se relacionam com a compreensão de seus recursos de rede no Azure. Um desses recursos é a solução de problemas de recursos. A solução de problemas de recursos pode ser chamada por meio do portal, do PowerShell, da CLI ou da API REST. Quando chamado, o observador de rede inspeciona a integridade de um gateway de rede virtual ou uma conexão e retorna suas descobertas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você já seguiu as etapas em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

Para obter uma lista de tipos de gateway com suporte, visite [tipos de gateway com suporte](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Descrição geral

A solução de problemas de recursos fornece a capacidade de solucionar problemas que surgem com gateways de rede virtual e conexões. Quando uma solicitação é feita para a solução de problemas de recursos, os logs são consultados e inspecionados. Quando a inspeção for concluída, os resultados serão retornados. As solicitações de solução de problemas de recursos são solicitações de execução longa, o que pode levar vários minutos para retornar um resultado. Os logs da solução de problemas são armazenados em um contêiner em uma conta de armazenamento que é especificada.

## <a name="retrieve-network-watcher"></a>Recuperar observador de rede

A primeira etapa é recuperar a instância do observador de rede. A `$networkWatcher` variável é passada para o `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet na etapa 4.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Recuperar uma conexão de gateway de rede virtual

Neste exemplo, a solução de problemas de recursos está sendo executada em uma conexão. Você também pode passá-lo para um gateway de rede virtual.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

A solução de problemas de recursos retorna dados sobre a integridade do recurso; ela também salva os logs em uma conta de armazenamento a ser revisada. Nesta etapa, criaremos uma conta de armazenamento, se existir uma conta de armazenamento existente, você poderá usá-la.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Executar solução de problemas de recursos do observador de rede

Você soluciona problemas de recursos `Start-AzNetworkWatcherResourceTroubleshooting` com o cmdlet. Passamos o cmdlet do objeto observador de rede, a ID da conexão ou o gateway de rede virtual, a ID da conta de armazenamento e o caminho para armazenar os resultados.

> [!NOTE]
> O `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet é de longa execução e pode levar alguns minutos para ser concluído.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Depois de executar o cmdlet, o observador de rede revisa o recurso para verificar a integridade. Ele retorna os resultados para o Shell e armazena os logs dos resultados na conta de armazenamento especificada.

## <a name="understanding-the-results"></a>Compreendendo os resultados

O texto da ação fornece orientação geral sobre como resolver o problema. Se uma ação puder ser executada para o problema, será fornecido um link com diretrizes adicionais. No caso em que não há nenhuma orientação adicional, a resposta fornece a URL para abrir um caso de suporte.  Para obter mais informações sobre as propriedades da resposta e o que está incluído, visite [visão geral de solução de problemas](network-watcher-troubleshoot-overview.md) do observador de rede

Para obter instruções sobre como baixar arquivos de contas de armazenamento do Azure, consulte Introdução ao [armazenamento de BLOBs do Azure usando o .net](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é Gerenciador de Armazenamento. Mais informações sobre Gerenciador de Armazenamento podem ser encontradas aqui no seguinte link: [Explorador de Armazenamento](https://storageexplorer.com/)

## <a name="next-steps"></a>Passos seguintes

Se as configurações tiverem sido alteradas para interromper a conectividade VPN, consulte [gerenciar grupos de segurança de rede](../virtual-network/manage-network-security-group.md) para rastrear o grupo de segurança de rede e as regras de segurança que podem estar em questão.
