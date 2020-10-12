---
title: Azure VMware Solution by CloudSimple - Escolha uma solução de equilíbrio de carga para CloudSimple Private Clouds
description: Descreve as opções de equilíbrio de carga implementando uma aplicação numa Nuvem Privada
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2fec54c19b9040e7cb44f1a5d2b7909510ab7313
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82734608"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Escolha uma solução de equilíbrio de carga para CloudSimple Private Clouds

Ao implementar uma aplicação numa CloudSimple Private Cloud, pode escolher qualquer uma das várias opções para equilibrar a carga.

Pode escolher um equilibrador de carga virtual ou baseado em software na sua nuvem privada CloudSimple ou até mesmo utilizar o balançador de carga Azure L7 em execução na sua subscrição Azure para fazer frente com os VMs de nível web em execução na CloudSimple Private Cloud. Aqui, listamos algumas opções:

## <a name="virtual-load-balancers"></a>Equilibradores de carga virtuais

Pode implantar aparelhos de balançador de carga virtuais no seu ambiente VMware através da interface vCenter e configurá-los para o tráfego frontal da aplicação.

Alguns fornecedores populares são: NginX: http://nginx.org/en/docs/http/load_balancing.html F5- BigIP - Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Equilibrador de carga Azure L7

Quando utiliza o Azure Application Gateway como um equilibrador de carga L7 para a sua aplicação em execução numa Nuvem Privada, não precisa de gerir o software do balançador de carga. O software do balanceador de carga é gerido pela Azure. Todos os VMs de nível web na Nuvem Privada utilizam endereços IP privados e não requerem regras NAT adicionais ou endereços de IPs públicos para resolver nomes. Os VMs de nível web comunicam com o Gateway de aplicação Azure sobre uma ligação privada, de baixa latência e largura de banda alta.

Para saber mais sobre como configurar esta solução, consulte o guia de solução "Using Azure Application Gateway" como um equilibrador de carga L7.

## <a name="azure-internal-load-balancer"></a>Equilibrador de carga interna Azure

Se optar por executar a sua aplicação numa implementação híbrida onde o nível frontal da Web está a funcionar dentro de um Azure vNet na sua subscrição Azure e o nível DB da aplicação está a ser executado em VMware VMs em CloudSimple Private Cloud, pode utilizar o balançador interno de carga Azure (balançador de carga L4) em frente aos VMs de nível DB para gestão de tráfego.

Para saber mais, consulte a documentação [do Balançador Interno de Carga](../load-balancer/components.md#frontend-ip-configurations) Azure.

## <a name="global-server-load-balancer"></a>Balanceador global de carga do servidor

Se estiver à procura de um equilibrador de carga baseado em DNS, então pode utilizar soluções de terceiros disponíveis no Azure Marketplace ou seguir com a solução Azure nativa.

O Azure Traffic Manager é um equilibrador de carga baseado em DNS que lhe permite distribuir o tráfego da melhor forma para serviços em regiões e instalações globais do Azure, ao mesmo tempo que proporciona alta disponibilidade e capacidade de resposta. Para saber mais, consulte a documentação do Gestor [de Tráfego da](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) Azure.
