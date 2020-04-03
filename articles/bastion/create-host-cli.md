---
title: Criar um anfitrião bastião usando o Azure CLI [ Bastião Azure
description: Neste artigo, aprenda a criar e apagar um anfitrião bastião
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: f98c965ad3b776f3688a716ba28b5367a00c9119
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619229"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Crie um anfitrião do Bastião Azure usando o Azure CLI

Este artigo mostra-lhe como criar um anfitrião do Azure Bastion usando o Azure CLI. Uma vez que você disponibiliza o serviço Azure Bastion na sua rede virtual, a experiência RDP/SSH sem emenda está disponível para todos os VMs na mesma rede virtual. A implantação do Azure Bastion é por rede virtual, não por subscrição/conta ou máquina virtual.

Opcionalmente, pode criar um anfitrião do Azure Bastion utilizando o [portal Azure,](bastion-create-host-portal.md)ou utilizando o [Azure PowerShell.](bastion-create-host-powershell.md)

## <a name="before-you-begin"></a>Antes de começar

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um anfitrião do Bastion

Esta secção ajuda-o a criar um novo recurso Azure Bastion utilizando o Azure CLI.

1. Crie uma rede virtual e uma subnet Azure Bastion. Deve criar a sub-rede Azure Bastion utilizando o valor de nome **AzureBastionSubnet**. Este valor permite ao Azure saber para que sub-rede implantar os recursos da Bastião. Isto é diferente de uma sub-rede gateway. Deve utilizar uma sub-rede de, pelo menos,27 ou sub-rede maior (/27, /26, e assim por diante). Crie a **AzureBastionSubnet** sem tabelas ou delegações de rotas. Se utilizar grupos de segurança de rede na **AzureBastionSubnet,** consulte o artigo [Trabalho com NSGs.](bastion-nsg.md)

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Crie um endereço IP público para o Azure Bastion. O IP público é o endereço IP público do recurso Bastião sobre o qual o RDP/SSH será acedido (sobre o porto 443). O endereço IP público deve estar na mesma região que o recurso Bastião que está a criar.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. Crie um novo recurso Azure Bastion na AzureBastionSubnet da sua rede virtual. Leva cerca de 5 minutos para o recurso Bastião criar e implantar.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Passos seguintes

* Leia o [Bastião FAQ](bastion-faq.md) para obter informações adicionais.

* Para utilizar grupos de segurança de rede com a subnet Azure Bastion, consulte [Trabalhar com NSGs](bastion-nsg.md).
