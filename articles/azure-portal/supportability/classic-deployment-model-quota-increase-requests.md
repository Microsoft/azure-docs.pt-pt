---
title: Modelo de implementação clássico Azure
description: O modelo de implementação Classic, agora substituído pelo modelo Resource Manager, impõe um limite global de quota vCPU para VMs e conjuntos de escala de máquinas virtuais.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b5b5e8c11bfe164aaa3539742dac8c4d267c69e1
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745609"
---
# <a name="classic-deployment-model"></a>Modelo de implementação clássica

O modelo de implementação Classic é o modelo de implementação Azure de geração mais antiga. Impõe um limite global de quota vCPU para máquinas virtuais e conjuntos de escala de máquinas virtuais. O modelo de implementação Classic já não é recomendado, e agora é substituído pelo modelo Resource Manager.

Para saber mais sobre estes dois modelos de implementação e as vantagens de utilizar o Gestor de Recursos, consulte [o Gestor de Recursos e a implementação clássica.](../../azure-resource-manager/management/deployment-models.md)

Quando uma nova subscrição é criada, uma quota padrão de vCPUs é atribuída a ela. Sempre que uma nova máquina virtual seja implantada utilizando o modelo de implantação Clássico, a soma da utilização nova e existente do VCPU em todas as regiões não deve exceder a quota vCPU aprovada para o modelo de implantação Clássico.

Para saber mais sobre quotas, consulte [os limites de subscrição e serviços da Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

Pode solicitar um aumento do limite de quota vCPU para o modelo de implantação Clássico. Utilize ou **ajuda + suporte** ou use + **quotas** no portal Azure.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Pedido por VM série vCPU aumento de quota a nível de subscrição usando ajuda + suporte

Siga as instruções abaixo para criar um pedido de apoio utilizando o **suporte Help +** no portal Azure.

1. A partir do menu [do portal Azure,](https://portal.azure.com) selecione **Help + support**.

   ![Selecione Ajuda + suporte no portal Azure](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selecione **Novo pedido de suporte**.

   ![Criar um novo pedido de apoio no portal Azure](./media/resource-manager-core-quotas-request/new-support-request.png)

1. No **tipo de emissão,** escolha **limites de serviço e de subscrição (quotas)**.

   ![Selecione as quotas como o tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selecione a subscrição cuja quota pretende aumentar.

   ![Selecione subscrição para a qual aumentar uma quota](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para **o tipo de quota**, selecione **Compute -VM (cores-vCPUs) aumentos de limites** de subscrição .

   ![Selecione o tipo de quota para aumentar](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **Seguinte: Soluções** para abrir **detalhes do problema**. **Selecione Fornecer detalhes** para fornecer informações adicionais.

   ![Forneça detalhes para ajudar o seu pedido](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Em **detalhes de Quota,** selecione **Classic** e selecione uma **localização.**

   ![Adicionar detalhes, incluindo modelo de implementação e localização](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Para **a família SKU,** selecione uma ou mais famílias SKU para aumentar.

   ![Especifique a família SKU para aumentar](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Introduza os novos limites que gostaria na subscrição. Para remover uma linha, desescolh o SKU da **família SKU** ou selecione o ícone "X" de devoluções. Depois de introduzir uma quota para cada família SKU, **selecione Save and Continue** in Quota **details** to continue com o pedido de apoio.

   ![Solicitar novos limites](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Pedido por VM série vCPU aumento de quota a nível de subscrição usando usage + quotas

Siga as instruções abaixo para criar um pedido de apoio utilizando **quotas De Utilização +** no portal Azure.

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Subscrições.**

   ![Ir a Subscrições no portal Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a subscrição cuja quota pretende aumentar.

   ![Selecione subscrição para modificar](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selecione **Utilização + quotas**.

   ![Selecione o uso e as quotas para uma subscrição](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **Pedir aumento**.

   ![Selecione para aumentar a quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selecione **Compute-VM (cores-vCPUs) o limite de subscrição aumenta** à medida que o **tipo de quota**.

   ![Selecione tipo de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **Seguinte: Soluções** para abrir **detalhes do problema**. **Selecione Fornecer detalhes** para fornecer informações adicionais.

   ![Forneça detalhes para o seu pedido](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Em **detalhes de Quota,** selecione **Classic** e a **Location**.

   ![Selecione detalhes da quota, incluindo modelo de implementação e localização](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Selecione uma ou mais famílias SKU para um aumento.

   ![Selecione família SKU para aumentar](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Introduza os novos limites que gostaria na subscrição. Para remover uma linha, desescolh o SKU da **família SKU** ou selecione o ícone "X" de devoluções. Depois de introduzir uma quota para cada família SKU, **selecione Save and Continue** in Quota **details** to continue com o pedido de apoio.

   ![Insira nova quota](./media/resource-manager-core-quotas-request/new-limits-classic.png)

