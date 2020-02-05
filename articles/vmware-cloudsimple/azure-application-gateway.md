---
title: Utilize o Portal de Aplicações Azure com máquinas virtuais VMware
description: Descreve como usar a porta de aplicação Azure para gerir o tráfego web de entrada para servidores web que executam em máquinas virtuais VMware ganham o ambiente AVS Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94cc6e40b88fe631d525f41001034f5dada05397
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015461"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-avs-private-cloud-environment"></a>Utilize o Portal de Aplicações Azure com máquinas virtuais VMware no ambiente Da Vs Private Cloud

Pode utilizar o Portal de Aplicações Azure para gerir o tráfego web para os seus servidores web que executam em máquinas virtuais VMware dentro do seu ambiente AVS Private Cloud.

Ao aproveitar o Portal de Aplicações Azure numa implantação híbrida público-privada, pode gerir o tráfego web para as suas aplicações, fornecer uma frontal frontal segura e descarregar o processamento de SSL para serviços em funcionamento no ambiente VMware. O Portal de Aplicações Azure rotas de entrada de tráfego web para apoiar casos de piscina que residem em ambientes VMware de acordo com regras configuradas e sondas de saúde.

Esta solução De gateway de aplicação Azure requer:

* Ter uma subscrição do Azure.
* Crie e configure uma rede virtual Azure e uma subnet dentro da rede virtual.
* Crie e configure as regras do NSG e peer your vNet usando ExpressRoute para a sua Nuvem Privada AVS.
* Crie e configure a sua Nuvem Privada AVS.
* Crie e configure o seu Gateway de Aplicação Azure.

## <a name="azure-application-gateway-deployment-scenario"></a>Cenário de implementação do Gateway de Aplicação Azure

Neste cenário, o Portal de Aplicações Azure funciona na sua rede virtual Azure. A rede virtual está ligada à sua Nuvem Privada AVS sobre um circuito ExpressRoute. Todas as subredes da Nuvem Privada AVS são acessíveis a IP a partir das subredes de rede virtual.

![Equilibrador de carga Azure na rede virtual Azure](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Como implementar a solução

O processo de implantação consiste nas seguintes tarefas:

1. [Verifique se os pré-requisitos são cumpridos](#1-verify-prerequisites)
2. [Ligue a sua ligação virtual Azure à Nuvem Privada AVS](#2-connect-your-azure-virtual-network-to-your-avs-private-cloud)
3. [Implementar um portal de aplicação Azure](#3-deploy-an-azure-application-gateway)
4. [Crie e configure a piscina VM do Web Server na sua Nuvem Privada AVS](#4-create-and-configure-a-web-server-vm-pool-in-your-avs-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Verificar os pré-requisitos

Verifique se estes pré-requisitos são cumpridos:

* Já foi criado um Gestor de Recursos Azure e uma rede virtual.
* Já foi criada uma subnet dedicada (para Application Gateway) dentro da sua rede virtual Azure.
* Já foi criada uma Nuvem Privada AVS.
* Não existe um conflito ip entre subredes IP na rede virtual e subnets na Nuvem Privada.

## <a name="2-connect-your-azure-virtual-network-to-your-avs-private-cloud"></a>2. Ligue a sua rede virtual Azure à sua Nuvem Privada AVS

Para ligar a sua rede virtual Azure à sua Nuvem Privada AVS, siga este processo.

1. [No portal AVS, copie a informação de peering ExpressRoute](virtual-network-connection.md).

2. [Configure uma porta de entrada de rede virtual para a sua rede virtual Azure.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

3. [Ligue a sua rede virtual ao circuito AVS ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Utilize as informações de observação que copiou para ligar a sua rede virtual ao circuito ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Implementar um portal de aplicação Azure

As instruções detalhadas para tal estão disponíveis na [Create a application gateway com regras de encaminhamento baseadas em caminhos utilizando o portal Azure](../application-gateway/create-url-route-portal.md). Aqui está um resumo dos passos necessários:

1. Crie uma rede virtual no seu grupo de subscrição e recursos.
2. Crie uma sub-rede (a ser utilizada como sub-rede dedicada) dentro da sua rede virtual.
3. Crie um Gateway de Aplicação padrão (opcionalmente ativar o WAF): A partir da página inicial do portal Azure, clique em **Recursos** > **Networking** > **Application Gateway** do lado superior esquerdo da página. Selecione o SKU padrão, tamanho, e forneça a subscrição do Azure, grupo de recursos e informações de localização. Se necessário, crie um novo IP público para esta porta de entrada de aplicações e forneça detalhes sobre a rede virtual e a subnet dedicada para o gateway da aplicação.
4. Adicione uma piscina de backend com máquinas virtuais e adicione-a ao seu gateway de aplicação.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-avs-private-cloud"></a>4. Crie e configure uma piscina VM do servidor web na sua Nuvem Privada AVS

No vCenter, crie VMs com o OS e o servidor web à sua escolha (como Windows/IIS ou Linux/Apache). Escolha uma sub-rede/VLAN que seja designada para o nível web na sua Nuvem Privada AVS. Verifique se pelo menos um vNIC dos VMs do servidor web está na sub-rede de nível web.
