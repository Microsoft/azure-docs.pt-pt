---
title: Criar um hospedeiro bastonário usando O Azure CLI Bastião Azure
description: Neste artigo, aprenda a criar e apagar um anfitrião de Bastião
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: 851ec86feb5244ff43759a7aef2b80876dcfa734
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018547"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Criar um anfitrião Azure Bastion usando Azure CLI

Este artigo mostra-lhe como criar um anfitrião Azure Bastion usando Azure CLI. Depois de implementar o Bastion, pode ligar-se a um VM através do seu endereço IP privado através do seu navegador utilizando o portal Azure. O seu VM não precisa de um endereço IP público, de um cliente adicional ou de um software especial. A implementação do Azure Bastion é por rede virtual, não por subscrição/conta ou máquina virtual. A experiência PDR/SSH sem costura está disponível para todos os VMs na mesma rede virtual.

Opcionalmente, pode criar um anfitrião Azure Bastion utilizando o [portal Azure,](tutorial-create-host-portal.md)ou utilizando [a Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um hospedeiro de bastião

Esta secção ajuda-o a criar um novo recurso Azure Bastion utilizando o Azure CLI.

> [!NOTE]
> Como mostrado nos exemplos, utilize o `--location` parâmetro com cada comando para garantir que os recursos são `--resource-group` implantados em conjunto.

1. Crie uma rede virtual e uma sub-rede Azure Bastion. Tem de criar a sub-rede Azure Bastion utilizando o valor do nome **AzureBastionSubnet**. Este valor permite ao Azure saber a que sub-rede deve implantar os recursos do Bastião. Isto é diferente de uma sub-rede Gateway. Deve utilizar uma sub-rede de pelo menos /27 ou sub-rede maior (/27, /26, e assim por diante). Crie a **AzureBastionSubnet** sem quaisquer tabelas de rotas ou delegações. Se utilizar grupos de segurança de rede na **AzureBastionSubnet,** consulte o artigo [do Trabalho com NSGs.](bastion-nsg.md)

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Crie um endereço IP público para Azure Bastion. O IP público é o endereço IP público do recurso Bastião no qual o PDR/SSH será acedido (sobre a porta 443). O endereço IP público deve estar na mesma região que o recurso Bastion que está a criar.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Crie um novo recurso Azure Bastion na AzureBastionSubnet da sua rede virtual. Leva cerca de 5 minutos para o recurso Bastion criar e implantar.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>Passos seguintes

* Ligue-se a uma máquina virtual.
   * [VM do Linux](bastion-connect-vm-ssh.md)
   * [VM do Windows](bastion-connect-vm-rdp.md)

