---
title: Ligue a Solução Azure VMware ao seu ambiente no local
description: Saiba como ligar a Solução VMware Azure ao seu ambiente no local.
ms.topic: tutorial
ms.date: 04/19/2021
ms.openlocfilehash: 392d82a9aca9b60b394a5d5f4a7e6b0111438e59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725624"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Ligue a Solução Azure VMware ao seu ambiente no local

Neste artigo, continuará a utilizar as [informações recolhidas durante](production-ready-deployment-steps.md) o planeamento de ligar a Solução Azure VMware ao seu ambiente no local.

Antes de começar, deve ter um circuito ExpressRoute do seu ambiente no local para Azure.


>[!NOTE]
> Pode ligar através da VPN, mas isso está fora de alcance para este documento de arranque rápido.

## <a name="establish-an-expressroute-global-reach-connection"></a>Estabelecer uma ligação ExpressRoute Global Reach

Para estabelecer conectividade no local com a sua nuvem privada Azure VMware Solution utilizando o ExpressRoute Global Reach, siga os ambientes do Peer no local para um tutorial [de nuvem privada.](tutorial-expressroute-global-reach-private-cloud.md)

Este tutorial resulta numa ligação como mostrado no diagrama.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Global Reach no local diagrama de conectividade de rede." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>Verificar a conectividade da rede no local

Deverá agora ver no seu **router de borda no local** onde o ExpressRoute liga os segmentos de rede NSX-T e os segmentos de gestão da Solução VMware Azure.

>[!IMPORTANT]
>Todos têm um ambiente diferente, e alguns terão de permitir que estas rotas se propaguem de volta à rede no local.  

Alguns ambientes têm firewalls que protegem o circuito ExpressRoute.  Se não ocorrerem firewalls e nenhuma poda de rota, pingar o servidor VMware Solution vCenter Azure ou um [VM no segmento NSX-T](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) a partir do ambiente no local. Além disso, a partir do VM no segmento NSX-T, você pode alcançar recursos no seu ambiente no local.

## <a name="next-steps"></a>Passos seguintes

Continue até a secção seguinte para implementar e configurar VMware HCX

> [!div class="nextstepaction"]
> [Implementar e configurar o VMware HCX](tutorial-deploy-vmware-hcx.md)