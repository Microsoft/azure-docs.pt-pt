---
title: Adicione um segmento de rede NSX-T
description: Passos para adicionar um segmento de rede NSX-T para Azure VMware Solution.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 14d698413d31af2dcbbdea5f37ec7f24f65199ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462134"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. No NSX-T Manager, selecione Segmentos **de Rede**  >  e, em seguida, selecione **Add Segment**. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Screenshot mostrando como adicionar um novo segmento":::

1. Insira um nome para o segmento.

1. Selecione o Gateway Tier-1 (TNTxx-T1) como gateway **conectado** e deixe o **Tipo** como Flexível.

1. Selecione a zona de **transporte** pré-configurada (TNTxx-OVERLAY-TZ) e, em seguida, selecione **set Subnets**. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Desa estaga o nome do segmento, o gateway conectado e o tipo e a zona de transporte, em seguida, selecione set Subnet.":::

1. Introduza o endereço IP do gateway e, em seguida, **selecione Adicionar**. 

   >[!IMPORTANT]
   >O endereço IP tem de estar num bloco de endereços RFC1918 não sobreposto, que garante a ligação aos VMs no novo segmento.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Desaponuse o endereço IP do gateway para o novo segmento e, em seguida, selecione ADD.":::

1. **Selecione Aplicar** e, em seguida, **Guardar**.

1. Selecione **Não** para recusar a opção de continuar a configurar o segmento. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Recuse-se a configurar ainda mais o segmento de rede recém-criado selecionando NO.":::

1. Confirme a presença do novo segmento de rede. Neste exemplo, **o LS01** é o novo segmento de rede.

   1. No NSX-T Manager, selecione   >  **Segmentos** de Rede . 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Confirme que o novo segmento de rede está presente no NSX-T.":::

   1. No vCenter, **selecione Networking > SDDC-Datacenter**.

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Confirme que o novo segmento de rede está presente no vCenter.":::