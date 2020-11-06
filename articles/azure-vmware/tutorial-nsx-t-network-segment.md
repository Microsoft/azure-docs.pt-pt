---
title: Tutorial - Adicione um segmento de rede NSX-T na Solução VMware Azure
description: Aprenda a criar um segmento de rede NSX-T para utilizar para máquinas virtuais (VMs) em vCenter.
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 8ecb37a42e2986bd1c6261b8fe6c23382323b31d
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335053"
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
