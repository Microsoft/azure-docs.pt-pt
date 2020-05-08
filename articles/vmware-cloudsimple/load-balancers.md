---
title: Solução Azure VMware by CloudSimple - Escolha uma solução de equilíbrio de carga para CloudSimple Private Clouds
description: Descreve as opções de equilíbrio de carga que implementam uma aplicação numa Nuvem Privada
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2fec54c19b9040e7cb44f1a5d2b7909510ab7313
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734608"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Escolha uma solução de equilíbrio de carga para CloudSimple Private Clouds

Ao implementar uma aplicação numa CloudSimple Private Cloud, pode escolher qualquer uma das várias opções para equilibrar a carga.

Pode escolher um equilibrador de carga virtual ou baseado em software na sua nuvem privada CloudSimple ou até mesmo utilizar o equilíbrio de carga Azure L7 a funcionar na subscrição do Azure para frontalizar os VMs de nível web que estão a funcionar na CloudSimple Private Cloud. Aqui, listamos algumas opções:

## <a name="virtual-load-balancers"></a>Equilibradores de carga virtuais

Pode implantar aparelhos de equilíbrio de carga virtual no seu ambiente VMware através da interface vCenter e configurá-los para frontalizar o tráfego da aplicação.

Alguns vendedores populares são: NginX: http://nginx.org/en/docs/http/load_balancing.html F5- BigIP - Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC:https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Equilibrador de carga Azure L7

Quando utiliza o Portal de Aplicações Azure como um balancedor de carga L7 para a sua aplicação em funcionamento numa Cloud Privada, não precisa de gerir o software de equilíbrio de carga. O software de equilíbrio de carga é gerido pelo Azure. Todos os VMs de nível web na Nuvem Privada usam endereços IP privados e não requerem regras nat assimétricas adicionais ou endereços de IPs públicos para resolver nomes. Os VMs de nível Web comunicam com o Portal de Aplicações Azure sobre uma ligação privada, de baixa latência e largura de banda.

Para saber mais sobre como configurar esta solução, consulte o guia de solução sobre a utilização do Gateway de Aplicação Azure como um equilibrador de carga L7.

## <a name="azure-internal-load-balancer"></a>Equilibrador de carga interna Azure

Se optar por executar a sua aplicação numa implementação híbrida onde o nível frontal web está a funcionar dentro de um Azure vNet na sua subscrição Azure e o nível DB da aplicação está a funcionar em VMware VMs em CloudSimple Private Cloud, pode utilizar o equilíbrio dor de carga interna Azure (Equilíbrio de carga L4) em frente aos seus VMs de nível DB para gestão de tráfego.

Para saber mais, consulte a documentação [do Equilíbrio de Carga Interna](../load-balancer/components.md#frontend-ip-configurations) azure.

## <a name="global-server-load-balancer"></a>Equilibrador global de carga de servidor

Se procura um equilibrador de carga baseado em DNS, pode utilizar soluções de terceiros disponíveis no Azure Marketplace ou optar pela solução Azure nativa.

O Azure Traffic Manager é um equilibrista de carga de tráfego baseado em DNS que lhe permite distribuir o melhor tráfego pelos serviços em todas as regiões globais do Azure e no local, ao mesmo tempo que proporciona elevada disponibilidade e capacidade de resposta. Para saber mais, consulte a documentação [do Gestor de Tráfego](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) do Azure.
