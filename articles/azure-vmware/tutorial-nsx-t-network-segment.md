---
title: 'Tutorial: Criar um segmento de rede NSX-T na Solução VMware Azure'
description: Neste tutorial, criou os segmentos de rede NSX-T que são usados para VMs em vCenter
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: cee65211cbef25ec029c68888bc8e6059f7c7896
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750459"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Tutorial: Criar um segmento de rede NSX-T na Solução VMware Azure

Os segmentos de rede criados no NSX-T Manager são utilizados como redes para máquinas virtuais (VMs) em vCenter. Os VMs criados em vCenter são colocados nos segmentos de rede criados em NSX-T e são visíveis no vCenter.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Navegue no NSX-T Manager para adicionar segmentos de rede
> * Adicione um novo segmento de rede
> * Observe o novo segmento de rede no vCenter

## <a name="prerequisites"></a>Pré-requisitos

Uma nuvem privada Azure VMware Solution com acesso às interfaces de gestão vCenter e NSX-T Manager são necessárias para completar este tutorial. Consulte o [Tutorial: Configurar a rede para a sua nuvem privada VMware em Azure](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Fornecimento de um segmento de rede em NSX-T

1. No vCenter para a sua nuvem privada, selecione **SDDC-Datacenter > Networks** e note que ainda não existem redes.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="No vCenter para a sua nuvem privada, selecione SDDC-Datacenter > Networks e note que ainda não existem redes.":::

1. No NSX-T Manager para a sua nuvem privada, **selecione Networking**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="No NSX-T Manager para a sua nuvem privada, selecione Networking.":::

1. Selecione **Segmentos**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Selecione Segmentos na página 'Visão Geral' da rede.":::

1. Na página de visão geral dos segmentos NSX-T, selecione **ADD SEGMENT**. Três segmentos são criados como parte do fornecimento de nuvem privada e não podem ser usados para VMs.  Para o efeito, terá de adicionar um novo segmento de rede.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Na página de visão geral dos segmentos NSX-T, selecione ADD SEGMENT.":::

1. Nomeie o segmento, escolha o Gateway Tier1 pré-configurado (TNTxx-T1) como **gateway conectado,** deixe o **Tipo** como Flexível, escolha a **zona de transporte** de sobreposição pré-configurada (TNTxx-OVERLAY-TZ) e, em seguida, selecione set Subnets. Todas as outras definições nesta secção e os **PERFIS DE PORTAS** e **SEGMENTOS** podem permanecer no predefinido, como é a configuração.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Desa estaga o nome do segmento, o gateway conectado e o tipo e a zona de transporte, em seguida, selecione set Subnet.":::

1. Desaponuse o endereço IP do gateway para o novo segmento e, em seguida, selecione **ADD**. O endereço IP que utiliza tem de estar num bloco de endereços RFC1918 não sobreposto, o que garante que pode ligar-se aos VMs no novo segmento.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Desaponuse o endereço IP do gateway para o novo segmento e, em seguida, selecione ADD.":::

1. Aplique o novo segmento de rede selecionando **APPLY** e, em seguida, guarde a configuração com **SAVE**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Aplique o novo segmento de rede na configuração NSX-T com APPLY.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Guarde o novo segmento de rede para a configuração NSX-T com SAVE.":::

1. O novo segmento de rede foi agora criado e irá recusar a opção de continuar a configurar o segmento selecionando **NO**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Recuse-se a configurar ainda mais o segmento de rede recém-criado selecionando NO.":::

1. Confirme que o novo segmento de rede está presente no NSX-T **selecionando Networking > Segmentos** e vendo que o novo segmento está na lista (neste caso, "ls01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Confirme que o novo segmento de rede está presente no NSX-T.":::

1. Confirme que o novo segmento de rede está presente no vCenter **selecionando networking > SDDC-Datacenter** e observando o novo segmento está na lista (neste caso, "ls01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Confirme que o novo segmento de rede está presente no vCenter.":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou os segmentos de rede NSX-T que são utilizados para VMs em vCenter. Agora pode utilizar o [Tutorial: Criar uma Biblioteca de conteúdos para implantar VMs na Solução VMware Azure](tutorial-deploy-vm-content-library.md) para criar uma Biblioteca de Conteúdos em vCenter e a disponibilização de um VM na rede que criou neste tutorial.

<!-- LINKS - external-->

<!-- LINKS - internal -->
