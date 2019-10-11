---
title: Solicitações de aumento de cota regional do Azure | Microsoft Docs
description: Solicitações de aumento de cota regional
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e73f22b0e617ad8f20b98c3bb0fb1647bf5fe61d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249076"
---
# <a name="total-regional-vcpu-limit-increase"></a>Aumento do limite total regional de vCPU 

As cotas de vCPU do Resource Manager para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são impostas em duas camadas para cada assinatura, em cada região. 

A primeira camada é o **limite de VCPUs regional total** (em toda a série de VM), e a segunda camada é o **limite de vCPUs por série de VMs** (como a série D vCPUs). Sempre que uma nova VM for implantada, a soma do uso de vCPUs novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Além disso, a contagem de vCPU total e nova e existente implantada em toda a série de VMs não deve exceder a cota de vCPUs regional total aprovada para a assinatura. Se qualquer uma dessas cotas for excedida, a implantação da VM não será permitida.
Você pode solicitar um aumento do limite de cota de vCPUs para a série de VMs de portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPUs regional total pelo mesmo valor. 

Quando uma nova assinatura é criada, o vCPUs regional total padrão pode não ser igual à soma de cotas de vCPU padrão para todas as séries de VMs individuais. Isso pode resultar em uma assinatura com cota suficiente para cada série de VM individual que você deseja implantar, mas não cota suficiente para o total de vCPUs regionais para todas as implantações. Nesse caso, será necessário enviar uma solicitação para aumentar o limite de vCPUs regional total explicitamente. O limite de vCPUs regional total não pode exceder a soma da cota aprovada em todas as séries de VM para a região.

Saiba mais sobre cotas na [página de cotas do vCPU de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e na página [limites de serviço e assinatura do Azure](https://aka.ms/quotalimits) . 

Agora você pode solicitar um aumento via folha **ajuda + suporte** ou a folha **usos + cota** no Portal. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>O aumento da cota de vCPUs regional total de solicitação no nível da assinatura usando a folha **ajuda + suporte**

Siga as instruções abaixo para criar uma solicitação de suporte por meio da folha ' ajuda + suporte ' do Azure disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **ajuda + suporte**.

![Ajuda + suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecione **Novo pedido de suporte**. 

![Novo pedido de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Na lista suspensa tipo de problema, escolha **limites de serviço e assinatura (cotas)** .

![Lista suspensa tipo de problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecione a subscrição que precisa de uma quota maior.

![Selecionar notícias de assinatura](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecione **outras solicitações** no menu suspenso **tipo de cota** .

![Quotatype](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. No painel de **detalhes** , forneça informações adicionais de acordo com o exemplo abaixo, para ajudar a processar sua solicitação e continuar com a criação do caso. 
    1.  **Modelo de implantação** – Especifique ' Resource Manager '
    2.  **Região solicitada** – especifique a região necessária, por exemplo, leste dos EUA 2
    3.  **Novo valor de limite** – especifique o novo limite de região. Isso não deve exceder a soma da cota aprovada para famílias de SKU individuais para esta assinatura

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Solicitação de aumento total da cota de vCPUs regional no nível da assinatura usando a folha **usos + cota**

Siga as instruções abaixo usando para criar uma solicitação de suporte por meio da folha ' uso + cota ' do Azure disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **assinaturas**.

![Subscrições](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecione a subscrição que precisa de uma quota maior.

![Selecionar subscrição](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selecionar **uso + cotas**

![Selecionar uso e cotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. No canto superior direito, selecione **solicitar aumento**.

![Aumento da solicitação](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selecione **outras solicitações** no menu suspenso **tipo de cota** .

![Quotatype](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. No painel de **detalhes** , forneça informações adicionais de acordo com o exemplo abaixo, para ajudar a processar sua solicitação e continuar com a criação do caso. 
    1.  **Modelo de implantação** – Especifique ' Resource Manager '
    2.  **Região solicitada** – especifique a região necessária, por exemplo, leste dos EUA 2
    3.  **Novo valor de limite** – especifique o novo limite de região. Isso não deve exceder a soma da cota aprovada para famílias de SKU individuais para esta assinatura

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



