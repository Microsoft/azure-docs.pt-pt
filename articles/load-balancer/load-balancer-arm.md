---
title: O suporte do Azure Resource Manager para o Balanceador de carga | Documentos da Microsoft
description: Com o powershell para o Balanceador de carga com o Azure Resource Manager. Utilizar modelos de Balanceador de carga
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 596ac871067886ee3124c0f21beb35cb3b8fe1ae
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593816"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Utilizar o suporte do Azure Resource Manager com o Balanceador de carga do Azure



O Azure Resource Manager é a estrutura de gestão preferenciais para serviços do Azure. O Balanceador de carga do Azure podem ser gerido através de APIs baseadas no Azure Resource Manager e ferramentas.

## <a name="concepts"></a>Conceitos

Com o Resource Manager, o Balanceador de carga do Azure contém os seguintes recursos de subordinados:

* Configuração de IP Front-end – um balanceador de carga pode incluir um ou mais front-end endereços IP, também conhecidos como um IPs virtuais (VIPs). Estes endereços IP servem como entrada para o tráfego.
* Conjunto de endereços de back-end – estes são os endereços IP associados com a máquina virtual Interface de rede cartão (NIC) para que a carga é distribuída.
* Regras de balanceamento de carga – uma propriedade de regra mapeia um IP de front-end especificado e a combinação de portas para um conjunto de endereços IP de back-end e a combinação de portas. Um balanceador de carga individual pode ter várias regras de balanceamento de carga. Cada regra é uma combinação de um IP de front-end e porta e back-end IP e porta associados a VMs.
* Sondas – sondas permitem controlar o estado de funcionamento de instâncias de VM. Se uma sonda de estado de funcionamento falhar, a instância VM será automaticamente retirada da rotação.
* Regras NAT de entrada – NAT definindo o tráfego de entrada que flui através do IP de front-end de regras e distribuído para o IP de back-end.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modelos de início rápido

O Gestor de Recursos do Azure permite utilizar modelos declarativos para aprovisionar as suas aplicações. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Pode utilizar o mesmo modelo para implementar repetidamente a sua aplicação durante cada fase do ciclo de vida da aplicação.

Modelos podem incluir definições de máquinas virtuais, redes virtuais, conjuntos de disponibilidade, Interfaces de rede (NICs), as contas de armazenamento, balanceadores de carga, grupos de segurança de rede e IPs públicos. Com modelos, pode criar tudo o que precisa para um aplicativo complexo. O ficheiro de modelo pode ser verificado no sistema de gerenciamento de conteúdo para controle de versão e colaboração.

[Saiba mais sobre os modelos](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Saiba mais sobre os recursos de rede](../networking/networking-overview.md)

Para modelos de início rápido com o Balanceador de carga do Azure, consulte a [repositório do GitHub](https://github.com/Azure/azure-quickstart-templates) que aloja um conjunto de modelos da Comunidade gerado.

Exemplos de modelos:

* [2 VMs num Load Balancer e regras de balanceamento de carga](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs numa VNET com regras de Balanceador de carga interno e o Balanceador de carga](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs num Balanceador de carga e configurar regras NAT do LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configuração de Balanceador de carga do Azure com o PowerShell ou a CLI

Começar a utilizar com os cmdlets, ferramentas de linha de comandos e as APIs REST do Azure Resource Manager

* [Cmdlets de rede do Azure](https://docs.microsoft.com/powershell/module/az.network#networking) pode ser utilizado para criar um balanceador de carga.
* [Como criar um balanceador de carga com o Azure Resource Manager](load-balancer-get-started-ilb-arm-ps.md)
* [Utilizar a CLI do Azure com a gestão de recursos do Azure](../xplat-cli-azure-resource-manager.md)
* [REST APIs do Balanceador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Passos Seguintes

Também pode [introdução à criação de um balanceador de carga de acesso à Internet](load-balancer-get-started-internet-arm-ps.md) e configurar o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento de tráfego de rede de Balanceador de carga específico.

Saiba como gerenciar [definições de tempo limite TCP para um balanceador de carga de inatividade](load-balancer-tcp-idle-timeout.md). Isso é importante quando a sua aplicação precisa para manter as ligações ativas para os servidores por trás de um balanceador de carga.
