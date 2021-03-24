---
title: Tutorial - Adicione um segmento de rede NSX-T na Solução VMware Azure
description: Aprenda a criar um segmento de rede NSX-T para utilizar para máquinas virtuais (VMs) em vCenter.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9125e552f9641a2d26b9584b66a4447f9c152161
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "103462133"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Tutorial: Adicione um segmento de rede na Solução VMware Azure 

As máquinas virtuais (VMs) criadas em vCenter são colocadas nos segmentos de rede criados em NSX-T e são visíveis no vCenter.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Navegue no NSX-T Manager para adicionar segmentos de rede
> * Adicione um novo segmento de rede
> * Observe o novo segmento de rede no vCenter

## <a name="prerequisites"></a>Pré-requisitos

Uma nuvem privada Azure VMware Solution com acesso às interfaces vCenter e NSX-T Manager. Para mais informações, consulte o tutorial [de rede Configure.](tutorial-configure-networking.md)

## <a name="add-a-network-segment"></a>Adicionar um segmento de rede

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um segmento de rede NSX-T para usar para VMs em vCenter. 

Agora pode: 

- [Criar e gerir o DHCP para a Azure VMware Solution](manage-dhcp.md)
- [Criar uma Biblioteca de Conteúdos para implantar VMs na Solução VMware Azure](deploy-vm-content-library.md) 
- [Par no local ambientes para uma nuvem privada](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
