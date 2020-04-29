---
title: Solução Azure VMware by CloudSimple - Ligação no local utilizando expressRoute
description: Descreve como solicitar uma ligação no local usando expressRoute da rede da região CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77019626"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Ligue-se a partir das instalações para a CloudSimple usando o ExpressRoute

Se já tem uma ligação Azure ExpressRoute de uma localização externa (como no local) para o Azure, pode ligá-la ao seu ambiente CloudSimple. Pode fazê-lo através de uma funcionalidade Azure que permite que dois circuitos ExpressRoute se conectem entre si. Este método estabelece uma ligação segura, privada, alta largura de banda, baixa latência entre os dois ambientes.

[![Ligação ExpressRoute no local - Alcance Global](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Antes de começar

É necessário um bloco de endereços de rede **/29** para estabelecer a ligação Global Reach a partir do local.  O espaço de endereço /29 é utilizado para a rede de trânsito entre os circuitos ExpressRoute.  A rede de trânsito não deve sobrepor-se a nenhuma das suas redes virtuais Azure, redes no local ou redes CloudSimple Private Cloud.

## <a name="prerequisites"></a>Pré-requisitos

* É necessário um circuito Azure ExpressRoute antes de poder estabelecer a ligação entre o circuito e as redes CloudSimple Private Cloud.
* É necessário um utilizador com privilégios para criar chaves de autorização num circuito ExpressRoute.

## <a name="scenarios"></a>Cenários

Ligar a sua rede no local à sua rede Private Cloud permite-lhe utilizar a Cloud Privada de várias formas, incluindo os seguintes cenários:

* Aceda à sua rede Private Cloud sem criar uma ligação VPN site-to-site.
* Use o seu Diretório Ativo no local como fonte de identidade na sua Nuvem Privada.
* Migrar máquinas virtuais que executam no local para a sua Nuvem Privada.
* Use a sua Nuvem Privada como parte de uma solução de recuperação de desastres.
* Consuma recursos no local nos seus VMs de carga de trabalho em Nuvem Privada.

## <a name="connecting-expressroute-circuits"></a>Ligação dos circuitos ExpressRoute

Para estabelecer a ligação ExpressRoute, deve criar uma autorização no seu circuito ExpressRoute e fornecer as informações de autorização à CloudSimple.


### <a name="create-expressroute-authorization"></a>Criar autorização ExpressRoute

1. Inicie sessão no Portal do Azure.

2. A partir da barra de pesquisa superior, procure o **circuito ExpressRoute** e clique nos **circuitos ExpressRoute** em **Serviços**.
    [![Circuitos ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selecione o circuito ExpressRoute que pretende ligar à sua rede CloudSimple.

4. Na página ExpressRoute, clique em **Autorizações,** insira um nome para a autorização e clique em **Guardar**.
    [![Autorização do Circuito ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Copie o ID de recurso e a chave de autorização clicando no ícone da cópia. Cola a identificação e a chave num ficheiro de texto.
    [![Cópia de Autorização do Circuito Expressroute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > O ID do **recurso** deve ser copiado ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` da UI e deve estar no formato quando o fornecer para suporte.

6. Preencha um bilhete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Suporte</a> para a ligação a criar.
    * Tipo de emissão: **Técnico**
    * Subscrição: **Subscrição onde o serviço CloudSimple é implementado**
    * Serviço: **Solução VMware by CloudSimple**
    * Tipo de problema: **Pedido de serviço**
    * Subtipo de problemas: **Criar ligação ExpressRoute ao local**
    * Forneça a chave de identificação de recursos e autorização que copiou e guardou no painel de detalhes.
    * Forneça um espaço de endereço de rede /29 para a rede de trânsito.
    * Está a enviar uma rota padrão através do ExpressRoute?
    * O tráfego da Nuvem Privada deve utilizar a rota padrão enviada através do ExpressRoute?

    > [!IMPORTANT]
    > O envio de uma rota padrão permite-lhe enviar todo o tráfego de internet a partir de Private Cloud utilizando a sua ligação à Internet no local.  Para desativar a rota predefinida configurada na Cloud Privada e utilizar a rota padrão de ligação no local, forneça os detalhes no bilhete de apoio.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre as ligações de rede Azure](cloudsimple-azure-network-connection.md)  
