---
title: Azure VMware Solution by CloudSimple - Conecte a nuvem privada à rede Azure utilizando o ExpressRoute
description: Descreve como ligar o seu ambiente CloudSimple Private Cloud à rede virtual Azure utilizando o ExpressRoute
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 98eec6b48d2b02c6eb7f6faea38994a9176bcf87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895993"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Ligue o seu ambiente CloudSimple Private Cloud à rede virtual Azure utilizando o ExpressRoute

A cloudSimple Private Cloud pode ser ligada à sua rede virtual Azure utilizando o Azure ExpressRoute.  Esta alta largura de banda, ligação de baixa latência permite-lhe aceder a serviços em execução na sua subscrição Azure a partir do seu ambiente Private Cloud.

A ligação de rede virtual permite-lhe:

* Utilize o Azure como alvo de reserva para máquinas virtuais na sua Nuvem Privada.
* Implemente servidores KMS na sua subscrição Azure para encriptar a sua loja de dados Private Cloud vSAN.
* Utilize aplicações híbridas onde o nível web da aplicação é executado na nuvem pública enquanto os níveis de aplicação e base de dados funcionam na sua Nuvem Privada.

![Ligação Azure ExpressRoute à rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Configurar uma ligação de rede virtual

Para configurar a ligação de rede virtual à sua Nuvem Privada, precisa da sua chave de autorização, circuito de pares URI e acesso à sua subscrição Azure. Esta informação está disponível na página De Ligação de Rede Virtual no portal CloudSimple. Para obter instruções, consulte [obter informações de espreitar a rede virtual Azure para a ligação CloudSimple](virtual-network-connection.md). Se tiver algum problema em obter a informação, envie um pedido de <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">apoio</a>.

> [!TIP]
> Se já tem uma rede virtual Azure, uma sub-rede de gateway e um gateway de rede virtual, pode saltar para o passo 4.

1. Crie uma rede virtual na sua subscrição Azure e verifique se o espaço de endereço selecionado é diferente do espaço de endereço da sua Cloud Privada.  Se já tem uma rede virtual Azure, pode utilizar a existente.  Para mais detalhes, consulte [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).
2. Crie a sub-rede gateway na sua rede virtual Azure.  Se já tiver uma sub-rede de gateway na sua rede virtual Azure, pode utilizar a existente. Para mais detalhes, consulte [Criar a sub-rede do gateway](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Crie o portal de rede virtual na sua rede virtual.  Se tiver uma porta de entrada de rede virtual existente, pode utilizar a existente. Para mais detalhes, consulte [Criar o gateway de rede virtual.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)
4. Crie a ligação entre a sua rede virtual e a sua Nuvem Privada resgatando a chave de autorização descrita como descrita no [Connect uma rede virtual a um circuito - subscrição diferente](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Se estiver a utilizar um gateway de rede virtual existente e tiver uma ligação ExpressRoute para o mesmo local que o circuito CloudSimple ExpressRoute, a ligação não será estabelecida.  Crie uma nova rede virtual e siga os passos anteriores.

## <a name="test-the-virtual-network-connection"></a>Testar a ligação de rede virtual

Após a criação da ligação, pode verificar o estado da ligação selecionando **propriedades** em **Definições**.  O Estado e o Estado de Provisionamento devem mostrar **Que Foi Bem sucedido**.

![Estado da Ligação](media/azure-expressroute-connection.png)

Para testar a ligação de rede virtual:

1. Crie uma máquina virtual na sua subscrição Azure.
2. Encontre o endereço IP do seu Private Cloud vCenter (consulte o seu e-mail de boas-vindas).
3. Ping your Cloud vCenter a partir da máquina virtual criada na sua rede virtual Azure.
4. Pingar a sua máquina virtual Azure a partir de uma máquina virtual que funciona no seu VCenter Private Cloud.

Se tiver algum problema que estabeleça a ligação, envie um pedido de <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">apoio</a>.
