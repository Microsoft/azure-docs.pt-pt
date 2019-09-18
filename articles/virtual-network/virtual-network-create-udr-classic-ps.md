---
title: Controlar o roteamento em uma rede virtual do Azure-PowerShell-clássico | Microsoft Docs
description: Saiba como controlar o roteamento no VNets usando o PowerShell | Clássico
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: f2f2c17740bd94629209c2bffb82689ecc931fc8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058777"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Controlar o roteamento e usar dispositivos virtuais (clássico) usando o PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [CLI do Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (Clássico)](virtual-network-create-udr-classic-ps.md)
> * [CLI (Clássica)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Antes de trabalhar com os recursos do Azure, é importante entender que o Azure atualmente tem dois modelos de implantação: Azure Resource Manager e clássico. Confirme que compreende os [modelos e ferramentas de implementação](../azure-resource-manager/resource-manager-deployment-model.md) antes de trabalhar com qualquer recurso do Azure. Você pode exibir a documentação para diferentes ferramentas selecionando uma opção na parte superior deste artigo. Este artigo abrange o modelo de implementação clássica.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Os comandos de Azure PowerShell de exemplo abaixo esperam um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, crie o ambiente mostrado em [criar uma VNet (clássica) usando o PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar o UDR para a sub-rede de front-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de front-end com base no cenário acima, siga as etapas abaixo.

1. Execute o seguinte comando para criar uma tabela de rotas para a sub-rede de front-end:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Execute o comando a seguir para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para a VM **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Execute o seguinte comando para associar a tabela de rotas à sub-rede **frontend** :

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar o UDR para a sub-rede de back-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de back-end com base no cenário, conclua as seguintes etapas:

1. Execute o seguinte comando para criar uma tabela de rotas para a sub-rede de back-end:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Execute o comando a seguir para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de front-end (192.168.1.0/24) para a VM **FW1** (192.168.0.4):

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Execute o seguinte comando para associar a tabela de rotas à sub-rede de **back-end** :

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Habilitar o encaminhamento de IP na VM FW1

Para habilitar o encaminhamento de IP na VM FW1, conclua as seguintes etapas:

1. Execute o seguinte comando para verificar o status do encaminhamento de IP:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. Execute o seguinte comando para habilitar o encaminhamento de IP para a VM *FW1* :

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
