---
title: Solução Azure VMware by CloudSimple - Ligue a nuvem privada à rede Azure utilizando a ExpressRoute
description: Descreve como ligar o seu ambiente CloudSimple Private Cloud à rede virtual Azure usando o ExpressRoute
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015240"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Conecte o seu ambiente CloudSimple Private Cloud à rede virtual Azure utilizando o ExpressRoute

A cloudSimple Private Cloud pode ser ligada à sua rede virtual Azure utilizando o Azure ExpressRoute.  Esta elevada largura de banda e baixa ligação de latência permite-lhe aceder a serviços em funcionamento na sua subscrição Azure a partir do seu ambiente Private Cloud.

A ligação de rede virtual permite::

* Utilize o Azure como alvo de reserva para máquinas virtuais na sua Nuvem Privada.
* Implemente servidores KMS na sua subscrição Azure para encriptar a sua loja de dados Private Cloud vSAN.
* Utilize aplicações híbridas onde o nível web da aplicação funciona na nuvem pública enquanto os níveis de aplicação e base de dados funcionam na sua Nuvem Privada.

![Ligação Azure ExpressRoute à rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Criar uma ligação de rede virtual

Para configurar a ligação de rede virtual à sua Cloud Privada, necessita da sua chave de autorização, circuito de pares URI e acesso à sua subscrição Azure. Esta informação está disponível na página de Ligação de Rede Virtual no portal CloudSimple. Para obter instruções, consulte [Obter informações de procura de rede virtual Azure para a ligação CloudSimple](virtual-network-connection.md). Se tiver algum problema em obter a informação, envie um pedido de <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">apoio</a>.

> [!TIP]
> Se já tem uma rede virtual Azure, subnet gateway e gateway de rede virtual, pode saltar para o passo 4.

1. Crie uma rede virtual na subscrição do Azure e verifique se o espaço de endereço que seleciona é diferente do espaço de endereço da sua Cloud Privada.  Se já tem uma rede virtual Azure, pode utilizar a existente.  Para mais detalhes, consulte [Criar uma rede virtual utilizando o portal Azure.](../virtual-network/quick-create-portal.md)
2. Crie a sub-rede gateway na sua rede virtual Azure.  Se já tem uma subrede de gateway na sua rede virtual Azure, pode utilizar a existente. Para mais detalhes, consulte [Criar a sub-rede gateway](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Crie o portal de rede virtual na sua rede virtual.  Se tiver um portal de rede virtual existente, pode utilizar o existente. Para mais detalhes, consulte [Criar o portal da rede virtual](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Crie a ligação entre a sua rede virtual e a sua Nuvem Privada, resgatando a chave de autorização descrita no [Connect a virtual network a um circuito - subscrição diferente](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Se estiver a utilizar um portal de rede virtual existente e tiver uma ligação ExpressRoute ao mesmo local que o circuito CloudSimple ExpressRoute, a ligação não será estabelecida.  Crie uma nova rede virtual e siga os passos anteriores.

## <a name="test-the-virtual-network-connection"></a>Testar a ligação virtual da rede

Após a criação da ligação, pode verificar o estado da ligação selecionando **Propriedades** em **Definições**.  O Estado de Estatuto e o Estado de Provisionamento devem **mostrar-se bem sucedidos**.

![Estado de Ligação](media/azure-expressroute-connection.png)

Para testar a ligação de rede virtual:

1. Crie uma máquina virtual na sua subscrição Azure.
2. Encontre o endereço IP do seu vCenter Private Cloud (consulte o seu e-mail de boas-vindas).
3. Ping your Cloud vCenter a partir da máquina virtual criada na sua rede virtual Azure.
4. Ping a sua máquina virtual Azure a partir de uma máquina virtual que funciona no seu vCenter De Nuvem Privada.

Se tiver algum problema que estabeleça a ligação, apresente um pedido de <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">apoio</a>.
