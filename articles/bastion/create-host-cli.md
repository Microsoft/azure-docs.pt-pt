---
title: Criar um hospedeiro bastonário usando O Azure CLI Bastião Azure
description: Neste artigo, aprenda a criar e apagar um anfitrião de Bastião
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: e7f80bb7f9be2e01aa24090d7305b1a5d882da04
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85255520"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Criar um anfitrião Azure Bastion usando Azure CLI

Este artigo mostra-lhe como criar um anfitrião Azure Bastion usando Azure CLI. Uma vez que fornece o serviço Azure Bastion na sua rede virtual, a experiência PDR/SSH sem costura está disponível para todos os VMs na mesma rede virtual. A implementação do Azure Bastion é por rede virtual, não por subscrição/conta ou máquina virtual.

Opcionalmente, pode criar um anfitrião Azure Bastion utilizando o [portal Azure,](bastion-create-host-portal.md)ou utilizando [a Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Antes de começar

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um hospedeiro de bastião

Esta secção ajuda-o a criar um novo recurso Azure Bastion utilizando o Azure CLI.

1. Crie uma rede virtual e uma sub-rede Azure Bastion. Tem de criar a sub-rede Azure Bastion utilizando o valor do nome **AzureBastionSubnet**. Este valor permite ao Azure saber a que sub-rede deve implantar os recursos do Bastião. Isto é diferente de uma sub-rede Gateway. Deve utilizar uma sub-rede de pelo menos /27 ou sub-rede maior (/27, /26, e assim por diante). Crie a **AzureBastionSubnet** sem quaisquer tabelas de rotas ou delegações. Se utilizar grupos de segurança de rede na **AzureBastionSubnet,** consulte o artigo [do Trabalho com NSGs.](bastion-nsg.md)

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Crie um endereço IP público para Azure Bastion. O IP público é o endereço IP público do recurso Bastião no qual o PDR/SSH será acedido (sobre a porta 443). O endereço IP público deve estar na mesma região que o recurso Bastion que está a criar.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp --sku Standard
   ```

3. Crie um novo recurso Azure Bastion na AzureBastionSubnet da sua rede virtual. Leva cerca de 5 minutos para o recurso Bastion criar e implantar.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Passos seguintes

* Leia as [FAQ de Bastião](bastion-faq.md) para obter informações adicionais.

* Para utilizar grupos de segurança de rede com a sub-rede Azure Bastion, consulte [Work with NSGs](bastion-nsg.md).
