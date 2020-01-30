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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842805"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Cota de spot: aumentar os limites para todas as séries de VM

As máquinas virtuais (VMs) spot fornecem um modelo diferente de uso do Azure. Permitem-lhe assumir custos mais baixos em troca de deixar o Azure remover as máquinas virtuais conforme necessário para implementações de instâncias VM pagas ou reservadas. Para obter mais informações sobre VMs pontuais, consulte [VMs de ponto do Azure para conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/use-spot.md).

O Azure Resource Manager dá suporte a dois tipos de cotas do vCPU para máquinas virtuais:

* *As VMs pagas conforme o uso* e as *instâncias de VM reservadas* estão sujeitas a uma *cota de vCPU padrão*.
* As *VMs pontuais* estão sujeitas a uma *cota de vCPU Spot*.

Para o tipo de quota spot vCPU, as quotas vCPU do Gestor de Recursos são aplicadas em todas as séries de máquinas virtuais disponíveis como um único limite regional.

Sempre que você implantar uma nova VM Spot, o uso total de vCPU novo e existente para todas as instâncias de VM Spot não deverá exceder o limite de cota vCPU Spot aprovado. Se a cota de spot for excedida, a implantação da VM Spot não será permitida.

Este artigo discute como solicitar um aumento no limite de cota vCPU Spot usando o portal do Azure.

Para saber mais sobre as quotas padrão vCPU, consulte [as quotas vCPU](../../virtual-machines/windows/quotas.md) da máquina virtual e os limites de [subscrição e serviço do Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

Para aprender sobre o aumento do limite de vCPU por região, consulte [a quota Standard: Aumentar os limites por região](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Solicitar um aumento do limite de quota da Ajuda + suporte

Para solicitar um aumento de limite de quota spot para todas as séries de máquinas virtuais utilizando **ajuda + suporte:**

> [!NOTE]
> Você também pode solicitar um aumento de limite de cota para várias regiões por meio de um único caso de suporte. Para obter detalhes, consulte a etapa 8.

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

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

1. Em **detalhes da Quota,** faça os seguintes passos:

   1. Para **o modelo de implantação,** selecione o modelo apropriado e, para **localizações,** selecione uma localização.

      ![Fornecer detalhes adicionais sobre as quotas](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Para a localização selecionada, em **Tipos,** em **Selecionar um tipo,** escolha **Spot**.

      ![Selecione o tipo de ponto](./media/resource-manager-core-quotas-request/select-spot-type.png)

       Em **tipos**, você pode solicitar tipos de cota padrão e spot de um único caso de suporte por meio do suporte a várias seleções.

       Para mais informações, consulte [a quota Standard: Aumentar os limites por sérieVM](per-vm-quota-requests.md).

   1. Insira o novo limite de cota que você deseja para esta assinatura.

      ![Selecione uma nova quota para spot VM](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Locais**, e, em seguida, selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Especificar localizações adicionais em detalhes de quotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Solicitar um aumento do limite de quota do painel de assinaturas

Para solicitar um aumento do limite de quota spot para todas as séries VM do painel **de Assinaturas:**

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

1. Selecione **Seguinte: Soluções** para abrir **DETALHES DE PROBLEMAS**. Selecione **Fornecer detalhes** para introduzir informações adicionais. Em **detalhes da Quota,** introduza as seguintes informações:

   1. Para **o modelo de implantação,** selecione o modelo apropriado e, para **localizações,** selecione uma localização.

      ![Fornecer detalhes das quotas](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Para a localização selecionada, em **Tipos,** em **Selecionar um tipo,** escolha **Spot**.

      ![Selecione tipo Spot](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Para mais informações, consulte [a quota Standard: Aumentar os limites por sérieVM](per-vm-quota-requests.md).

   1. Insira o novo limite de cota que você deseja para esta assinatura.

      ![Introduza um novo valor para o limite vCPU](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Para solicitar um aumento de quota para mais de um local, selecione uma localização adicional em **Locais**, e, em seguida, selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Selecione localizações adicionais em detalhes de quotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.
