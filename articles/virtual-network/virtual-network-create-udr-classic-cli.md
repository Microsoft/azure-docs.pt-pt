---
title: Controlar o roteamento em uma rede virtual do Azure-CLI-clássico | Microsoft Docs
description: Saiba como controlar o roteamento em VNets usando o CLI do Azure no modelo de implantação clássico
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 1193145b315175e6394db4caf93ab2e76a942ed9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058793"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Controlar o roteamento e usar dispositivos virtuais (clássicos) usando o CLI do Azure

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [CLI do Azure](tutorial-create-route-table-cli.md)
> * [PowerShell (Clássico)](virtual-network-create-udr-classic-ps.md)
> * [CLI (Clássica)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Você também pode [controlar o roteamento e usar dispositivos virtuais no modelo de implantação do Resource Manager](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Os comandos de CLI do Azure de exemplo abaixo esperam um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, crie o ambiente mostrado em [criar uma VNet (clássica) usando o CLI do Azure](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar o UDR para a sub-rede de front-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de front-end com base no cenário acima, siga as etapas abaixo.

1. Execute o seguinte comando para alternar para o modo clássico:

    ```azurecli
    azure config mode asm
    ```

    Saída:

        info:    New mode is asm

2. Execute o seguinte comando para criar uma tabela de rotas para a sub-rede de front-end:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Saída:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parâmetros:
   
   * **-l (ou --location)** . Região do Azure em que o novo NSG será criado. Para nosso cenário, *westus*.
   * **-n (ou --name)** . Nome para o novo NSG. Para o nosso cenário, *NSG-frontend*.
3. Execute o comando a seguir para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para a VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Saída:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parâmetros:
   
   * **-r (ou--nome-da-tabela-de-rotas)** . Nome da tabela de rotas em que a rota será adicionada. Para o nosso cenário, *UDR-frontend*.
   * **-a (or --address-prefix)** . Prefixo de endereço da sub-rede para a qual os pacotes estão destinados. Para o nosso cenário, *192.168.2.0/24*.
   * **-t (ou--Next-salto-Type)** . Tipo de tráfego de objeto ao qual será enviado. Os valores possíveis são *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*ou *None*.
   * **-p (ou--Next-salto-IP-address**). Endereço IP para o próximo salto. Para o nosso cenário, *192.168.0.4*.
4. Execute o seguinte comando para associar a tabela de rotas criada com a sub-rede **frontend** :

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Saída:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Parâmetros:
   
   * **-t (ou--vnet-Name)** . Nome da VNet em que a sub-rede está localizada. Para o nosso cenário *TestVNet*.
   * **-n (ou--subnet-Name**. Nome da sub-rede à qual a tabela de rotas será adicionada. Para o nosso cenário *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar o UDR para a sub-rede de back-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de back-end com base no cenário, conclua as seguintes etapas:

1. Execute o seguinte comando para criar uma tabela de rotas para a sub-rede de back-end:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Execute o comando a seguir para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de front-end (192.168.1.0/24) para a VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Execute o seguinte comando para associar a tabela de rotas à sub-rede de **back-end** :

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

