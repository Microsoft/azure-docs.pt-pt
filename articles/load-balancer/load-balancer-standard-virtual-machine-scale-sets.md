---
title: Balançadores de carga padrão Azure e conjuntos de balança de máquina virtual
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: Com este caminho de aprendizagem, começa com o Azure Standard Load Balancer e os Conjuntos de Balanças De Máquina Virtual.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: fdca40d5113f06d185255be2e237cb52b47e9793
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697446"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Balançador de carga Azure com conjuntos de escala de máquina virtual Azure

Ao trabalhar com conjuntos de balanças de máquina virtuais e balanceador de carga, devem ser consideradas as seguintes orientações:

## <a name="port-forwarding-and-inbound-nat-rules"></a>Regras de encaminhamento de porta e acesso NAT:
  * Após a criação do conjunto de escala, a porta de backend não pode ser modificada para uma regra de equilíbrio de carga utilizada por uma sonda sanitária do equilibrador de carga. Para alterar a porta, pode remover a sonda de saúde atualizando o conjunto de escala de máquina virtual Azure, atualizar a porta e, em seguida, configurar novamente a sonda de saúde.
  * Ao utilizar a balança de máquina virtual definida no pool de backend do balançador de carga, as regras NAT de entrada padrão são criadas automaticamente.
## <a name="inbound-nat-pool"></a>Piscina NAT de entrada:
  * Cada conjunto de balança de máquina virtual deve ter pelo menos uma piscina NAT de entrada. 
  * A piscina NAT de entrada é uma coleção de regras NAT de entrada. Uma piscina NAT de entrada não suporta vários conjuntos de balanças de máquinas virtuais.
  * Para eliminar uma piscina NAT de um conjunto de balanças de máquinas virtuais existentes, é necessário remover primeiro a piscina NAT do conjunto de escala. Um exemplo completo utilizando o CLI é mostrado abaixo:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
  az vmss update-instances
     -–instance-ids *
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```
## <a name="load-balancing-rules"></a>Regras de equilíbrio de carga:
  * Ao utilizar a balança de máquina virtual definida no pool de backend do balançador de carga, a regra de equilíbrio de carga padrão é criada automaticamente.
## <a name="outbound-rules"></a>Regras de saída:
  *  Para criar uma regra de saída para um pool de backend que já é referenciado por uma regra de equilíbrio de carga, é necessário primeiro marcar **"Criar regras implícitas de saída"** como **Não** no portal quando a regra de equilíbrio de carga de entrada é criada.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Criação de regras de equilíbrio de carga" border="true":::

Os seguintes métodos podem ser utilizados para implantar uma balança de máquina virtual definida com um equilibrador de carga Azure existente.

* [Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente utilizando o portal Azure](./configure-vm-scale-set-portal.md).
* [Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente utilizando a Azure PowerShell](./configure-vm-scale-set-powershell.md).
* [Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente utilizando o Azure CLI](./configure-vm-scale-set-cli.md).