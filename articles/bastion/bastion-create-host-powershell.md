---
title: Criar um hospedeiro bastonário usando a Azure PowerShell Microsoft Docs
description: Neste artigo, aprenda a criar um anfitrião do Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: ba2716613a0e950cbae5c65add410ac8a8b38955
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077731"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Criar um anfitrião Azure Bastion usando Azure PowerShell

Este artigo mostra-lhe como criar um hospedeiro Azure Bastion usando o PowerShell. Uma vez que fornece o serviço Azure Bastion na sua rede virtual, a experiência PDR/SSH sem costura está disponível para todos os VMs na mesma rede virtual. A implementação do Azure Bastion é por rede virtual, não por subscrição/conta ou máquina virtual.

Opcionalmente, pode criar um anfitrião Azure Bastion utilizando o [portal Azure](./tutorial-create-host-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um hospedeiro de bastião

Esta secção ajuda-o a criar um novo recurso Azure Bastion utilizando a Azure PowerShell.

1. Crie uma rede virtual e uma sub-rede Azure Bastion. Tem de criar a sub-rede Azure Bastion utilizando o valor do nome **AzureBastionSubnet**. Este valor permite ao Azure saber a que sub-rede deve implantar os recursos do Bastião. Isto é diferente de uma sub-rede Gateway. Deve utilizar uma sub-rede de pelo menos /27 ou sub-rede maior (/27, /26, e assim por diante). Crie a **AzureBastionSubnet** sem quaisquer tabelas de rotas ou delegações. Se utilizar grupos de segurança de rede na **AzureBastionSubnet,** consulte o artigo [do Trabalho com NSGs.](bastion-nsg.md)

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Crie um endereço IP público para Azure Bastion. O IP público é o endereço IP público do recurso Bastião no qual o PDR/SSH será acedido (sobre a porta 443). O endereço IP público deve estar na mesma região que o recurso Bastion que está a criar.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Crie um novo recurso Azure Bastion na AzureBastionSubnet da sua rede virtual. Leva cerca de 5 minutos para o recurso Bastion criar e implantar.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Passos seguintes

* Leia as [FAQ de Bastião](bastion-faq.md) para obter informações adicionais.
* Para utilizar grupos de segurança de rede com a sub-rede Azure Bastion, consulte [Work with NSGs](bastion-nsg.md).
