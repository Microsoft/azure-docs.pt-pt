---
title: Aumentar a quota de vCPU do Azure por VM pedidos | Documentos da Microsoft
description: por quota de vCPU VM aumentar pedidos
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 79879cd7f5ea5af1b794735f32e6e1367458e124
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310731"
---
# <a name="vm-series-vcpu-limit-increase"></a>Aumento do limite de vCPU das Séries de VM

Quotas de vCPU de Gestor de recursos para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são impostas em dois escalões para cada subscrição, em cada região. 

A primeira camada é o **limite Total Regional de vCPUs** (em todas as séries de VM), e a segunda camada é o **por série de VM vCPUs limitar** (por exemplo, a série D vCPUs). Sempre que uma nova VM vai ser implementada, a soma da utilização de vCPUs novos e existentes para a série de VM não pode exceder a quota de vCPU aprovada para a série de VM específica. Além disso, a contagem de vCPU total de novas e existentes implementada em todas as séries de VM não deve exceder a quota de vCPUs Total Regional aprovada para a subscrição. Se qualquer um das quotas são excedido, a implementação da VM será não ser permitida.
Pode pedir um aumento de limite de quota de vCPUs para a série VM no portal do Azure. Um aumento de quota de série de VM aumenta automaticamente o limite Total Regional de vCPUs na mesma proporção. 

Quando é criada uma nova subscrição, o padrão de vCPUs regionais, o Total pode não ser igual à soma de quotas de vCPU predefinidas para todas as séries de VM individuais. Isso pode resultar numa subscrição com quota suficiente para cada série de VM individuais que pretende implementar, mas não suficientes quota de vCPUs regionais Total para todas as implementações. Neste caso, terá de submeter um pedido para aumentar o limite Total Regional de vCPUs explicitamente. Limite de total de vCPUs regionais não pode exceder a soma de quota aprovado em todas as séries VM para a região.

Saiba mais sobre quotas no [página de quotas de vCPU de Máquina Virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e [subscrição do Azure e limites do serviço](https://aka.ms/quotalimits) página. 

Agora pode solicitar um aumento em **ajuda + suporte** painel ou o **utilizações + Quota** painel no portal. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Solicitar por aumento de quota de vCPU de série de VM através de nível de subscrição do **ajuda + suporte** painel

Siga as instruções abaixo para criar um pedido de suporte através "Ajuda + suporte" painel do Azure disponível no portal do Azure. 

1. Partir https://portal.azure.com, selecione **ajuda + suporte**.

![Ajuda + suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecione **Novo pedido de suporte**. 

![Novo pedido de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Na lista pendente Tipo de problema, escolha **limites de serviço e subscrição (cotas)** .

![Lista pendente Tipo de problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecione a subscrição que precisa de uma quota maior.

![Selecione a subscrição newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecione **aumenta o limite de subscrição - VM (núcleos vCPUs) de computação** na **tipo de quota** lista pendente. 

![Selecione o tipo de quota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. Na **detalhes do problema**, forneça informações adicionais para ajudar o processo de seu pedido clicando **fornecem detalhes**.

![Forneça detalhes](./media/resource-manager-core-quotas-request/provide-details.png)

7. Na **detalhes da Quota** painel, selecione o modelo de implementação e selecione uma localização.

![Detalhes da quota DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Selecione o **famílias de SKU** que exigem um aumento. 

![Família de SKU](./media/resource-manager-core-quotas-request/sku-family.png)

9. Introduza nos novos limites de que pretende na subscrição. Para remover uma linha, desmarque o SKU no menu pendente família SKU ou clique no ícone de rejeição "x". Depois de introduzir a quota pretendida para cada família SKU, clique em **guardar e continuar** no painel de detalhes de Quota para continuar com a criação do pedido de suporte.

![Novos limites](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Pedido por aumento de quota de vCPU de série de VM através de nível de subscrição **utilizações + Quota** painel

Siga as instruções abaixo para criar um pedido de suporte através do Azure "utilização + quotas" painel disponível no portal do Azure. 

1. Partir https://portal.azure.com, selecione **subscrições**.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecione a subscrição que precisa de uma quota maior.

![Selecionar subscrição](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selecione **utilização + quotas**

![Selecione a utilização e quotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. No canto superior direito, selecione **pedir aumento**.

![Pedir aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selecione **aumenta o limite de subscrição de computação-VM (núcleos vCPUs)** como o tipo de cotação. 

![Preencha o formulário](./media/resource-manager-core-quotas-request/forms.png)
   
6. Na **detalhes da Quota** painel, selecione o modelo de implementação e selecione uma localização.

![Painel de problema de quota](./media/resource-manager-core-quotas-request/problemstep.png)

7. Selecione o **famílias de SKU** que exigem um aumento.

![Séries SKU selecionadas](./media/resource-manager-core-quotas-request/sku-family.png)

8. Introduza nos novos limites de que pretende na subscrição. Para remover uma linha, desmarque o SKU no menu pendente família SKU ou clique no ícone de rejeição "x". Depois de introduzir a quota pretendida para cada família SKU, clique em **guardar e continuar** na página de passo de problema para continuar com a criação do pedido de suporte.

![Pedido de quota de novo SKU](./media/resource-manager-core-quotas-request/new-limits.png)

