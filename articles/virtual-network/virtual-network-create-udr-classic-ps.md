---
title: Controlar o encaminhamento num modelo de rede Virtual do Azure - PowerShell - clássico | Documentos da Microsoft
description: Saiba como controlar o encaminhamento em VNets com o PowerShell | Clássico
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 1441ee9a3d4a563ab35cd9b01e8347d8f51b827a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743409"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Controlar o encaminhamento e utilizar aplicações virtuais (clássico) com o PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [CLI do Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (Clássico)](virtual-network-create-udr-classic-ps.md)
> * [CLI (Clássica)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Antes de trabalhar com recursos do Azure, é importante compreender que o Azure tem atualmente dois modelos de implementação: O Azure Resource Manager e clássica. Confirme que compreende os [modelos e ferramentas de implementação](../azure-resource-manager/resource-manager-deployment-model.md) antes de trabalhar com qualquer recurso do Azure. Pode ver a documentação de diversas ferramentas selecionando uma opção na parte superior deste artigo. Este artigo abrange o modelo de implementação clássica.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

O exemplo do Azure PowerShell comandos abaixo esperam um ambiente simples já criado com base no cenário acima. Se quiser executar os comandos à medida que são apresentadas neste documento, criar o ambiente mostrado na [criar uma VNet (clássico) com o PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar o UDR para a sub-rede de front-end
Para criar a tabela de rotas e a rota necessário para a sub-rede de front-end com base no cenário acima, siga os passos abaixo.

1. Execute o seguinte comando para criar uma tabela de rota para a sub-rede de front-end:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Execute o seguinte comando para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para o **FW1** VM (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Execute o seguinte comando para associar a tabela de rotas com o **front-end** sub-rede:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar o UDR para a sub-rede de back-end
Para criar a tabela de rotas e a rota necessário para a sub-rede de back-end com base no cenário, conclua os seguintes passos:

1. Execute o seguinte comando para criar uma tabela de rota para a sub-rede de back-end:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Execute o seguinte comando para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de front-end (192.168.1.0/24) para o **FW1** VM (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Execute o seguinte comando para associar a tabela de rotas com o **back-end** sub-rede:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Ativar o reencaminhamento de IP na FW1 VM

Para ativar na FW1 VM de reencaminhamento de IPs, conclua os seguintes passos:

1. Execute o seguinte comando para verificar o estado de reencaminhamento de IP:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Execute o seguinte comando para ativar o IP de reencaminhamento para o *FW1* VM:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
