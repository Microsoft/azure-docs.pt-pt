---
title: Troubleshoot Azure VNet gateway e conexões - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Esta página explica como usar o problema do Observador de Rede Azure PowerShell cmdlet
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
ms.openlocfilehash: 0723ddc9b0e2f15d5c8e51c96d51f58f1313493a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78673657"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Troubleshoot Virtual Network Gateway e Connections usando O Observador de Rede Azure PowerShell

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI do Azure](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

O Network Watcher fornece muitas capacidades no que diz respeito à compreensão dos seus recursos de rede em Azure. Uma destas capacidades é a resolução de problemas de recursos. A resolução de problemas de recursos pode ser chamada através do portal PowerShell, CLI ou REST API. Quando chamado, o Observador de Rede inspeciona a saúde de um Gateway de Rede Virtual ou de uma Ligação e devolve as suas descobertas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já seguiu os passos na [Create a Network Watcher](network-watcher-create.md) para criar um Observador de Rede.

Para uma lista de visitas de tipos de gateway suportados, os tipos de [Gateway suportados](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Descrição geral

A resolução de problemas de recursos fornece os problemas de resolução de problemas de capacidade que surgem com Gateways e Conexões de Rede Virtual. Quando é feito um pedido para a resolução de problemas de recursos, os registos estão a ser consultados e inspecionados. Quando a inspeção estiver concluída, os resultados são devolvidos. Os pedidos de resolução de problemas de recursos são pedidos de longa duração, que podem demorar vários minutos a devolver um resultado. Os registos de resolução de problemas são guardados num recipiente numa conta de armazenamento especificada.

## <a name="retrieve-network-watcher"></a>Observador de rede de recuperação

O primeiro passo é recuperar a instância do Observador da Rede. A `$networkWatcher` variável é `Start-AzNetworkWatcherResourceTroubleshooting` passada para o cmdlet no passo 4.

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Recuperar uma ligação de gateway de rede virtual

Neste exemplo, a resolução de problemas de recursos está a ser remeter-se numa Ligação. Também pode passar-lhe um Portal de Rede Virtual.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

A resolução de problemas de recursos devolve dados sobre a saúde do recurso, também guarda registos para uma conta de armazenamento a ser revista. Neste passo, criamos uma conta de armazenamento, se existir uma conta de armazenamento existente, pode utilizá-la.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Executar Network Watcher resolução de problemas de recursos

Tu atiras recursos `Start-AzNetworkWatcherResourceTroubleshooting` com o cmdlet. Passamos o cmdlet o objeto Observador de Rede, o Id da Ligação ou O Gateway da Rede Virtual, o id da conta de armazenamento e o caminho para armazenar os resultados.

> [!NOTE]
> O `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet está a funcionar há muito tempo e pode demorar alguns minutos a ser concluído.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Uma vez executado o cmdlet, o Observador de Rede revê o recurso para verificar a saúde. Devolve os resultados à concha e armazena registos dos resultados na conta de armazenamento especificada.

## <a name="understanding-the-results"></a>Compreender os resultados

O texto de ação fornece orientações gerais sobre a forma de resolver a questão. Se for possível tomar uma medida para a questão, é fornecida uma ligação com orientações adicionais. No caso de não existir orientação adicional, a resposta fornece o url para abrir um caso de suporte.  Para mais informações sobre as propriedades da resposta e o que está incluído, visite a visão geral do [Network Watcher Troubleshoot](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre o download de ficheiros de contas de armazenamento azure, consulte o Get started com o [armazenamento Azure Blob utilizando .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é o Storage Explorer. Mais informações sobre o Storage Explorer podem ser encontradas aqui no seguinte link: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Passos seguintes

Se foram alteradas as definições que impedem a conectividade VPN, consulte [a Manage Network Security Groups](../virtual-network/manage-network-security-group.md) para localizar o grupo de segurança da rede e as regras de segurança que possam estar em causa.
