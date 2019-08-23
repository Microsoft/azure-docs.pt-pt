---
title: Solução do Azure VMware por conexão CloudSimple-local usando o ExpressRoute
description: Descreve como solicitar uma conexão local usando o ExpressRoute da rede CloudSimple Region
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee359b76072da3caee9ae1f5fab3d0fc28d25c0e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972694"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Conectar de local para CloudSimple usando o ExpressRoute

Se você já tiver uma conexão do ExpressRoute do Azure de um local externo (como no local) para o Azure, você poderá conectá-lo ao seu ambiente CloudSimple. Você pode fazer isso por meio de um recurso do Azure que permite que dois circuitos do ExpressRoute se conectem entre si. Esse método estabelece uma conexão segura, privada, de alta largura de banda e baixa latência entre os dois ambientes.

[![Conexão do ExpressRoute local-Alcance Global](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="prerequisites"></a>Pré-requisitos

* Um circuito do Azure ExpressRoute é necessário antes que você possa estabelecer a conexão entre o circuito e as redes de nuvem privada CloudSimple.
* Um usuário é necessário com privilégios para criar chaves de autorização em um circuito do ExpressRoute.

## <a name="scenarios"></a>Cenários

Conectar sua rede local à sua rede de nuvem privada permite que você use a nuvem privada de várias maneiras, incluindo as seguintes:

* Acesse sua rede de nuvem privada sem criar uma conexão VPN site a site.
* Use seu Active Directory local como uma fonte de identidade em sua nuvem privada.
* Migre máquinas virtuais em execução localmente para sua nuvem privada.
* Use sua nuvem privada como parte de uma solução de recuperação de desastre.
* Consuma recursos locais em suas VMs de carga de trabalho de nuvem privada.

## <a name="connecting-expressroute-circuits"></a>Conectando circuitos do ExpressRoute

Para estabelecer a conexão do ExpressRoute, você deve criar uma autorização no circuito do ExpressRoute e fornecer as informações de autorização para CloudSimple.

### <a name="create-expressroute-authorization"></a>Criar autorização de ExpressRoute

1. Inicie sessão no Portal do Azure.

2. Na barra de pesquisa superior, pesquise o **circuito do expressroute** e clique em **circuitos do expressroute** em **Serviços**.
    [![Circuitos do ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selecione o circuito do ExpressRoute que você pretende conectar à sua rede CloudSimple.

4. Na página ExpressRoute, clique em **autorizações**, insira um nome para a autorização e clique em **salvar**.
    [![Autorização de circuito do ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Copie a ID de recurso e a chave de autorização clicando no ícone de cópia. Cole a ID e a chave em um arquivo de texto.
    [![Cópia de autorização de circuito do ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > A **ID do recurso** deve ser copiada da interface do usuário e deve ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` estar no formato quando você fornecê-la para dar suporte ao.

6. Arquivo um tíquete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">suporte</a> para a conexão a ser criada.
    * Tipo de problema: **Technical**
    * Subscrição: **Assinatura em que o serviço CloudSimple está implantado**
    * Serviço: **Solução VMware por CloudSimple**
    * Tipo de problema: **Solicitação de serviço**
    * Subtipo de problema: **Criar conexão do ExpressRoute para o local**
    * Forneça a ID de recurso e a chave de autorização que você copiou e salvou no painel de detalhes.
