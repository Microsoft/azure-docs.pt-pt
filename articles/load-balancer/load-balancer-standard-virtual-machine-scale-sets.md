---
title: Adicione regras para O Equilibrador de Carga Padrão Azure e conjuntos de balança de máquinas virtuais
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: Com este caminho de aprendizagem, começa com o Azure Standard Load Balancer e conjuntos de escala de máquina virtual.
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
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592284"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Adicione regras para Azure Load Balancer com conjuntos de escala de máquina virtual

Quando trabalhar com conjuntos de balanças de máquinas virtuais e balanceador de carga Azure, considere as seguintes diretrizes.

## <a name="port-forwarding-and-inbound-nat-rules"></a>Regras de encaminhamento de portos e de entrada de NAT

Após a criação do conjunto de escala, a porta traseira não pode ser modificada para uma regra de equilíbrio de carga utilizada por uma sonda sanitária do equilibrador de carga. Para alterar a porta, retire a sonda de saúde atualizando o conjunto de balanças de máquina virtual e atualizando a porta. Em seguida, configurar a sonda de saúde novamente.

Quando utilizar a balança de máquina virtual definida no pool traseiro do equilibrador de carga, as regras NAT de entrada padrão são criadas automaticamente.
  
## <a name="inbound-nat-pool"></a>Piscina NAT de entrada

Cada conjunto de balança de máquina virtual deve ter pelo menos uma piscina NAT de entrada. Uma piscina NAT de entrada é uma coleção de regras NAT de entrada. Uma piscina NAT de entrada não suporta vários conjuntos de escala de máquina virtual.

## <a name="load-balancing-rules"></a>Regras de equilíbrio de carga

Quando utilizar a balança de máquina virtual definida no pool traseiro do equilibrador de carga, a regra de equilíbrio de carga padrão é criada automaticamente.
  
## <a name="outbound-rules"></a>Regras de saída

Para criar uma regra de saída para um pool back-end que já é referenciado por uma regra de equilíbrio de carga, selecione **Não** sob **Regras implícitas** de saída no portal Azure quando a regra de equilíbrio de carga de entrada for criada.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Screenshot que mostra criação de regras de equilíbrio de carga." border="true":::

Utilize os seguintes métodos para implantar uma balança de máquina virtual definida com uma instância existente de Balancer de Carga:

* [Configure uma balança de máquina virtual definida com um exemplo existente de Azure Load Balancer usando o portal Azure](./configure-vm-scale-set-portal.md)
* [Configure uma balança de máquina virtual definida com um exemplo existente de Azure Load Balancer usando Azure PowerShell](./configure-vm-scale-set-powershell.md)
* [Configure uma balança de máquina virtual definida com um exemplo existente de Azure Load Balancer usando o Azure CLI](./configure-vm-scale-set-cli.md)
* [Atualizar ou eliminar uma instância existente do Balançador de Carga Azure utilizado por um conjunto de escala de máquina virtual](./update-load-balancer-with-vm-scale-set.md)
