---
title: Resolver problemas de Gateway de rede Virtual do Azure e ligações - PowerShell | Documentos da Microsoft
description: Esta página explica como utilizar o observador de rede do Azure resolver problemas relacionados com o cmdlet do PowerShell
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 51fb834c0c6a3602ed0edfee6256183eefb2026b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57889493"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Resolver problemas de Gateway de rede Virtual e ligações com o PowerShell de observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI do Azure](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

Observador de rede oferece muitos recursos que diz respeito à Noções básicas sobre os recursos de rede no Azure. Um desses recursos é o recurso de resolução de problemas. Resolução de problemas de recursos pode ser chamada através do portal, o PowerShell, a CLI ou a REST API. Quando chamado, o observador de rede inspeciona o estado de funcionamento de um Gateway de rede Virtual ou uma ligação e devolve conclusões.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que já tiver seguido os passos em [criar um observador de rede](network-watcher-create.md) para criar um observador de rede.

Para obter uma lista de visita de tipos de gateway suportados [tipos de Gateway suportados](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Descrição geral

Resolução de problemas de recursos fornece a capacidade de resolver os problemas que surgem com ligações e Gateways de rede Virtual. Quando é efetuado um pedido para a resolução de problemas de recursos, os registos estão a ser consultado e inspecioná-lo. Quando a inspeção estiver concluída, os resultados são devolvidos. Recursos de pedidos de resolução de problemas são de longa pedidos, que pode demorar vários minutos para retornar um resultado. Os registos de resolução de problemas são armazenados num contentor numa conta de armazenamento que está especificado.

## <a name="retrieve-network-watcher"></a>Obter o observador de rede

A primeira etapa é obter a instância do observador de rede. O `$networkWatcher` variável é transmitida para o `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet no passo 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Obter uma ligação de Gateway de rede Virtual

Neste exemplo, a resolução de problemas de recursos está a ser foi executada numa conexão. Também pode passá-lo um Gateway de rede Virtual.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Resolução de problemas de recursos devolve dados sobre o estado de funcionamento do recurso, também economiza registos para uma conta de armazenamento para ser revisto. Neste passo, vamos criar uma conta de armazenamento, se existir uma conta de armazenamento existente pode utilizá-lo.

```powershell
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzureRmCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzureStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Executar a resolução de problemas de recursos do observador de rede

Resolver problemas relacionados com recursos com o `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet. O cmdlet, transmitimos o objeto do observador de rede, o Id da ligação ou Gateway de rede Virtual, o id de conta de armazenamento e o caminho para armazenar os resultados.

> [!NOTE]
> O `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet é de longa execução e pode demorar alguns minutos a concluir.

```powershell
Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Depois de executar o cmdlet, o observador de rede analisa o recurso para verificar o estado de funcionamento. Ele retorna os resultados para o shell e armazena os registos dos resultados na conta de armazenamento especificada.

## <a name="understanding-the-results"></a>Noções básicas sobre os resultados

O texto de ação fornece orientações gerais sobre como resolver o problema. Se uma ação possa ser executada para o problema, é fornecida uma ligação com orientações adicionais. No caso em que não existe nenhuma orientação adicional, a resposta fornece o url para abrir um incidente de suporte.  Para obter mais informações sobre as propriedades de resposta e o que está incluído, visite [descrição geral de resolução de problemas de observador de rede](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre o download de arquivos de contas de armazenamento do azure, consulte [introdução ao armazenamento de Blobs do Azure com o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser utilizada é o Explorador de armazenamento. Obter mais informações sobre o Explorador de armazenamento podem ser encontradas aqui na seguinte hiperligação: [Explorador de armazenamento](https://storageexplorer.com/)

## <a name="next-steps"></a>Passos Seguintes

Se foram alteradas as definições de conectividade VPN que stop, veja [gerir grupos de segurança de rede](../virtual-network/manage-network-security-group.md) rastrear as regras de segurança e de grupo de segurança de rede que podem ser em questão.
