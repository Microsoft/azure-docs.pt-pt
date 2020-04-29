---
title: Solicitar um aumento dos limites de quota vCPU por série Azure VM
description: Como solicitar um aumento do limite de quota vCPU para uma série VM no portal Azure, o que aumenta o limite total regional de VCPU pelo mesmo montante.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76843754"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Quota padrão: Aumentar os limites por série VM

O Gestor de Recursos Azure suporta dois tipos de quotas vCPU para máquinas virtuais:

* *Os VMs pay-as-you-go* e os *casos de VM reservados* estão sujeitos a uma *quota vCPU padrão*.
* *Os VMs spot* estão sujeitos a uma *quota spot vCPU*.

A quota-padrão vCPU para casos de máquinas virtuais pagas e reservadas é aplicada a dois níveis para cada subscrição em cada região:

* O primeiro nível é o limite total regional de *VCPUs,* em todas as séries VM.
* O segundo nível é o *limite vCPUs da série por VM,* como o VCPUs da série Dv3.

Sempre que implementar um novo Spot VM, o uso vCPU totalmente novo e existente para essa série VM não deve exceder a quota vCPU aprovada para essa série VM em particular. Adicionalmente, o número total de vCPUs novos e existentes que são implantados em todas as séries VM não deve exceder a quota vCPU regional total aprovada para a subscrição. Se uma destas quotas for excedida, a implantação de VM não é permitida.

Pode solicitar um aumento do limite de quota vCPU para a série VM utilizando o portal Azure. O aumento da quota da série VM aumenta automaticamente o limite total regional de VCPU pelo mesmo montante.

Para saber mais sobre as quotas padrão vCPU, consulte [as quotas vCPU](../../virtual-machines/windows/quotas.md) da máquina virtual e os limites de [subscrição e serviço do Azure.](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)

Para aprender sobre o aumento do limite de vCPU por região para a quota-padrão, ver [quota-padrão: Aumentar os limites por região](regional-quota-requests.md).

Para saber mais sobre o aumento dos limites vCPU spot, consulte [a quota spot: Aumentar os limites para todas as séries VM](low-priority-quota.md).

Pode solicitar um aumento dos limites de quota vCPU padrão por série VM de duas formas, conforme descrito nas seguintes secções.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Solicite um aumento padrão de quota da Ajuda + suporte

Para solicitar um aumento de quota vCPU padrão por série VM da **Ajuda + suporte**:

> [!NOTE]
> Pode também solicitar um aumento do limite de quota para várias regiões através de um único caso de apoio. Para mais detalhes, consulte o passo 8.

1. No menu [do portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![O link de ajuda + suporte](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Em **Ajuda + suporte,** selecione **Novo pedido de suporte**.

    ![Criar um novo pedido de apoio](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Para **o tipo de emissão,** selecione limites de serviço e **subscrição (quotas)**.

   ![Selecione um tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Para **Subscrição,** selecione a subscrição cuja quota pretende aumentar.

   ![Selecione uma subscrição para uma quota aumentada](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para o tipo de **quota,** selecione O limite de **subscrição Compute-VM (cores-vCPUs) aumenta**.

   ![Selecione um tipo de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **Seguinte: Soluções** para abrir **DETALHES DE PROBLEMAS**. Selecione **Fornecer detalhes** para introduzir informações adicionais.

   ![O link "Fornecer detalhes"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Nos detalhes da **Quota,** faça os seguintes passos:

   ![TFornecer detalhes adicionais de quotas](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Para **o modelo de implantação,** selecione o modelo apropriado.

   1. Para **locais,** selecione um local. Para a localização selecionada, em **Tipos,** em **Selecionar um tipo,** escolha **Standard**.

      ![Detalhes das quotas - tipos de quotas](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Em **Tipos,** pode solicitar tipos de quotas padrão e spot a partir de um único caso de suporte através de suporte multi-selecção.

      Para obter mais informações sobre o aumento dos limites de quota spot, consulte [os VMs spot Azure para conjuntos de escala](../../virtual-machine-scale-sets/use-spot.md)de máquinas virtuais .

   1. Em **Standard**, selecione a série SKU para aumentar as quotas.

      ![Detalhes da quota - Série SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Introduza os novos limites de quota que pretende para esta subscrição. Para remover um SKU da sua lista, limpe a caixa de verificação ao lado do SKU ou selecione o ícone "X" de devoluções.

      ![Selecione um novo limite vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Locais**, e, em seguida, selecione um tipo de VM apropriado. Pode então introduzir um limite que se aplica à localização adicional.

   ![Especificar localizações adicionais em detalhes de quotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **Guardar e continue** a criar o pedido de apoio.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Solicitar um aumento de quota padrão a partir de Assinaturas

Para solicitar um aumento de quota vCPU padrão por série VM a partir de **Assinaturas:**

> [!NOTE]
> Pode também solicitar um aumento do limite de quota para várias regiões através de um único caso de apoio. Para mais detalhes, consulte o passo 7.

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Assinaturas.**

   ![Assinaturas na pesquisa do portal Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a subscrição cuja quota pretende aumentar.

   ![Assinaturas para selecionar para alterações](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. No painel esquerdo, **selecione Usage + quotas**.

   ![O link "Utilização + quotas"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Na parte superior direita, selecione **Pedido aumentar**.

   ![Selecione para aumentar a quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Para o tipo de **quota,** selecione O limite de **subscrição Compute-VM (cores-vCPUs) aumenta**.

   ![Selecione um tipo de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Nos detalhes da **Quota,** faça os seguintes passos:

   1. Para **o modelo de implantação,** selecione o modelo apropriado e, para **localizações,** selecione uma localização.

      ![Fornecer detalhes das quotas](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Para a localização selecionada, em **Tipos,** **selecione um tipo**, e, em seguida, selecione **Standard**.

      ![Selecione tipo Standard](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Em **Tipos,** pode solicitar tipos de quotas padrão e spot a partir de um único caso de suporte através de suporte multi-selecção.

      Para obter mais informações sobre o aumento dos limites de quota spot, consulte [os VMs spot Azure para conjuntos de escala](../../virtual-machine-scale-sets/use-spot.md)de máquinas virtuais .

   1. Para **Standard**, selecione a série SKU cujas quotas pretende aumentar.

      ![Detalhes da quota - Série SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Introduza os novos limites de quota que pretende para esta subscrição. Para remover um SKU da sua lista, desmarque a caixa de verificação ao lado do SKU ou selecione o ícone "X" de devoluções.

      ![Selecione um novo limite vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Locais**, e, em seguida, selecione um tipo de VM apropriado.

   Este passo pré-carrega a série SKU que selecionou para locais anteriores. Introduza os limites de quota que pretende aplicar à série adicional.

   ![Selecione localizações adicionais em detalhes de quotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **Guardar e continue** a criar o pedido de apoio.
