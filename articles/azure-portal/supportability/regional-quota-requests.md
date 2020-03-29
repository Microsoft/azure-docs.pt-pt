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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843689"
---
# <a name="standard-quota-increase-limits-by-region"></a>Quota-padrão: Aumentar os limites por região

O Gestor de Recursos Azure suporta dois tipos de quotas vCPU para máquinas virtuais:

* *Os VMs pay-as-you-go* e os *casos de VM reservados* estão sujeitos a uma *quota vCPU padrão*.
* *Os VMs spot* estão sujeitos a uma *quota spot vCPU*.

A quota-padrão vCPU para casos de máquinas virtuais pagas e reservadas é aplicada a dois níveis para cada subscrição em cada região:

* O primeiro nível é o limite total regional de *VCPUs,* em todas as séries VM.
* O segundo nível é o *limite vCPUs da série por VM,* como o vCPUs da série D.

Sempre que implementar um novo Spot VM, o uso vCPU totalmente novo e existente para essa série VM não deve exceder a quota vCPU aprovada para essa série VM em particular. Adicionalmente, o número total de vCPUs novos e existentes que são implantados em todas as séries VM não deve exceder a quota vCPU regional total aprovada para a subscrição. Se uma destas quotas for excedida, a implantação de VM não é permitida.

Pode solicitar um aumento do limite de quota vCPU para a série VM utilizando o portal Azure. O aumento da quota da série VM aumenta automaticamente o limite total regional de VCPU pelo mesmo montante.

Quando cria uma nova subscrição, o número total de vCPUs regionais pode não ser igual à quota vCPU padrão total para todas as séries VM individuais. Esta discrepância pode resultar numa subscrição com quota suficiente para cada série VM individual que pretende implementar. Mas pode não haver quota suficiente para acomodar o total de vCPUs regionais para todas as implantações. Neste caso, deve apresentar um pedido para aumentar explicitamente o limite do número total de vCPUs regionais. O limite total regional de vCPU não pode exceder a quota total aprovada em todas as séries VM para a região.

Para saber mais sobre as quotas padrão vCPU, consulte [as quotas vCPU](../../virtual-machines/windows/quotas.md) da máquina virtual e os limites de [subscrição e serviço do Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

Para saber mais sobre o aumento dos limites vCPU spot, consulte [a quota spot: Aumentar os limites para todas as séries VM](low-priority-quota.md).

Pode solicitar um aumento do seu limite de quota padrão vCPU por região de duas formas.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Solicitar um aumento de quota por região da Ajuda + apoio

Para solicitar um aumento de quota vCPU por região da **Ajuda + suporte:**

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![O link "Ajuda + suporte"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Em **Ajuda + suporte,** selecione **Novo pedido de suporte**.

    ![Novo pedido de suporte](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Para **o tipo de emissão,** selecione limites de serviço e **subscrição (quotas)**.

   ![Selecione um tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Para **Subscrição,** selecione a subscrição cuja quota pretende aumentar.

   ![Selecionar uma subscrição](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para o **tipo quota,** selecione **Outros Pedidos**.

   ![Selecione um tipo de quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecione **Seguinte: Soluções** para abrir **DETALHES DE PROBLEMAS**. Em **Descrição,** forneça as seguintes informações:

    1. Para **o Modelo de Implantação,** especifique o Gestor de **Recursos**.  
    1. Para **a Região,** especifique a sua região necessária, por exemplo, **Leste US 2**.  
    1. Para **o Novo Limite,** especifique um novo limite de vCPU para a região. Este valor não deve exceder a soma das quotas aprovadas para séries SKU individuais para esta subscrição.

    ![Insira detalhes para o pedido de quota](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **Review + crie** para continuar a criar o pedido de suporte.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Solicitar um aumento de quota por região a partir de Assinaturas

Para solicitar um aumento de quota vCPU por região a partir de **assinaturas:**

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Assinaturas.**

   ![Ir a Assinaturas no portal Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a subscrição cuja quota pretende aumentar.

   ![Selecione uma subscrição para modificar](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. No painel esquerdo, **selecione Usage + quotas**.

   ![Siga o link de utilização e quotas](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Na parte superior direita, selecione **Pedido aumentar**.

   ![Selecione para aumentar a quota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Do **tipo quota,** selecione **Outros Pedidos**.

   ![Selecione o tipo de quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecione **Seguinte: Soluções** para abrir **DETALHES DE PROBLEMAS**. Na caixa **Descrição,** forneça as seguintes informações adicionais:

    1. Para **o Modelo de Implantação,** especifique o Gestor de **Recursos**.  
    1. Para **a Região,** especifique a sua região necessária, por exemplo, **Leste US 2**.  
    1. Para **o Novo Limite,** especifique um novo limite de vCPU para a região. Este valor não deve exceder a soma das quotas aprovadas para séries SKU individuais para esta subscrição.

    ![Insira informações em detalhes](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **Review + crie** para continuar a criar o pedido de suporte.
