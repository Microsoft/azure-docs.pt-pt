---
title: Azure VMware Solutions (AVS) - Escolha uma solução de equilíbrio de carga para Nuvens Privadas AVS
description: Descreve as opções de equilíbrio de carga que implementam uma aplicação numa Nuvem Privada AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 405bc9d95b8d82e2181e2fb828d6bcc00c8c4639
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014883"
---
# <a name="choose-a-load-balancing-solution-for-avs-private-clouds"></a>Escolha uma solução de equilíbrio de carga para AVS Private Clouds

Ao implementar uma aplicação numa Nuvem Privada AVS, pode escolher qualquer uma das várias opções para equilibrar a carga.

Pode escolher um equilibrador de carga virtual ou baseado em software na sua nuvem privada AVS ou até mesmo utilizar o equilíbrio de carga Azure L7 a funcionar na sua subscrição Azure para frontalizar os Seus VMs de nível web em execução na Nuvem Privada AVS. Aqui, listamos algumas opções:

## <a name="virtual-load-balancers"></a>Balanceadores de carga virtuais

Você pode implantar dispositivos de balanceador de carga virtual em seu ambiente VMware por meio da interface do vCenter e configurá-los para front-end do tráfego do aplicativo.

Alguns vendedores populares são: NginX: http://nginx.org/en/docs/http/load_balancing.html F5- BigIP - Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Balanceador de carga L7 do Azure

Quando utiliza o Portal de Aplicações Azure como um balancedor de carga L7 para a sua aplicação em funcionamento numa Nuvem Privada AVS, não precisa de gerir o software de equilíbrio de carga. O software do balanceador de carga é gerenciado pelo Azure. Todos os VMs de nível web na Nuvem Privada AVS usam endereços IP privados e não requerem regras nat adicionais ou endereços de IPs públicos para resolver nomes. As VMs da camada da Web se comunicam com o gateway de Aplicativo Azure por uma conexão privada, de baixa latência e de largura de banda alta.

Para saber mais sobre como configurar essa solução, consulte o guia de solução sobre como usar Aplicativo Azure Gateway como um balanceador de carga L7.

## <a name="azure-internal-load-balancer"></a>Balanceador de carga interno do Azure

Se optar por executar a sua aplicação numa implementação híbrida onde o nível frontal da web está a funcionar dentro de um Azure vNet na sua subscrição Azure e o nível DB da aplicação está a funcionar em VMware VMs em AVS Private Cloud, pode utilizar o equilíbrio de carga interna Azure (L L), pode utilizar o equilíbrio dorde carga interno Azure (L L). 4 equilibradores de carga) em frente aos seus VMs de nível DB para gestão de tráfego.

Para saber mais, consulte a documentação de [Load balancer interno](../load-balancer/concepts-limitations.md#internalloadbalancer) do Azure.

## <a name="global-server-load-balancer"></a>Balanceador de carga do servidor global

Se você estiver procurando um balanceador de carga baseado em DNS, poderá usar soluções de terceiros disponíveis no Azure Marketplace ou ir com a solução nativa do Azure.

O Gerenciador de tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que permite distribuir o tráfego de forma ideal para serviços em regiões globais do Azure e locais, fornecendo alta disponibilidade e capacidade de resposta. Para saber mais, consulte a documentação do [Gerenciador de tráfego](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) do Azure.
