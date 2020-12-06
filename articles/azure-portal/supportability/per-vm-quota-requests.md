---
title: Solicitar um aumento dos limites de quota vCPU por série Azure VM
description: Como solicitar um aumento do limite de quota vCPU para uma série VM no portal Azure, que aumenta o limite total regional de vCPU pelo mesmo montante.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5db3e538a64e275313e1e0ab01f6cc6350eabb77
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745439"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Quota padrão: aumentar os limites por séries de VM

O Azure Resource Manager suporta dois tipos de quotas vCPU para máquinas virtuais:

* *Os VMs pay-as-you-go* e *as instâncias VM reservadas* estão sujeitos a uma *quota padrão vCPU*.
* *Os VM spot* estão sujeitos a uma *quota vCPU à vista.*

A quota padrão vCPU para instâncias de máquinas virtuais pagas e reservadas é aplicada em dois níveis para cada subscrição em cada região:

* O primeiro nível é o *limite total de vCPUs regionais,* em todas as séries VM.
* O segundo nível é o *limite de vCPUs da série por VM,* como os vCPUs da série Dv3.

Sempre que implementar um novo spot VM, a utilização total de vCPU novo e existente para todos os casos de VM spot não deve exceder o limite de quota vCPU aprovado. Se a quota à vista for excedida, a colocação de VM no local não é permitida.

Pode solicitar um aumento do limite de quota vCPU para a série VM utilizando o portal Azure. Um aumento do contingente da série VM aumenta automaticamente o limite total regional de vCPU pelo mesmo montante.

Para saber mais sobre as quotas padrão vCPU, consulte [as quotas vCPU da máquina virtual](../../virtual-machines/windows/quotas.md) e os [limites de subscrição e serviço da Azure.](./classic-deployment-model-quota-increase-requests.md)

Para conhecer o aumento do limite vCPU por região para quota-padrão, consulte [a quota-padrão: Aumentar os limites por região](regional-quota-requests.md).

Para saber mais sobre o aumento dos limites de VM vCPU, consulte [quota spot: Aumente os limites para todas as séries VM](low-priority-quota.md).

Pode solicitar um aumento dos limites padrão de quota vCPU por série VM de duas maneiras, conforme descrito nas secções seguintes.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Solicite um aumento padrão de quota da Ajuda + suporte

Para solicitar um aumento padrão de quota vCPU por série VM da **Ajuda + suporte**:

> [!NOTE]
> Também pode solicitar um aumento do limite de quota para várias regiões através de um único caso de apoio. Para mais detalhes, consulte o passo 8.

1. No menu [do portal Azure,](https://portal.azure.com) selecione **Help + support**.

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

   ![Detalhes adicionais da quota da TProvide](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Para **o modelo de implantação,** selecione o modelo apropriado.

   1. Para **localizações,** selecione uma localização. Para a localização selecionada, em **Tipos**, em **Selecionar um tipo,** escolha **Standard**.

      ![Detalhes das quotas - tipos de quotas](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Em **Tipos**, pode solicitar os tipos de quota padrão e spot de um único caso de suporte através de suporte multi-selecção.

      Para obter mais informações sobre o aumento dos limites de quota pontual, consulte [VMs spot Azure para conjuntos de escala de máquina virtual](../../virtual-machine-scale-sets/use-spot.md).

   1. Na **Standard,** selecione a série SKU para aumentar as quotas.

      ![Detalhes da quota - série SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Insira os novos limites de quota que deseja para esta subscrição. Para remover um SKU da sua lista, limpe a caixa de verificação ao lado do SKU ou selecione o ícone "X" de devoluções.

      ![Selecione um novo vCPU Limit](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Localizações** e, em seguida, selecione um tipo VM apropriado. Em seguida, pode introduzir um limite que se aplica à localização adicional.

   ![Especificar localizações adicionais em detalhes de quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. **Selecione Save e continue** a criar o pedido de suporte.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Solicitar um aumento padrão de quota das Assinaturas

Para solicitar um aumento padrão de quota vCPU por série VM a partir de **assinaturas**:

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

1. Nos **pormenores da quota,** faça os seguintes passos:

   1. Para **o modelo de implantação**, selecione o modelo apropriado e para **localizações**, selecione uma localização.

      ![Fornecer detalhes de quota](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Para a localização selecionada, em **Tipos**, **selecione um tipo** e, em seguida, selecione **Standard**.

      ![Selecione tipo padrão](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Em **Tipos**, pode solicitar os tipos de quota padrão e spot de um único caso de suporte através de suporte multi-selecção.

      Para obter mais informações sobre o aumento dos limites de quota pontual, consulte [VMs spot Azure para conjuntos de escala de máquina virtual](../../virtual-machine-scale-sets/use-spot.md).

   1. Para **Standard,** selecione a série SKU cujas quotas pretende aumentar.

      ![Detalhes da quota - série SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Insira os novos limites de quota que deseja para esta subscrição. Para remover um SKU da sua lista, desescolh a caixa de verificação ao lado do SKU ou selecione o ícone "X" de devoluções.

      ![Selecione um novo limite vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Localizações** e, em seguida, selecione um tipo VM apropriado.

   Este passo pré-load a série SKU que selecionou para locais anteriores. Introduza os limites de quota que pretende aplicar às séries adicionais.

   ![Selecione localizações adicionais em detalhes de quota](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. **Selecione Save e continue** a criar o pedido de suporte.