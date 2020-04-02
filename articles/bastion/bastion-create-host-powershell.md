---
title: Criar um anfitrião bastião usando o Azure Powershell [ Microsoft Docs
description: Neste artigo, aprenda a criar um anfitrião do Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 43d834f0c834696cd4a836466c9663fe7c31a392
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520506"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Crie um anfitrião do Bastião Azure usando o Azure PowerShell

Este artigo mostra-lhe como criar um anfitrião do Azure Bastion usando a PowerShell. Uma vez que você disponibiliza o serviço Azure Bastion na sua rede virtual, a experiência RDP/SSH sem emenda está disponível para todos os VMs na mesma rede virtual. A implantação do Azure Bastion é por rede virtual, não por subscrição/conta ou máquina virtual.

Opcionalmente, pode criar um anfitrião do Azure Bastion utilizando o [portal Azure.](bastion-create-host-portal.md)

## <a name="before-you-begin"></a>Antes de começar

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um anfitrião do Bastion

Esta secção ajuda-o a criar um novo recurso Azure Bastion utilizando o Azure PowerShell.

1. Crie uma rede virtual e uma subnet Azure Bastion. Deve criar a sub-rede Azure Bastion utilizando o valor de nome **AzureBastionSubnet**. Este valor permite ao Azure saber para que sub-rede implantar os recursos da Bastião. Isto é diferente de uma sub-rede gateway. Deve utilizar uma sub-rede de, pelo menos,27 ou sub-rede maior (/27, /26, e assim por diante). Crie a **AzureBastionSubnet** sem tabelas ou delegações de rotas. Se utilizar grupos de segurança de rede na **AzureBastionSubnet,** consulte o artigo [Trabalho com NSGs.](bastion-nsg.md)

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Crie um endereço IP público para o Azure Bastion. O IP público é o endereço IP público do recurso Bastião sobre o qual o RDP/SSH será acedido (sobre o porto 443). O endereço IP público deve estar na mesma região que o recurso Bastião que está a criar.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Crie um novo recurso Azure Bastion na AzureBastionSubnet da sua rede virtual. Leva cerca de 5 minutos para o recurso Bastião criar e implantar.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Passos seguintes

* Leia o [Bastião FAQ](bastion-faq.md) para obter informações adicionais.

* Para utilizar grupos de segurança de rede com a subnet Azure Bastion, consulte [Trabalhar com NSGs](bastion-nsg.md).
