---
title: Quota de máquina virtual spot
description: Aumente os limites de quota para os VMs spot, que fornecem um modelo de utilização Azure que permite assumir custos mais baixos em troca de deixar o Azure remover VMs conforme necessário.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745490"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Quota à vista: Aumentar os limites para todas as séries VM

As máquinas virtuais spot (VMs) fornecem um modelo diferente de utilização do Azure. Eles permitem-lhe assumir custos mais baixos em troca de deixar a Azure remover máquinas virtuais, conforme necessário para implantações de instâncias VM pagas ou reservadas. Para obter mais informações sobre VMs spot, consulte [VMs spot Azure para conjuntos de escala de máquina virtual](../../virtual-machine-scale-sets/use-spot.md).

O Azure Resource Manager suporta dois tipos de quotas vCPU para máquinas virtuais:

* *Os VMs pay-as-you-go* e *as instâncias VM reservadas* estão sujeitos a uma *quota padrão vCPU*.
* *Os VM spot* estão sujeitos a uma *quota vCPU à vista.*

Para o tipo de quota spot vCPU, as quotas do Gestor de Recursos vCPU são aplicadas em todas as séries de máquinas virtuais disponíveis como um único limite regional.

Sempre que implementar um novo spot VM, a utilização total de vCPU novo e existente para todos os casos de VM spot não deve exceder o limite de quota vCPU aprovado. Se a quota à vista for excedida, a colocação de VM no local não é permitida.

Este artigo discute como solicitar um aumento do limite de quota spot vCPU através do portal Azure.

Para saber mais sobre as quotas padrão vCPU, consulte [as quotas vCPU da máquina virtual](../../virtual-machines/windows/quotas.md) e os [limites de subscrição e serviço da Azure, quotas e restrições.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

Para conhecer o aumento do limite vCPU por região, consulte [a quota standard: Aumentar os limites por região.](regional-quota-requests.md)

## <a name="request-a-quota-limit-increase-from-help--support"></a>Solicite um aumento do limite de quota da Ajuda + suporte

Para solicitar um aumento do limite de quota pontual para todas as séries de máquinas virtuais utilizando **o suporte Help +**:

> [!NOTE]
> Também pode solicitar um aumento do limite de quota para várias regiões através de um único caso de apoio. Para mais detalhes, consulte o passo 8.

1. A partir do menu [do portal Azure,](https://portal.azure.com) selecione **Help + support**.

   ![O link de suporte Help +](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. No **Suporte Help +**, selecione Novo pedido de **suporte**.

    ![Criar um novo pedido de apoio](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Para **o tipo de emissão**, selecione **limites de serviço e de subscrição (quotas)**.

   ![Selecione um tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Para **Subscrição**, selecione a subscrição cuja quota pretende aumentar.

   ![Selecione uma subscrição para uma quota aumentada](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para **o tipo de quota**, selecione **Compute-VM (cores-vCPUs) aumentos de limites** de subscrição .

   ![Selecione um tipo de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **Seguinte: Soluções** para abrir **detalhes do problema**. **Selecione Fornecer detalhes** para introduzir informações adicionais.

   ![O link "Fornecer detalhes"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Nos **pormenores da quota,** faça os seguintes passos:

   1. Para **o modelo de implantação**, selecione o modelo apropriado e para **localizações**, selecione uma localização.

      ![Fornecer detalhes adicionais de quota](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Para a localização selecionada, em **Tipos**, em **Selecionar um tipo,** escolha **Spot**.

      ![Selecione o tipo de ponto](./media/resource-manager-core-quotas-request/select-spot-type.png)

       Em **Tipos**, pode solicitar os tipos de quota padrão e spot de um único caso de suporte através de suporte multi-selecção.

       Para obter mais informações, consulte [quota-série: Aumente os limites por séries VM](per-vm-quota-requests.md).

   1. Introduza o novo limite de quota que pretende para esta subscrição.

      ![Selecione uma nova quota para spot VM](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Localizações** e, em seguida, selecione um tipo VM apropriado. Em seguida, pode introduzir um limite que se aplica à localização adicional.

   ![Especificar localizações adicionais em detalhes de quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. **Selecione Save e continue** a criar o pedido de suporte.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Solicitar um aumento do limite de quota do painel de assinaturas

Para solicitar um aumento do limite de quota pontual para todas as séries VM do painel **de subscrições:**

> [!NOTE]
> Também pode solicitar um aumento do limite de quota para várias regiões através de um único caso de apoio. Para mais detalhes, consulte o passo 7.

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Subscrições.**

   ![Subscrições na pesquisa do portal Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a subscrição cuja quota pretende aumentar.

   ![Subscrições para selecionar para alterações](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. No painel esquerdo, selecione **Usage + quotas**.

   ![O link "Utilização + quotas"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No topo direito, selecione **Pedir aumento**.

   ![Selecione para aumentar a quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Para **o tipo de quota**, selecione **Compute-VM (cores-vCPUs) aumentos de limites** de subscrição .

   ![Selecione um tipo de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **Seguinte: Soluções** para abrir **detalhes do problema**. **Selecione Fornecer detalhes** para introduzir informações adicionais. Nos **pormenores da Quota**, insira as seguintes informações:

   1. Para **o modelo de implantação**, selecione o modelo apropriado e para **localizações**, selecione uma localização.

      ![Fornecer detalhes de quota](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Para a localização selecionada, em **Tipos**, em **Selecionar um tipo,** escolha **Spot**.

      ![Selecione o tipo de ponto](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Para obter mais informações, consulte [quota-série: Aumente os limites por séries VM](per-vm-quota-requests.md).

   1. Introduza o novo limite de quota que pretende para esta subscrição.

      ![Introduza um novo valor para o limite vCPU](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Localizações** e, em seguida, selecione um tipo VM apropriado. Em seguida, pode introduzir um limite que se aplica à localização adicional.

   ![Selecione localizações adicionais em detalhes de quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. **Selecione Save e continue** a criar o pedido de suporte.
