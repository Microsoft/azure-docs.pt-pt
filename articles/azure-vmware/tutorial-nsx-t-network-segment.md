---
title: Tutorial - Criar um segmento de rede NSX-T na Solução VMware Azure
description: Saiba como criar os segmentos de rede NSX-T que são usados para VMs em vCenter
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f88f186d2af10bcc114d64920a3ac489ef7be54f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367674"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Tutorial: Criar um segmento de rede NSX-T na Solução VMware Azure

As máquinas virtuais (VMs) criadas em vCenter são colocadas nos segmentos de rede criados em NSX-T e são visíveis no vCenter.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Navegue no NSX-T Manager para adicionar segmentos de rede
> * Adicione um novo segmento de rede
> * Observe o novo segmento de rede no vCenter

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma nuvem privada Azure VMware Solution com acesso às interfaces de gestão vCenter e NSX-T Manager. Para mais informações, consulte o tutorial [de rede Configure.](tutorial-configure-networking.md)

## <a name="provision-a-network-segment-in-nsx-t"></a>Fornecimento de um segmento de rede em NSX-T

1. No vCenter para a sua nuvem privada, selecione **SDDC-Datacenter > Networks** e note que ainda não existem redes.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

1. No NSX-T Manager para a sua nuvem privada, **selecione Networking**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

1. Selecione **Segmentos**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

1. Na página de visão geral dos segmentos NSX-T, selecione **ADD SEGMENT**. Três segmentos são criados como parte do fornecimento de nuvem privada e não podem ser usados para VMs.  Para o efeito, terá de adicionar um novo segmento de rede.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

1. Nomeie o segmento, escolha o Gateway Tier1 pré-configurado (TNTxx-T1) como **gateway conectado,** deixe o **Tipo** como Flexível, escolha a **zona de transporte** de sobreposição pré-configurada (TNTxx-OVERLAY-TZ) e, em seguida, selecione set Subnets. Todas as outras definições nesta secção e os **PERFIS DE PORTAS** e **SEGMENTOS** podem permanecer no predefinido, como é a configuração.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

1. Desaponuse o endereço IP do gateway para o novo segmento e, em seguida, selecione **ADD**. O endereço IP que utiliza tem de estar num bloco de endereços RFC1918 não sobreposto, o que garante que pode ligar-se aos VMs no novo segmento.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

1. Aplique o novo segmento de rede selecionando **APPLY** e, em seguida, guarde a configuração com **SAVE**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

1. O novo segmento de rede foi agora criado e irá recusar a opção de continuar a configurar o segmento selecionando **NO**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

1. Confirme que o novo segmento de rede está presente no NSX-T **selecionando Networking > Segmentos** e vendo que o novo segmento está na lista (neste caso, "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

1. Confirme que o novo segmento de rede está presente no vCenter **selecionando networking > SDDC-Datacenter** e observando o novo segmento está na lista (neste caso, "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou os segmentos de rede NSX-T que são utilizados para VMs em vCenter. Agora pode [criar uma Biblioteca de conteúdos para implantar VMs na Solução VMware Azure](deploy-vm-content-library.md). Também pode providenciar um VM na rede que criou neste tutorial.

Caso contrário, continue até ao próximo tutorial para aprender a criar o ExpressRoute Global Reach olhando para uma nuvem privada numa Solução VMware Azure.

> [!div class="nextstepaction"]
> [Par no local ambientes para uma nuvem privada](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
