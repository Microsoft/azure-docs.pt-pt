---
title: Azure VMware Solution by CloudSimple - Ligação no local usando ExpressRoute
description: Descreve como solicitar uma ligação no local utilizando o ExpressRoute da rede da região CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 27d1d9e54838d9f45a28d634fa1c24fb8785aee1
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899240"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Ligue-se de instalações para CloudSimple usando ExpressRoute

Se já tiver uma ligação Azure ExpressRoute de uma localização externa (como no local) para Azure, pode ligá-la ao seu ambiente CloudSimple. Pode fazê-lo através de uma funcionalidade Azure que permite que dois circuitos ExpressRoute se conectem entre si. Este método estabelece uma ligação segura, privada, de alta largura de banda, baixa latência entre os dois ambientes.

[![Ligação ExpressRoute - Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Antes de começar

É necessário um bloco de endereços de rede **/29** para estabelecer a ligação Global Reach a partir do local.  O espaço de endereço /29 é utilizado para a rede de trânsito entre os circuitos ExpressRoute.  A rede de trânsito não deve sobrepor-se a nenhuma das suas redes virtuais Azure, redes no local ou redes CloudSimple Private Cloud.

## <a name="prerequisites"></a>Pré-requisitos

* É necessário um circuito Azure ExpressRoute antes de poder estabelecer a ligação entre o circuito e as redes CloudSimple Private Cloud.
* É necessário um utilizador com privilégios para criar chaves de autorização num circuito ExpressRoute.

## <a name="scenarios"></a>Cenários

Ligar a sua rede no local à sua rede Private Cloud permite-lhe utilizar a Nuvem Privada de várias formas, incluindo os seguintes cenários:

* Aceda à sua rede Private Cloud sem criar uma ligação VPN site-to-site.
* Use o seu Ative Directory no local como fonte de identidade na sua Nuvem Privada.
* Migrar máquinas virtuais que correm no local para a sua Nuvem Privada.
* Use a sua Nuvem Privada como parte de uma solução de recuperação de desastres.
* Consuma recursos no local nos seus VMs de carga de trabalho private Cloud.

## <a name="connecting-expressroute-circuits"></a>Ligação dos circuitos ExpressRoute

Para estabelecer a ligação ExpressRoute, deve criar uma autorização no seu circuito ExpressRoute e fornecer as informações de autorização à CloudSimple.


### <a name="create-expressroute-authorization"></a>Criar autorização ExpressRoute

1. Inicie sessão no Portal do Azure.

2. A partir da barra de pesquisa superior, procure o **circuito ExpressRoute** e clique nos **circuitos ExpressRoute** em **Serviços**.
    [![Circuitos ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selecione o circuito ExpressRoute que pretende ligar à sua rede CloudSimple.

4. Na página ExpressRoute, clique em **Autorizações, insira** um nome para a autorização e clique em **Guardar**.
    [![Autorização do Circuito ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Copie o ID do recurso e a chave de autorização clicando no ícone de cópia. Cole o ID e a chave num ficheiro de texto.
    [![Cópia de autorização do circuito ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **O ID do recurso** deve ser copiado da UI e deve estar no formato quando o ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` fornece para suporte.

6. Arquivar um bilhete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Suporte</a> para a ligação a ser criada.
    * Tipo de emissão: **Técnico**
    * Subscrição: **Subscrição onde o serviço CloudSimple é implementado**
    * Serviço: **Solução VMware by CloudSimple**
    * Tipo de problema: **Pedido de serviço**
    * Subtipo de problema: **Criar ligação ExpressRoute às instalações**
    * Forneça a chave de identificação e autorização de recursos que copiou e guardou no painel de detalhes.
    * Forneça um espaço de endereço de rede /29 para a rede de trânsito.
    * Está a enviar a rota padrão através do ExpressRoute?
    * O tráfego private Cloud deve utilizar a rota predefinida enviada através do ExpressRoute?

    > [!IMPORTANT]
    > O envio de uma rota predefinidora permite-lhe enviar todo o tráfego de internet da Private Cloud utilizando a sua ligação à Internet no local.  Para desativar a rota predefinida configurada na Nuvem Privada e utilizar a rota padrão de ligação no local, forneça os detalhes no bilhete de suporte.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre as ligações à rede Azure](cloudsimple-azure-network-connection.md)  
