---
title: Balanceador de Carga Standard e Conjuntos de Dimensionamento de Máquinas Virtuais
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
ms.openlocfilehash: 7e1df754a4a4ca5878d93d53282fd39191313b54
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883168"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Balançador de carga Azure com conjuntos de escala de máquina virtual Azure

Ao trabalhar com conjuntos de balanças de máquina virtuais e balanceador de carga, devem ser consideradas as seguintes orientações:

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

* [Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente utilizando o portal Azure](./configure-vm-scale-set-portal.md).
* [Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente utilizando a Azure PowerShell](./configure-vm-scale-set-powershell.md).
* [Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente utilizando o Azure CLI](./configure-vm-scale-set-cli.md).
* [Atualizar ou eliminar o balanceador de carga azure existente utilizado pelo Conjunto de Escala de Máquina Virtual](./update-load-balancer-with-vm-scale-set.md)
