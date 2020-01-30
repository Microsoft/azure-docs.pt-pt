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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843754"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Cota padrão: aumentar os limites por série de VMs

O Azure Resource Manager dá suporte a dois tipos de cotas do vCPU para máquinas virtuais:

* *As VMs pagas conforme o uso* e as *instâncias de VM reservadas* estão sujeitas a uma *cota de vCPU padrão*.
* As *VMs pontuais* estão sujeitas a uma *cota de vCPU Spot*.

A quota-padrão vCPU para casos de máquinas virtuais pagas e reservadas é aplicada a dois níveis para cada subscrição em cada região:

* O primeiro nível é o limite total regional de *VCPUs,* em todas as séries VM.
* O segundo nível é o *limite vCPUs da série por VM,* como o VCPUs da série Dv3.

Sempre que você implantar uma nova VM Spot, o uso total de vCPU novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Adicionalmente, o número total de vCPUs novos e existentes que são implantados em todas as séries VM não deve exceder a quota vCPU regional total aprovada para a subscrição. Se uma dessas cotas for excedida, a implantação da VM não será permitida.

Você pode solicitar um aumento no limite de cota de vCPU para a série de VMs usando o portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPU regional total pelo mesmo valor.

Para saber mais sobre as cotas de vCPU padrão, consulte [cotas de vCPU de máquina virtual](../../virtual-machines/windows/quotas.md) e [limites de serviço e assinatura do Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests).

Para saber mais sobre como aumentar o limite de vCPU por região para a cota padrão, consulte [cota padrão: aumentar os limites por região](regional-quota-requests.md).

Para saber mais sobre como aumentar os limites de vCPU de VM Spot, confira [cota de spot: aumentar os limites para todas as séries de VM](low-priority-quota.md).

Pode solicitar um aumento dos limites de quota vCPU padrão por série VM de duas formas, conforme descrito nas seguintes secções.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Solicite um aumento padrão de quota da Ajuda + suporte

Para solicitar um aumento de quota vCPU padrão por série VM da **Ajuda + suporte**:

> [!NOTE]
> Você também pode solicitar um aumento de limite de cota para várias regiões por meio de um único caso de suporte. Para obter detalhes, consulte a etapa 8.

1. No menu [do portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![O link de ajuda + suporte](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Em **Ajuda + suporte,** selecione **Novo pedido de suporte**.

    ![Criar um novo pedido de apoio](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Para **tipo de problema**, selecione **limites de serviço e de assinatura (cotas)** .

   ![Selecione um tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Para **Subscrição,** selecione a subscrição cuja quota pretende aumentar.

   ![Selecione uma subscrição para uma quota aumentada](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para o tipo de **quota,** selecione O limite de **subscrição Compute-VM (cores-vCPUs) aumenta**.

   ![Selecione um tipo de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **Seguinte: Soluções** para abrir **DETALHES DE PROBLEMAS**. Selecione **Fornecer detalhes** para introduzir informações adicionais.

   ![O link "fornecer detalhes"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Nos detalhes da **Quota,** faça os seguintes passos:

   ![TFornecer detalhes adicionais de quotas](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Para **o modelo de implantação,** selecione o modelo apropriado.

   1. Para **locais,** selecione um local. Para a localização selecionada, em **Tipos,** em **Selecionar um tipo,** escolha **Standard**.

      ![Detalhes das quotas - tipos de quotas](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Em **tipos**, você pode solicitar tipos de cota padrão e spot de um único caso de suporte por meio do suporte a várias seleções.

      Para obter mais informações sobre como aumentar os limites de cota Spot, consulte [VMs de ponto do Azure para conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/use-spot.md).

   1. Em **Standard**, selecione a série SKU para aumentar as quotas.

      ![Detalhes da quota - Série SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Insira os novos limites de cota que você deseja para esta assinatura. Para remover um SKU da sua lista, limpe a caixa de verificação ao lado do SKU ou selecione o ícone "X" de devoluções.

      ![Selecione um novo limite vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Locais**, e, em seguida, selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Especificar localizações adicionais em detalhes de quotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Solicitar um aumento de quota padrão a partir de Assinaturas

Para solicitar um aumento de quota vCPU padrão por série VM a partir de **Assinaturas:**

> [!NOTE]
> Você também pode solicitar um aumento de limite de cota para várias regiões por meio de um único caso de suporte. Para obter detalhes, consulte a etapa 7.

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Assinaturas.**

   ![Assinaturas na pesquisa do portal Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Assinaturas para selecionar para alterações](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. No painel esquerdo, **selecione Usage + quotas**.

   ![O link "uso + cotas"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **solicitar aumento**.

   ![Selecione para aumentar a quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Para o tipo de **quota,** selecione O limite de **subscrição Compute-VM (cores-vCPUs) aumenta**.

   ![Selecione um tipo de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Nos detalhes da **Quota,** faça os seguintes passos:

   1. Para **o modelo de implantação,** selecione o modelo apropriado e, para **localizações,** selecione uma localização.

      ![Fornecer detalhes das quotas](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Para a localização selecionada, em **Tipos,** **selecione um tipo**, e, em seguida, selecione **Standard**.

      ![Selecione tipo Standard](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Em **tipos**, você pode solicitar tipos de cota padrão e spot de um único caso de suporte por meio do suporte a várias seleções.

      Para obter mais informações sobre como aumentar os limites de cota Spot, consulte [VMs de ponto do Azure para conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/use-spot.md).

   1. Para **Standard**, selecione a série SKU cujas quotas pretende aumentar.

      ![Detalhes da quota - Série SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Insira os novos limites de cota que você deseja para esta assinatura. Para remover um SKU da sua lista, desmarque a caixa de verificação ao lado do SKU ou selecione o ícone "X" de devoluções.

      ![Selecione um novo limite vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Locais**, e, em seguida, selecione um tipo de VM apropriado.

   Esta etapa sobrecarrega a série de SKU que você selecionou para os locais anteriores. Insira os limites de cota que você deseja aplicar à série adicional.

   ![Selecione localizações adicionais em detalhes de quotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.
