---
title: Pedido de aumento dos limites de quota regional do Azure
description: Como solicitar um aumento do limite de quota vCPU para uma região do portal Azure.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843689"
---
# <a name="standard-quota-increase-limits-by-region"></a>Cota padrão: aumentar os limites por região

O Azure Resource Manager dá suporte a dois tipos de cotas do vCPU para máquinas virtuais:

* *As VMs pagas conforme o uso* e as *instâncias de VM reservadas* estão sujeitas a uma *cota de vCPU padrão*.
* As *VMs pontuais* estão sujeitas a uma *cota de vCPU Spot*.

A quota-padrão vCPU para casos de máquinas virtuais pagas e reservadas é aplicada a dois níveis para cada subscrição em cada região:

* O primeiro nível é o limite total regional de *VCPUs,* em todas as séries VM.
* O segundo nível é o *limite vCPUs da série por VM,* como o vCPUs da série D.

Sempre que você implantar uma nova VM Spot, o uso total de vCPU novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Adicionalmente, o número total de vCPUs novos e existentes que são implantados em todas as séries VM não deve exceder a quota vCPU regional total aprovada para a subscrição. Se uma dessas cotas for excedida, a implantação da VM não será permitida.

Você pode solicitar um aumento no limite de cota de vCPU para a série de VMs usando o portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPU regional total pelo mesmo valor.

Quando você cria uma nova assinatura, o número total padrão de vCPUs regionais pode não ser igual à cota de vCPU padrão total para todas as séries de VMs individuais. Essa discrepância pode resultar em uma assinatura com cota suficiente para cada série de VM individual que você deseja implantar. Mas pode não haver cota suficiente para acomodar o total regional de vCPUs para todas as implantações. Nesse caso, você deve enviar uma solicitação para aumentar explicitamente o limite do número total de vCPUs regionais. O limite de vCPU regional total não pode exceder a cota total aprovada em todas as séries de VM para a região.

Para saber mais sobre as quotas padrão vCPU, consulte [as quotas vCPU](../../virtual-machines/windows/quotas.md) da máquina virtual e os limites de [subscrição e serviço do Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

Para saber mais sobre como aumentar os limites de vCPU de VM Spot, confira [cota de spot: aumentar os limites para todas as séries de VM](low-priority-quota.md).

Pode solicitar um aumento do seu limite de quota padrão vCPU por região de duas formas.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Solicitar um aumento de quota por região da Ajuda + apoio

Para solicitar um aumento de quota vCPU por região da **Ajuda + suporte:**

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![O link "ajuda + suporte"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Em **Ajuda + suporte,** selecione **Novo pedido de suporte**.

    ![Novo pedido de suporte](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Para **tipo de problema**, selecione **limites de serviço e de assinatura (cotas)** .

   ![Selecione um tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Para **Subscrição,** selecione a subscrição cuja quota pretende aumentar.

   ![Selecionar uma subscrição](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para o **tipo quota,** selecione **Outros Pedidos**.

   ![Selecione um tipo de quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecione **Seguinte: Soluções** para abrir **DETALHES DE PROBLEMAS**. Em **Descrição,** forneça as seguintes informações:

    1. Para o **modelo de implantação**, especifique **Resource Manager**.  
    1. Para **a Região,** especifique a sua região necessária, por exemplo, **Leste US 2**.  
    1. Para o **novo limite**, especifique um novo limite de vCPU para a região. Esse valor não deve exceder a soma das cotas aprovadas para a série SKU individual desta assinatura.

    ![Insira detalhes para o pedido de quota](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **Review + crie** para continuar a criar o pedido de suporte.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Solicitar um aumento de quota por região a partir de Assinaturas

Para solicitar um aumento de quota vCPU por região a partir de **assinaturas:**

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Assinaturas.**

   ![Ir a Assinaturas no portal Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione uma subscrição para modificar](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. No painel esquerdo, **selecione Usage + quotas**.

   ![Siga o link de utilização e quotas](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **solicitar aumento**.

   ![Selecione para aumentar a quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Do **tipo quota,** selecione **Outros Pedidos**.

   ![Selecione o tipo de quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecione **Seguinte: Soluções** para abrir **DETALHES DE PROBLEMAS**. Na caixa **Descrição,** forneça as seguintes informações adicionais:

    1. Para o **modelo de implantação**, especifique **Resource Manager**.  
    1. Para **a Região,** especifique a sua região necessária, por exemplo, **Leste US 2**.  
    1. Para o **novo limite**, especifique um novo limite de vCPU para a região. Esse valor não deve exceder a soma das cotas aprovadas para a série SKU individual desta assinatura.

    ![Insira informações em detalhes](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **Review + crie** para continuar a criar o pedido de suporte.
