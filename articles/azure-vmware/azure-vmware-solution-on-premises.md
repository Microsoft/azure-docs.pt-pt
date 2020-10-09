---
title: Ligue a Solução Azure VMware ao seu ambiente no local
description: Saiba como ligar a Solução VMware Azure ao seu ambiente no local.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 2a0cb641df00f3e580e87e38aff382d8e8101fc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91583424"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Ligue a Solução Azure VMware ao seu ambiente no local

Neste artigo, continuará a utilizar as [informações recolhidas durante](production-ready-deployment-steps.md) o planeamento de ligar a Solução Azure VMware ao seu ambiente no local.

Antes de começar, existem dois pré-requisitos para ligar a Solução Azure VMware ao seu ambiente no local:

- Um circuito ExpressRoute do seu ambiente no local para Azure.
- Um bloco de endereços de rede não sobreposto /29 para o espreitamento expressRoute Global Reach, que definiu como parte da fase de [planeamento](production-ready-deployment-steps.md).

>[!NOTE]
> Pode ligar-se via VPN, mas isso está fora de alcance para este documento de arranque rápido.

## <a name="establish-an-expressroute-global-reach-connection"></a>Estabelecer uma ligação ExpressRoute Global Reach

Para estabelecer conectividade no local com a sua nuvem privada Azure VMware Solution utilizando o ExpressRoute Global Reach, siga os ambientes do Peer no local para um tutorial [de nuvem privada.](tutorial-expressroute-global-reach-private-cloud.md)



## <a name="verify-on-premises-network-connectivity"></a>Verificar a conectividade da rede no local

Deverá agora ver no seu **router de borda no local** onde o ExpressRoute liga os segmentos de rede NSX-T e os segmentos de gestão da Solução VMware Azure.

>[!NOTE]
>Todos têm um ambiente diferente, e alguns terão de permitir que estas rotas se propaguem de volta à rede no local.  

Alguns ambientes terão firewalls protegendo o circuito ExpressRoute.  Se não houver firewalls e nenhuma poda de rota, pode pingar o servidor VMware Solution vCenter Azure ou um [VM](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) no segmento NSX-T a partir do seu ambiente no local.

Além disso, a partir do VM no segmento NSX-T, você pode alcançar recursos no seu ambiente no local.

## <a name="next-steps"></a>Passos seguintes

Continue até a secção seguinte para implementar e configurar VMware HCX

> [!div class="nextstepaction"]
> [Implementar e configurar o VMware HCX](tutorial-deploy-vmware-hcx.md)