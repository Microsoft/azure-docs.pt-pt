---
title: Solicitações de aumento de cota de vCPU do Azure por VM | Microsoft Docs
description: solicitações de aumento de cota de vCPU por VM
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e99bdd92d4a1aab833a95943b22aaabe7f0daa1d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248669"
---
# <a name="vm-series-vcpu-limit-increase"></a>Aumento do limite de vCPU das Séries de VM

As cotas de vCPU do Resource Manager para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são impostas em duas camadas para cada assinatura, em cada região. 

A primeira camada é o **limite de VCPUs regional total** (em toda a série de VM), e a segunda camada é o **limite de vCPUs por série de VMs** (como a série D vCPUs). Sempre que uma nova VM for implantada, a soma do uso de vCPUs novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Além disso, a contagem de vCPU total e nova e existente implantada em toda a série de VMs não deve exceder a cota de vCPUs regional total aprovada para a assinatura. Se qualquer uma dessas cotas for excedida, a implantação da VM não será permitida.
Você pode solicitar um aumento do limite de cota de vCPUs para a série de VMs de portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPUs regional total pelo mesmo valor. 

Saiba mais sobre cotas na [página de cotas do vCPU de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e na página [limites de serviço e assinatura do Azure](https://aka.ms/quotalimits) . 

Agora você pode solicitar um aumento via folha **ajuda + suporte** ou a folha **usos + cota** no Portal. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>A solicitação por série de VM vCPU aumento de cota no nível da assinatura usando a folha **ajuda + suporte**

Siga as instruções abaixo para criar uma solicitação de suporte por meio da folha ' ajuda + suporte ' do Azure disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **ajuda + suporte**.

   ![Ajuda + suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecione **Novo pedido de suporte**. 

     ![Novo pedido de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Na lista suspensa tipo de problema, escolha **limites de serviço e assinatura (cotas)** .

   ![Lista suspensa tipo de problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecione a subscrição que precisa de uma quota maior.

   ![Selecionar notícias de assinatura](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecione **computação – limite de assinatura de VM (núcleos-vCPUs) aumentos** no menu suspenso **tipo de cota** . 

   ![Selecionar tipo de cota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. Em **detalhes do problema**, forneça informações adicionais para ajudar a processar sua solicitação clicando em **fornecer detalhes**.

   ![Fornecer detalhes](./media/resource-manager-core-quotas-request/provide-details.png)

7. No painel **detalhes da cota** , selecione modelo de implantação e selecione um local.

   ![Detalhes da cota DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Selecione as **famílias de SKU** que exigem um aumento. 

   ![Família de SKU](./media/resource-manager-core-quotas-request/sku-family.png)

9. Insira os novos limites que você deseja na assinatura. Para remover uma linha, desmarque a SKU da lista suspensa da família de SKUs ou clique no ícone descartar "x". Depois de inserir a cota desejada para cada família de SKUs, clique em **salvar e continue** no painel detalhes da cota para continuar com a criação da solicitação de suporte.

   ![Novos limites](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Solicitação por série de VM vCPU aumento de cota no nível da assinatura usando **usos +** folha de cota

Siga as instruções abaixo usando para criar uma solicitação de suporte por meio da folha ' uso + cota ' do Azure disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **assinaturas**.

   ![Subscrições](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecione a subscrição que precisa de uma quota maior.

   ![Selecionar subscrição](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selecionar **uso + cotas**

   ![Selecionar uso e cotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. No canto superior direito, selecione **solicitar aumento**.

   ![Aumento da solicitação](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selecione **computação – o limite de assinatura de VM (núcleos-vCPUs) aumenta** como o tipo de cotação. 

   ![Preencher formulário](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. No painel **detalhes da cota** , selecione modelo de implantação e selecione um local.

   ![Folha problema de cota](./media/resource-manager-core-quotas-request/quota-details.png)

7. Selecione as **famílias de SKU** que exigem um aumento.

   ![Série de SKUs selecionada](./media/resource-manager-core-quotas-request/sku-family.png)

8. Insira os novos limites que você deseja na assinatura. Para remover uma linha, desmarque a SKU da lista suspensa da família de SKUs ou clique no ícone descartar "x". Depois de inserir a cota desejada para cada família de SKUs, clique em **salvar e continue** na página etapa do problema para continuar com a criação da solicitação de suporte.

   ![Nova solicitação de cota de SKU](./media/resource-manager-core-quotas-request/new-limits.png)
 
