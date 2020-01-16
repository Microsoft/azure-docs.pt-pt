---
title: Solução do Azure VMware por CloudSimple-escolha uma solução de balanceamento de carga para nuvens privadas do CloudSimple
description: Descreve as opções de balanceamento de carga implantando um aplicativo em uma nuvem privada
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045727"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Escolher uma solução de balanceamento de carga para nuvens privadas do CloudSimple

Ao implantar um aplicativo em uma nuvem privada do CloudSimple, você pode escolher qualquer uma das várias opções para o balanceamento de carga.

Você pode escolher um balanceador de carga baseado em software ou virtual em sua nuvem privada do CloudSimple ou até mesmo usar o Azure L7 Load Balancer em execução na sua assinatura do Azure para front-end suas VMs de camada da Web em execução na nuvem privada do CloudSimple. Aqui, listamos algumas opções:

## <a name="virtual-load-balancers"></a>Balanceadores de carga virtuais

Você pode implantar dispositivos de balanceador de carga virtual em seu ambiente VMware por meio da interface do vCenter e configurá-los para front-end do tráfego do aplicativo.

Alguns fornecedores populares são: NginX: http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Balanceador de carga L7 do Azure

Ao usar Aplicativo Azure Gateway como um balanceador de carga L7 para seu aplicativo em execução em uma nuvem privada, você não precisa gerenciar o software do balanceador de carga. O software do balanceador de carga é gerenciado pelo Azure. Todas as VMs da camada da Web na nuvem privada usam endereços IP privados e não exigem regras NAT adicionais ou endereços IPs públicos para resolver nomes. As VMs da camada da Web se comunicam com o gateway de Aplicativo Azure por uma conexão privada, de baixa latência e de largura de banda alta.

Para saber mais sobre como configurar essa solução, consulte o guia de solução sobre como usar Aplicativo Azure Gateway como um balanceador de carga L7.

## <a name="azure-internal-load-balancer"></a>Balanceador de carga interno do Azure

Se você optar por executar seu aplicativo em uma implantação híbrida em que a camada de front-end da Web está sendo executada em uma vNet do Azure em sua assinatura do Azure e a camada de BD do aplicativo estiver sendo executada em VMs VMware na nuvem privada do CloudSimple, você poderá usar a carga interna do Azure balanceador (balanceador de carga L4) na frente de suas VMs de camada de BD para o gerenciamento de tráfego.

Para saber mais, consulte a documentação de [Load balancer interno](../load-balancer/concepts-limitations.md#internalloadbalancer) do Azure.

## <a name="global-server-load-balancer"></a>Balanceador de carga do servidor global

Se você estiver procurando um balanceador de carga baseado em DNS, poderá usar soluções de terceiros disponíveis no Azure Marketplace ou ir com a solução nativa do Azure.

O Gerenciador de tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que permite distribuir o tráfego de forma ideal para serviços em regiões globais do Azure e locais, fornecendo alta disponibilidade e capacidade de resposta. Para saber mais, consulte a documentação do [Gerenciador de tráfego](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) do Azure.
