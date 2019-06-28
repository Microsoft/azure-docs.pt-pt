---
title: Pedidos de aumentar a quota regional do Azure | Documentos da Microsoft
description: Pedidos de aumento de regional quota
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7ac6dce9aec1c363fa9772caabad9ce542d43888
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310657"
---
# <a name="total-regional-vcpu-limit-increase"></a>Aumento do limite total regional de vCPU 

Quotas de vCPU de Gestor de recursos para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são impostas em dois escalões para cada subscrição, em cada região. 

A primeira camada é o **limite Total Regional de vCPUs** (em todas as séries de VM), e a segunda camada é o **por série de VM vCPUs limitar** (por exemplo, a série D vCPUs). Sempre que uma nova VM vai ser implementada, a soma da utilização de vCPUs novos e existentes para a série de VM não pode exceder a quota de vCPU aprovada para a série de VM específica. Além disso, a contagem de vCPU total de novas e existentes implementada em todas as séries de VM não deve exceder a quota de vCPUs Total Regional aprovada para a subscrição. Se qualquer um das quotas são excedido, a implementação da VM será não ser permitida.
Pode pedir um aumento de limite de quota de vCPUs para a série VM no portal do Azure. Um aumento de quota de série de VM aumenta automaticamente o limite Total Regional de vCPUs na mesma proporção. 

Quando é criada uma nova subscrição, o padrão de vCPUs regionais, o Total pode não ser igual à soma de quotas de vCPU predefinidas para todas as séries de VM individuais. Isso pode resultar numa subscrição com quota suficiente para cada série de VM individuais que pretende implementar, mas não suficientes quota de vCPUs regionais Total para todas as implementações. Neste caso, terá de submeter um pedido para aumentar o limite Total Regional de vCPUs explicitamente. Limite de total de vCPUs regionais não pode exceder a soma de quota aprovado em todas as séries VM para a região.

Saiba mais sobre quotas no [página de quotas de vCPU de Máquina Virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e [subscrição do Azure e limites do serviço](https://aka.ms/quotalimits) página. 

Agora pode solicitar um aumento em **ajuda + suporte** painel ou o **utilizações + Quota** painel no portal. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Pedir aumento de quota de vCPUs regionais Total no uso de nível de subscrição do **ajuda + suporte** painel

Siga as instruções abaixo para criar um pedido de suporte através "Ajuda + suporte" painel do Azure disponível no portal do Azure. 

1. Partir https://portal.azure.com, selecione **ajuda + suporte**.

![Ajuda + suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecione **Novo pedido de suporte**. 

![Novo pedido de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Na lista pendente Tipo de problema, escolha **limites de serviço e subscrição (cotas)** .

![Lista pendente Tipo de problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecione a subscrição que precisa de uma quota maior.

![Selecione a subscrição newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecione **Other Requests** na **tipo de quota** lista pendente.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Na **detalhes** painel, fornecem informações adicionais de acordo com o exemplo abaixo, para ajudar a processo seu pedido e continuar com a criação de caso. 
    1.  **Modelo de implementação** – especifique o Gestor de recursos
    2.  **Região pedida** – especificar a sua região necessária, por exemplo, E.U.A. Leste 2
    3.  **Novo valor de limite** – especifique novo limite de região. Isso não deve exceder a soma de quota aprovado para individuais famílias SKU para esta subscrição

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Pedir aumento de quota de vCPUs regionais Total no uso de nível de subscrição do **utilizações + Quota** painel

Siga as instruções abaixo para criar um pedido de suporte através do Azure "utilização + quotas" painel disponível no portal do Azure. 

1. Partir https://portal.azure.com, selecione **subscrições**.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecione a subscrição que precisa de uma quota maior.

![Selecionar subscrição](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selecione **utilização + quotas**

![Selecione a utilização e quotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. No canto superior direito, selecione **pedir aumento**.

![Pedir aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selecione **Other Requests** na **tipo de quota** lista pendente.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. Na **detalhes** painel, fornecem informações adicionais de acordo com o exemplo abaixo, para ajudar a processo seu pedido e continuar com a criação de caso. 
    1.  **Modelo de implementação** – especifique o Gestor de recursos
    2.  **Região pedida** – especificar a sua região necessária, por exemplo, E.U.A. Leste 2
    3.  **Novo valor de limite** – especifique novo limite de região. Isso não deve exceder a soma de quota aprovado para individuais famílias SKU para esta subscrição

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



