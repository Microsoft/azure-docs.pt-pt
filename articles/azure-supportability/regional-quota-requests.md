---
title: Solicitações de aumento de cota regional do Azure | Microsoft Docs
description: Solicitações de aumento de cota regional
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 132cf6ccfec5af9951f5dc6d6a3c6d3c81363d81
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850011"
---
# <a name="standard-quota-regional-vcpu-limit-increase"></a>Cota padrão: aumento do limite de vCPU regionais 

O Gerenciador de recursos dá suporte a dois tipos de cotas do vCPU para máquinas virtuais. **As VMs pagas conforme o** uso e as **instâncias de VM reservadas** usam a cota padrão. As **VMs pontuais** usam a cota Spot. 

A cota de vCPU padrão para instâncias de VMs pagas conforme o uso e as de VM reservadas são impostas em duas camadas para cada assinatura em cada região.
 
A primeira camada é o **limite de VCPUs regional total** (em toda a série de VM), e a segunda camada é o **limite de vCPUs por série de VMs** (como a série D vCPUs). Sempre que uma nova VM for implantada, a soma do uso de vCPUs novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Além disso, a contagem de vCPU total e nova e existente implantada em toda a série de VMs não deve exceder a cota de vCPUs regional total aprovada para a assinatura. Se qualquer uma dessas cotas for excedida, a implantação da VM não será permitida. Você pode solicitar um aumento do limite de cota de vCPUs para a série de VMs de portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPUs regional total pelo mesmo valor.

Quando uma nova assinatura é criada, o vCPUs regional total padrão pode não ser igual à soma de cotas de vCPU padrão para todas as séries de VMs individuais. Isso pode resultar em uma assinatura com cota suficiente para cada série de VM individual que você deseja implantar, mas não cota suficiente para o total de vCPUs regionais para todas as implantações. Nesse caso, será necessário enviar uma solicitação para aumentar o limite de vCPUs regional total explicitamente. O limite de vCPUs regional total não pode exceder a soma da cota aprovada em todas as séries de VM para a região.

Saiba mais sobre as cotas de vCPU padrão na [página de cotas do vCPU de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e na página [limites de serviço e assinatura do Azure](https://aka.ms/quotalimits) .

Saiba mais sobre como **aumentar os limites de vCPU de VM Spot** [aqui](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Você pode solicitar um aumento no **limite de vCPU regional total de VM padrão** por meio da folha **ajuda + suporte** ou da folha **usos + cota** no Portal.

## <a name="request-standard-quota-regional-vcpu-limit-increase-at-subscription-level-using-the-help--support-blade"></a>Solicitar aumento de limite de vCPU regional da cota padrão no nível da assinatura usando a folha ajuda + suporte

Siga as instruções abaixo para criar uma solicitação de suporte por meio da folha ' ajuda + suporte ' do Azure disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **ajuda + suporte**.

![Ajuda + Suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecione **Novo pedido de suporte**. 

![Novo pedido de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Na lista suspensa tipo de problema, escolha **limites de serviço e assinatura (cotas)** .

![Lista suspensa tipo de problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecione a subscrição que precisa de uma quota maior.

![Selecionar notícias de assinatura](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecione **outras solicitações** na lista suspensa **tipo de cota** .

![Tipo de Quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. No painel de **detalhes** , forneça informações adicionais de acordo com o exemplo abaixo, para ajudar a processar sua solicitação e continuar com a criação do caso. 
    1.  **Modelo de implantação** – Especifique ' Resource Manager '
    2.  **Região solicitada** – especifique a região necessária, por exemplo, leste dos EUA 2
    3.  **Novo valor de limite** – especifique o novo limite de região. Isso não deve exceder a soma da cota aprovada para famílias de SKU individuais para esta assinatura

![Detalhes da cota](./media/resource-manager-core-quotas-request/regional-details.png)

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

5. Selecione **outras solicitações** na lista suspensa **tipo de cota** .

![Tipo de Quota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. No painel de **detalhes** , forneça informações adicionais de acordo com o exemplo abaixo, para ajudar a processar sua solicitação e continuar com a criação do caso. 
    1.  **Modelo de implantação** – Especifique ' Resource Manager '
    2.  **Região solicitada** – especifique a região necessária, por exemplo, leste dos EUA 2
    3.  **Novo valor de limite** – especifique o novo limite de região. Isso não deve exceder a soma da cota aprovada para famílias de SKU individuais para esta assinatura

![Detalhes da cota](./media/resource-manager-core-quotas-request/regional-details.png)



