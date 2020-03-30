---
title: Utilize o Portal de Aplicações Azure com máquinas virtuais VMware
description: Descreve como usar a porta de aplicação Azure para gerir o tráfego web de entrada para servidores web que executam em máquinas virtuais VMware ganham o ambiente CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015461"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Utilize o Portal de Aplicações Azure com máquinas virtuais VMware no ambiente CloudSimple Private Cloud

Pode utilizar o Portal de Aplicações Azure para gerir o tráfego web para os seus servidores web que executam em máquinas virtuais VMware dentro do seu ambiente Cloud Simple Private Cloud.

Ao aproveitar o Portal de Aplicações Azure numa implantação híbrida público-privada, pode gerir o tráfego web para as suas aplicações, fornecer uma frontal frontal segura e descarregar o processamento de SSL para os seus serviços em funcionamento em ambiente VMware. O Azure Application Gateway encaminha o tráfego web para apoiar as instâncias de piscina que residem em ambientes VMware de acordo com regras configuradas e sondas de saúde.

Esta solução De gateway de aplicação Azure requer:

* Ter uma subscrição do Azure.
* Crie e configure uma rede virtual Azure e uma subnet dentro da rede virtual.
* Crie e configure as regras do NSG e peer your vNet usando ExpressRoute para a sua CloudSimple Private Cloud.
* Crie & Configure a sua Nuvem Privada.
* Crie & configure o gateway de aplicação Azure.

## <a name="azure-application-gateway-deployment-scenario"></a>Cenário de implementação do Gateway de Aplicação Azure

Neste cenário, o Portal de Aplicações Azure funciona na sua rede virtual Azure. A rede virtual está ligada à sua Nuvem Privada sobre um circuito ExpressRoute. Todas as subredes na Nuvem Privada são acessíveis a IP a partir das subredes de rede virtual.

![Equilibrador de carga Azure na rede virtual Azure](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Como implementar a solução

O processo de implantação consiste nas seguintes tarefas:

1. [Verifique se os pré-requisitos são cumpridos](#1-verify-prerequisites)
2. [Ligue a sua ligação virtual Azure à Nuvem Privada](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Implementar um portal de aplicação Azure](#3-deploy-an-azure-application-gateway)
4. [Crie e configure a piscina VM do Web Server na sua nuvem privada](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Verificar os pré-requisitos

Verifique se estes pré-requisitos são cumpridos:

* Já foi criado um Gestor de Recursos Azure e uma rede virtual.
* Já foi criada uma subnet dedicada (para Application Gateway) dentro da sua rede virtual Azure.
* Já foi criada uma CloudSimple Private Cloud.
* Não existe um conflito ip entre subredes IP na rede virtual e subnets na Nuvem Privada.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Ligue a sua rede virtual Azure à sua Nuvem Privada

Para ligar a sua rede virtual Azure à sua Cloud Privada, siga este processo.

1. [No portal CloudSimple, copie a informação de peering ExpressRoute](virtual-network-connection.md).

2. [Configure uma porta de entrada de rede virtual para a sua rede virtual Azure.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

3. [Ligue a sua rede virtual ao circuito CloudSimple ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Utilize as informações de observação que copiou para ligar a sua rede virtual ao circuito ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Implementar um portal de aplicação Azure

As instruções detalhadas para tal estão disponíveis na [Create a application gateway com regras de encaminhamento baseadas em caminhos utilizando o portal Azure](../application-gateway/create-url-route-portal.md). Aqui está um resumo dos passos necessários:

1. Crie uma rede virtual no seu grupo de subscrição e recursos.
2. Crie uma sub-rede (a ser utilizada como sub-rede dedicada) dentro da sua rede virtual.
3. Crie um Gateway de Aplicação padrão (opcionalmente ativar **Resource** > WAF): A partir da página inicial do portal Azure, clique no Portal de > **Aplicação** de**Rede**de Recursos gateway do lado superior esquerdo da página. Selecione o SKU padrão e o tamanho e forneça informações de subscrição, grupo de recursos e localização azure. Se necessário, crie um novo IP público para esta porta de entrada de aplicações e forneça detalhes sobre a rede virtual e a subnet dedicada para o gateway da aplicação.
4. Adicione uma piscina de backend com máquinas virtuais e adicione-a ao seu gateway de aplicação.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Crie e configure uma piscina VM do servidor web na sua Nuvem Privada

No vCenter, crie VMs com o OS e o servidor web à sua escolha (como Windows/IIS ou Linux/Apache). Escolha uma sub-rede/VLAN que seja designada para o nível web na sua Nuvem Privada. Verifique se pelo menos um vNIC dos VMs do servidor web está na sub-rede de nível web.
