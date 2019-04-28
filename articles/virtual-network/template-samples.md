---
title: Exemplos de modelos do Azure Resource Manager para a rede virtual | Microsoft Docs
description: Saiba mais sobre modelos do Azure Resource Manager diferentes disponíveis para a implementação de redes virtuais do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: c4d0c65cfae0bb323978f4143b0058071562ddb6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098427"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>Exemplos de modelos do Azure Resource Manager para a rede virtual

A tabela seguinte inclui ligações para exemplos de modelos do Azure Resource Manager. Pode implementar modelos com o [portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure, a [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure ou o Azure [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para saber como criar os seus próprios modelos, veja [Criar o primeiro modelo](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Compreender a estrutura e a sintaxe de modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para a sintaxe JSON e propriedades a utilizar em modelos, consulte [tipos de recursos de Network](/azure/templates/microsoft.network/allversions).


| | |
|----|----|
|[Criar uma rede virtual com duas sub-redes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| Cria uma rede virtual com duas sub-redes.|
|[Encaminhar o tráfego através de uma aplicação virtual de rede](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| Cria uma rede virtual com três sub-redes. Implementa uma máquina virtual em cada um das sub-redes. Cria uma tabela de rota que contém rotas para direcionar o tráfego de uma sub-rede para outra através da máquina virtual na terceira sub-rede. Associa a tabela de rotas para um das sub-redes.|
|[Criar um ponto final de serviço de rede virtual do Armazenamento do Microsoft Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|Cria uma nova rede virtual com duas sub-redes e uma interface de rede em cada sub-rede. Permite que um ponto final de serviço para o Armazenamento do Azure para uma das sub-redes e protege uma nova conta de armazenamento para essa sub-rede.|
|[Ligar duas redes virtuais](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| Cria duas redes virtuais e um peering de rede virtual entre as mesmas.|
|[Criar uma máquina virtual com vários endereços IP](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| Cria uma VM Windows ou Linux com vários endereços IP.|
|[Configurar IPv4 + IPv6 dupla pilha de rede virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/ipv6-in-vnet)|Implementa a rede virtual de pilha dupla (IPv4 + IPv6) com duas VMs e um balanceador de carga básico do Azure com endereços IP públicos IPv4 e IPv6. |
