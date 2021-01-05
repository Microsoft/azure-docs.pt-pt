---
title: Use gateway de aplicação Azure com máquinas virtuais VMware
description: Descreve como usar o gateway de aplicações Azure para gerir o tráfego web de entrada para servidores web em execução em máquinas virtuais VMware ganham o ambiente CloudSimple Private Cloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d14fdc4e2b2b1dfca8194077acc0453808611946
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897948"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Utilize gateway de aplicação Azure com máquinas virtuais VMware no ambiente CloudSimple Private Cloud

Pode utilizar o Azure Application Gateway para gerir o tráfego web de entrada para os seus servidores web em execução em máquinas virtuais VMware dentro do seu ambiente CloudSimple Private Cloud.

Aproveitando o Azure Application Gateway numa implementação híbrida público-privada, pode gerir o tráfego web para as suas aplicações, fornecer um front-end seguro e descarregar o processamento de TLS para os seus serviços em funcionamento em ambiente VMware. O Azure Application Gateway encaminha o tráfego web para retominar as instâncias de piscina que residem em ambientes VMware de acordo com regras configuradas e sondas de saúde.

Esta solução Azure Application Gateway requer que:

* Ter uma subscrição do Azure.
* Crie e configuure uma rede virtual Azure e uma sub-rede dentro da rede virtual.
* Crie e configuure as regras NSG e persiga o seu vNet usando o ExpressRoute para a sua CloudSimple Private Cloud.
* Crie & configurar a sua Nuvem Privada.
* Crie & configurar o seu Gateway de aplicações Azure.

## <a name="azure-application-gateway-deployment-scenario"></a>Cenário de implementação do Gateway de aplicação Azure

Neste cenário, o Azure Application Gateway funciona na sua rede virtual Azure. A rede virtual está ligada à sua Nuvem Privada sobre um circuito ExpressRoute. Todas as sub-redes da Nuvem Privada são IP contactáveis a partir das sub-redes de rede virtuais.

![Equilibr de carga Azure na rede virtual Azure](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Como implementar a solução

O processo de implantação consiste nas seguintes tarefas:

1. [Verifique se os pré-requisitos são cumpridos](#1-verify-prerequisites)
2. [Ligue a sua ligação virtual Azure à Nuvem Privada](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Implementar um gateway de aplicações Azure](#3-deploy-an-azure-application-gateway)
4. [Crie e configuure piscina VM do Servidor Web na sua Nuvem Privada](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Verificar os pré-requisitos

Verifique se estes pré-requisitos são cumpridos:

* Já está criado um Gestor de Recursos Azure e uma rede virtual.
* Uma sub-rede dedicada (para Gateway de aplicações) dentro da sua rede virtual Azure já está criada.
* Uma Nuvem Privada CloudSimple já está criada.
* Não existe conflito IP entre sub-redes IP na rede virtual e sub-redes na Nuvem Privada.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Ligue a sua rede virtual Azure à sua Nuvem Privada

Para ligar a sua rede virtual Azure à sua Nuvem Privada, siga este processo.

1. [No portal CloudSimple, copie a informação de espreitar ExpressRoute](virtual-network-connection.md).

2. [Configure uma porta de entrada de rede virtual para a sua rede virtual Azure.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

3. [Ligue a sua rede virtual ao circuito CloudSimple ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Utilize as informações de espreitamento que copiou para ligar a sua rede virtual ao circuito ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Implementar um gateway de aplicações Azure

As instruções detalhadas para tal estão disponíveis na [Criar um gateway de aplicações com regras de encaminhamento baseadas em caminhos utilizando o portal Azure](../application-gateway/create-url-route-portal.md). Aqui está um resumo das etapas necessárias:

1. Crie uma rede virtual no seu grupo de subscrição e recursos.
2. Crie uma sub-rede (a ser usada como sub-rede dedicada) dentro da sua rede virtual.
3. Crie um Gateway de aplicação padrão (opcionalmente ativar o WAF): A partir da página inicial do portal Azure, clique em **Gateway** de Aplicação de Rede de Recursos  >    >  a partir do lado superior esquerdo da página. Selecione o SKU padrão e o tamanho e forneça informações de subscrição, grupo de recursos e localização da Azure. Se necessário, crie um novo IP público para este gateway de aplicações e forneça detalhes sobre a rede virtual e a sub-rede dedicada para o gateway de aplicações.
4. Adicione uma piscina de backend com máquinas virtuais e adicione-a ao seu gateway de aplicações.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Crie e configura uma piscina VM do servidor web na sua Nuvem Privada

No vCenter, crie VMs com o sistema operativo e servidor web à sua escolha (como Windows/IIS ou Linux/Apache). Escolha uma sub-rede/VLAN que seja designada para o nível web na sua Nuvem Privada. Verifique se pelo menos um vNIC dos VMs do servidor web está na sub-rede de nível web.
