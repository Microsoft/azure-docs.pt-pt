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
ms.openlocfilehash: f5a8453f84854108facb4da4616a7d362e0fb38c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532280"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Balançador de carga Azure com conjuntos de escala de máquina virtual Azure

Ao trabalhar com conjuntos de balanças de máquina virtuais e balanceador de carga, devem ser consideradas as seguintes orientações:

## <a name="multiple-virtual-machine-scale-sets-cant-use-the-same-load-balancer"></a>Vários conjuntos de escala de máquina virtual não podem usar o mesmo equilibrador de carga
## <a name="port-forwarding-and-inbound-nat-rules"></a>Regras de encaminhamento de porta e acesso NAT:
  * Após a criação do conjunto de escala, a porta de backend não pode ser modificada para uma regra de equilíbrio de carga utilizada por uma sonda sanitária do equilibrador de carga. Para alterar a porta, pode remover a sonda de saúde atualizando o conjunto de escala de máquina virtual Azure, atualizar a porta e, em seguida, configurar novamente a sonda de saúde.
  * Ao utilizar a balança de máquina virtual definida no pool de backend do balançador de carga, as regras NAT de entrada padrão são criadas automaticamente.
## <a name="inbound-nat-pool"></a>Piscina NAT de entrada:
  * Cada conjunto de balança de máquina virtual deve ter pelo menos uma piscina NAT de entrada. 
  * A piscina NAT de entrada é uma coleção de regras NAT de entrada. Uma piscina NAT de entrada não suporta vários conjuntos de balanças de máquinas virtuais.
  
## <a name="load-balancing-rules"></a>Regras de equilíbrio de carga:
  * Ao utilizar a balança de máquina virtual definida no pool de backend do balançador de carga, a regra de equilíbrio de carga padrão é criada automaticamente.
## <a name="outbound-rules"></a>Regras de saída:
  *  Para criar uma regra de saída para um pool de backend que já é referenciado por uma regra de equilíbrio de carga, é necessário primeiro marcar **"Criar regras implícitas de saída"** como **Não** no portal quando a regra de equilíbrio de carga de entrada é criada.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Criação de regras de equilíbrio de carga" border="true":::

Os seguintes métodos podem ser utilizados para implantar uma balança de máquina virtual definida com um equilibrador de carga Azure existente.

* [Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente utilizando o portal Azure](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal).
* [Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente utilizando a Azure PowerShell](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell).
* [Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente utilizando o Azure CLI](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli).
