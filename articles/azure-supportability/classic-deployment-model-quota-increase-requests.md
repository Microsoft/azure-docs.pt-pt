---
title: Modelo de implantação clássico do Azure | Microsoft Docs
description: Modelo de implantação clássico do Azure
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c7860a098096d718a6c5d7cd661ef2b1c1b21e89
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802671"
---
# <a name="classic-deployment-model"></a>Modelo de implementação clássica

O modelo de implantação clássico é o modelo de implantação do Azure de geração mais antiga. Ele impõe um limite de cota de vCPU global para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais. O modelo de implantação clássico não é mais recomendado e agora é substituído pelo modelo do Resource Manager. 

Para saber mais sobre esses dois modelos de implantação e as vantagens de usar o Resource Manager, consulte o [Resource Manager e](../azure-resource-manager/resource-manager-deployment-model.md) a página de implantação clássica.
 
Quando uma nova assinatura é criada, uma cota padrão de vCPUs é atribuída a ela. Sempre que uma nova VM for implantada usando o modelo de implantação clássico, a soma do uso de vCPU novo e existente em todas as regiões não deverá exceder a cota vCPU aprovada para o modelo de implantação clássico.
 
Saiba mais sobre cotas na página de [limites de serviço e assinatura do Azure](https://aka.ms/quotalimits) .

Você pode solicitar um aumento no limite de cota de vCPU para o modelo de implantação clássico por meio da folha ' ajuda + suporte ' ou da folha ' usos + cota ' no Portal.

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

7. No painel **detalhes da cota** , selecione clássico e selecione um local.

   ![Detalhes da cota DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Selecione as **famílias de SKU** que exigem um aumento. 

   ![Família de SKU](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Insira os novos limites que você deseja na assinatura. Para remover uma linha, desmarque a SKU da lista suspensa da família de SKUs ou clique no ícone descartar "x". Depois de inserir a cota desejada para cada família de SKUs, clique em **salvar e continue** no painel detalhes da cota para continuar com a criação da solicitação de suporte.

   ![Novos limites](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Solicitação por série de VM vCPU aumento de cota no nível da assinatura usando **usos +** folha de cota

Siga as instruções abaixo usando para criar uma solicitação de suporte por meio da folha ' uso + cota ' do Azure disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **assinaturas**.

   ![Subscrições](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecione a subscrição que precisa de uma quota maior.

   ![Selecionar subscrição](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selecionar **uso + cotas**

   ![Selecionar uso e cotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. No canto superior direito, selecione **solicitar aumento**.

   ![Pedir aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Selecione **computação – o limite de assinatura de VM (núcleos-vCPUs) aumenta** como o tipo de cotação. 

   ![Preencher formulário](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. Em **detalhes do problema**, forneça informações adicionais para ajudar a processar sua solicitação clicando em **fornecer detalhes**.

   ![Fornecer detalhes](./media/resource-manager-core-quotas-request/provide-details.png)

7. No painel **detalhes da cota** , selecione clássico e selecione um local.

   ![Detalhes da cota DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. Selecione as **famílias de SKU** que exigem um aumento. 

   ![Família de SKU](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. Insira os novos limites que você deseja na assinatura. Para remover uma linha, desmarque a SKU da lista suspensa da família de SKUs ou clique no ícone descartar "x". Depois de inserir a cota desejada para cada família de SKUs, clique em **salvar e continue** no painel detalhes da cota para continuar com a criação da solicitação de suporte.

   ![Novos limites](./media/resource-manager-core-quotas-request/new-limits-classic.png)

