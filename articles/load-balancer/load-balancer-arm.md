---
title: Suporte de Azure Resource Manager para Load Balancer | Microsoft Docs
description: Usando o PowerShell para Load Balancer com Azure Resource Manager. Usando modelos para o balanceador de carga
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 839b607b7787d51151401737848a46d7b66229dd
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275477"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Usando o suporte do Azure Resource Manager com Azure Load Balancer



Azure Resource Manager é a estrutura de gerenciamento preferida para serviços no Azure. Azure Load Balancer pode ser gerenciado usando APIs e ferramentas baseadas em Azure Resource Manager.

## <a name="concepts"></a>Conceitos

Com o Gerenciador de recursos, Azure Load Balancer contém os seguintes recursos filho:

* Configuração de IP de front-end – um balanceador de carga pode incluir um ou mais endereços IP de front-ends, também conhecidos como VIPs (IPs virtuais). Estes endereços IP servem como entrada para o tráfego.
* Pool de endereços de back-end – esses são endereços IP associados à NIC (placa de interface de rede) da máquina virtual para a qual a carga é distribuída.
* Regras de balanceamento de carga – uma propriedade de regra mapeia uma determinada combinação de IP de front-end e porta para um conjunto de endereços IP de back-end e combinação de porta. Um balanceador de carga individual pode ter várias regras de balanceamento de carga. Cada regra é uma combinação de um IP de front-end e uma porta e um IP de back-end e uma porta associada às VMs.
* Investigações – as investigações permitem manter o controle da integridade das instâncias de VM. Se uma investigação de integridade falhar, a instância de VM será retirada da rotação automaticamente.
* Regras de NAT de entrada – regras de NAT que definem o tráfego de entrada que flui através do IP de front-end e distribuído para o IP de back-end.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modelos de início rápido

O Gestor de Recursos do Azure permite utilizar modelos declarativos para aprovisionar as suas aplicações. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Pode utilizar o mesmo modelo para implementar repetidamente a sua aplicação durante cada fase do ciclo de vida da aplicação.

Os modelos podem incluir definições para máquinas virtuais, redes virtuais, conjuntos de disponibilidade, NICs (interfaces de rede), contas de armazenamento, balanceadores de carga, grupos de segurança de rede e IPs públicos. Com os modelos, você pode criar tudo o que precisa para um aplicativo complexo. O arquivo de modelo pode ser verificado no sistema de gerenciamento de conteúdo para controle de versão e colaboração.

[Saiba mais sobre modelos](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Saiba mais sobre os recursos de rede](../networking/networking-overview.md)

Para modelos de início rápido usando Azure Load Balancer, consulte o [repositório GitHub](https://github.com/Azure/azure-quickstart-templates) que hospeda um conjunto de modelos gerados pela Comunidade.

Exemplos de modelos:

* [2 VMs em uma Load Balancer e regras de balanceamento de carga](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs em uma VNET com um Load Balancer interno e regras de Load Balancer](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs em um Load Balancer e configurar regras de NAT no LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configurando Azure Load Balancer com um PowerShell ou CLI

Introdução aos cmdlets Azure Resource Manager, ferramentas de linha de comando e APIs REST

* Os cmdlets de [rede do Azure](https://docs.microsoft.com/powershell/module/az.network#networking) podem ser usados para criar um Load Balancer.
* [Como criar um balanceador de carga usando Azure Resource Manager](load-balancer-get-started-ilb-arm-ps.md)
* [Usando o CLI do Azure com o gerenciamento de recursos do Azure](../xplat-cli-azure-resource-manager.md)
* [Load Balancer APIs REST](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Passos Seguintes

Você também pode começar [a criar um balanceador de carga voltado](load-balancer-get-started-internet-arm-ps.md) para a Internet e configurar o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento de tráfego de rede do balanceador de carga específico.

Saiba como gerenciar [configurações de tempo limite de TCP ocioso para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Isso é importante quando seu aplicativo precisa manter conexões ativas para servidores atrás de um balanceador de carga.
