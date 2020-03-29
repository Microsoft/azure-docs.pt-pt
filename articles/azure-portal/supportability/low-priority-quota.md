---
title: Quota de máquina virtual spot - Azure
description: Aumente os limites de quota para os VMs spot, que fornecem um modelo de utilização do Azure que permite assumir custos mais baixos em troca de deixar o Azure remover VMs conforme necessário.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842805"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Quota spot: Aumentar limites para todas as séries VM

As máquinas virtuais spot (VMs) fornecem um modelo diferente de utilização do Azure. Permitem-lhe assumir custos mais baixos em troca de deixar o Azure remover as máquinas virtuais conforme necessário para implementações de instâncias VM pagas ou reservadas. Para obter mais informações sobre vMs spot, consulte [VMs spot Azure para conjuntos de escala](../../virtual-machine-scale-sets/use-spot.md)de máquinas virtuais .

O Gestor de Recursos Azure suporta dois tipos de quotas vCPU para máquinas virtuais:

* *Os VMs pay-as-you-go* e os *casos de VM reservados* estão sujeitos a uma *quota vCPU padrão*.
* *Os VMs spot* estão sujeitos a uma *quota spot vCPU*.

Para o tipo de quota spot vCPU, as quotas vCPU do Gestor de Recursos são aplicadas em todas as séries de máquinas virtuais disponíveis como um único limite regional.

Sempre que implementar um novo VM spot, o uso total de VCPU novo e existente para todos os casos de VM spot não deve exceder o limite de quota vCPU spot aprovado. Se a quota de spot for excedida, a implantação de VM no local não é permitida.

Este artigo discute como solicitar um aumento do limite de quota spot vCPU através do portal Azure.

Para saber mais sobre as quotas padrão vCPU, consulte [as quotas vCPU](../../virtual-machines/windows/quotas.md) da máquina virtual e os limites de [subscrição e serviço do Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

Para aprender sobre o aumento do limite de vCPU por região, consulte [a quota Standard: Aumentar os limites por região](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Solicitar um aumento do limite de quota da Ajuda + suporte

Para solicitar um aumento de limite de quota spot para todas as séries de máquinas virtuais utilizando **ajuda + suporte:**

> [!NOTE]
> Pode também solicitar um aumento do limite de quota para várias regiões através de um único caso de apoio. Para mais detalhes, consulte o passo 8.

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

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

1. Em **detalhes da Quota,** faça os seguintes passos:

   1. Para **o modelo de implantação,** selecione o modelo apropriado e, para **localizações,** selecione uma localização.

      ![Fornecer detalhes adicionais sobre as quotas](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Para a localização selecionada, em **Tipos,** em **Selecionar um tipo,** escolha **Spot**.

      ![Selecione o tipo de ponto](./media/resource-manager-core-quotas-request/select-spot-type.png)

       Em **Tipos,** pode solicitar tipos de quotas padrão e spot a partir de um único caso de suporte através de suporte multi-selecção.

       Para mais informações, consulte [a quota Standard: Aumentar os limites por sérieVM](per-vm-quota-requests.md).

   1. Introduza o novo limite de quotas que pretende para esta subscrição.

      ![Selecione uma nova quota para spot VM](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Locais**, e, em seguida, selecione um tipo de VM apropriado. Pode então introduzir um limite que se aplica à localização adicional.

   ![Especificar localizações adicionais em detalhes de quotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **Guardar e continue** a criar o pedido de apoio.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Solicitar um aumento do limite de quota do painel de assinaturas

Para solicitar um aumento do limite de quota spot para todas as séries VM do painel **de Assinaturas:**

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

1. Selecione **Seguinte: Soluções** para abrir **DETALHES DE PROBLEMAS**. Selecione **Fornecer detalhes** para introduzir informações adicionais. Em **detalhes da Quota,** introduza as seguintes informações:

   1. Para **o modelo de implantação,** selecione o modelo apropriado e, para **localizações,** selecione uma localização.

      ![Fornecer detalhes das quotas](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Para a localização selecionada, em **Tipos,** em **Selecionar um tipo,** escolha **Spot**.

      ![Selecione tipo Spot](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Para mais informações, consulte [a quota Standard: Aumentar os limites por sérieVM](per-vm-quota-requests.md).

   1. Introduza o novo limite de quotas que pretende para esta subscrição.

      ![Introduza um novo valor para o limite vCPU](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Locais**, e, em seguida, selecione um tipo de VM apropriado. Pode então introduzir um limite que se aplica à localização adicional.

   ![Selecione localizações adicionais em detalhes de quotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **Guardar e continue** a criar o pedido de apoio.
