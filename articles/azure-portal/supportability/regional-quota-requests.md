---
title: Solicitar um aumento dos limites de quota do VCPU regional da Azure
description: Como solicitar um aumento do limite de quota vCPU para uma região no portal Azure.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745422"
---
# <a name="standard-quota-increase-limits-by-region"></a>Quota-padrão: Aumentar os limites por região

O Azure Resource Manager suporta dois tipos de quotas vCPU para máquinas virtuais:

* *Os VMs pay-as-you-go* e *as instâncias VM reservadas* estão sujeitos a uma *quota padrão vCPU*.
* *Os VM spot* estão sujeitos a uma *quota vCPU à vista.*

A quota padrão vCPU para instâncias de máquinas virtuais pagas e reservadas é aplicada em dois níveis para cada subscrição em cada região:

* O primeiro nível é o *limite total de vCPUs regionais,* em todas as séries VM.
* O segundo nível é o *limite de vCPUs da série por VM,* como os vCPUs da série D.

Sempre que implementar um novo spot VM, a utilização total de vCPU nova e existente para esta série VM não deve exceder a quota vCPU aprovada para essa série VM em particular. Além disso, o número total de vCPUs novos e existentes que são implantados em todas as séries VM não deve exceder a quota total de VCPU regional aprovada para a subscrição. Se uma destas quotas for excedida, a implantação em VM não é permitida.

Pode solicitar um aumento do limite de quota vCPU para a série VM utilizando o portal Azure. Um aumento do contingente da série VM aumenta automaticamente o limite total regional de vCPU pelo mesmo montante.

Quando se cria uma nova subscrição, o número total padrão de vCPUs regionais pode não ser igual ao contingente vCPU total padrão para todas as séries VM individuais. Esta discrepância pode resultar numa subscrição com quota suficiente para cada série VM individual que pretende implementar. Mas pode não haver quotas suficientes para acomodar os vCPUs regionais totais para todas as implantações. Neste caso, deve apresentar um pedido para aumentar explicitamente o limite do número total de vCPUs regionais. O limite total de vCPU regional não pode exceder a quota total aprovada em todas as séries VM para a região.

Para saber mais sobre as quotas padrão vCPU, consulte [as quotas vCPU da máquina virtual](../../virtual-machines/windows/quotas.md) e os [limites de subscrição e serviço da Azure, quotas e restrições.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

Para saber mais sobre o aumento dos limites de VM vCPU, consulte [quota spot: Aumente os limites para todas as séries VM](low-priority-quota.md).

Pode solicitar um aumento do seu limite de quota padrão vCPU por região de duas maneiras.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Solicito um aumento de quota por região a partir do apoio Help +

Para solicitar um aumento da quota vCPU por região a partir da **Ajuda + apoio**:

1. A partir do menu [do portal Azure,](https://portal.azure.com) selecione **Help + support**.

   ![O link "Ajuda + suporte"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. No **Suporte Help +**, selecione Novo pedido de **suporte**.

    ![Novo pedido de suporte](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Para **o tipo de emissão**, selecione **limites de serviço e de subscrição (quotas)**.

   ![Selecione um tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Para **Subscrição**, selecione a subscrição cuja quota pretende aumentar.

   ![Selecionar uma subscrição](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para o **tipo quota**, selecione **Outros Pedidos**.

   ![Selecione um tipo de quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecione **Seguinte: Soluções** para abrir **detalhes do problema**. Na **Descrição**, fornecer as seguintes informações:

    1. Para **o modelo de implantação**, especifique o Gestor de **Recursos**.  
    1. Para **a Região**, especifique a sua região requerida, por exemplo, **Leste DOS EUA 2**.  
    1. Para **o Novo Limite**, especifique um novo limite vCPU para a região. Este valor não deve exceder a soma das quotas aprovadas para séries SKU individuais para esta subscrição.

    ![Introduza detalhes para o pedido de quota](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **Review + criar** para continuar a criar o pedido de suporte.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Solicitar um aumento de quota por região a partir de Assinaturas

Para solicitar um aumento do contingente vCPU por região a partir de **Assinaturas:**

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Subscrições.**

   ![Ir a Subscrições no portal Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a subscrição cuja quota pretende aumentar.

   ![Selecione uma subscrição para modificar](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. No painel esquerdo, selecione **Usage + quotas**.

   ![Siga o link de utilização e quotas](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No topo direito, selecione **Pedir aumento**.

   ![Selecione para aumentar a quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. A partir **do tipo Quota,** selecione **Outros Pedidos.**

   ![Selecione o tipo de quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecione **Seguinte: Soluções** para abrir **detalhes do problema**. Na caixa **Descrição,** forneça as seguintes informações adicionais:

    1. Para **o modelo de implantação**, especifique o Gestor de **Recursos**.  
    1. Para **a Região**, especifique a sua região requerida, por exemplo, **Leste DOS EUA 2**.  
    1. Para **o Novo Limite**, especifique um novo limite vCPU para a região. Este valor não deve exceder a soma das quotas aprovadas para séries SKU individuais para esta subscrição.

    ![Insira informações em detalhes](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **Review + criar** para continuar a criar o pedido de suporte.
