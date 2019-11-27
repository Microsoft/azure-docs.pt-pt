---
title: Solicitações de aumento de cota de vCPU do Azure por VM | Microsoft Docs
description: solicitações de aumento de cota de vCPU por VM
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: ccd0c88c95ae9a752ef8ea2387bbde4f8559bc68
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74531754"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>Cota padrão: aumento de limite de vCPU por série de VM

O Gerenciador de recursos dá suporte a dois tipos de cotas do vCPU para máquinas virtuais. **As VMs pagas conforme o uso e as instâncias de VM reservadas** usam a cota padrão. As **VMs de baixa prioridade** usam a cota de baixa prioridade. A cota de vCPU padrão para instâncias de VMs pagas conforme o uso e as de VM reservadas são impostas em duas camadas para cada assinatura em cada região

A primeira camada é o **limite de VCPUs regional total** (em toda a série de VM), e a segunda camada é o **limite de vCPUs por série de VMs** (como a vCPUs da série Dv3). Sempre que uma nova VM for implantada, a soma do uso de vCPUs novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Além disso, a contagem de vCPU total e nova e existente implantada em toda a série de VMs não deve exceder a cota de vCPUs regional total aprovada para a assinatura. Se qualquer uma dessas cotas for excedida, a implantação da VM não será permitida.
Você pode solicitar um aumento do limite de cota de vCPUs para a série de VMs de portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPUs regional total pelo mesmo valor. 

Saiba mais sobre as cotas de vCPU padrão na [página de cotas do vCPU de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e na [página limites de serviço e assinatura do Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Saiba mais sobre como aumentar o limite de vCPU regionais para a cota padrão [aqui](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests). 

Saiba mais sobre como **aumentar os limites de vCPU de VM de baixa prioridade** [aqui](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Você pode solicitar um aumento nos **limites de cota padrão do vCPU por série de VM** por meio da folha **ajuda + suporte** ou da folha **usos + cota** no Portal.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>Solicitar aumento de cota de vCPU padrão por série de VM no nível de assinatura usando a folha ajuda + suporte

Siga as instruções abaixo para criar uma solicitação de suporte por meio da folha ' ajuda + suporte ' do Azure disponível no portal do Azure. 

Você também pode solicitar a cota para várias regiões por meio de um único caso de suporte. Consulte a etapa 11 abaixo para obter detalhes.

1. Em https://portal.azure.com, selecione **ajuda + suporte**.

   ![Ajuda + suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecione **Novo pedido de suporte**. 

     ![Novo pedido de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Na lista suspensa tipo de problema, escolha **limites de serviço e assinatura (cotas)** .

   ![Lista suspensa tipo de problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecione a subscrição que precisa de uma quota maior.

   ![Selecionar notícias de assinatura](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecione **computação – limite de assinatura de VM (núcleos-vCPUs) aumentos** na lista suspensa **tipo de cota** . 

   ![Selecionar tipo de cota](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. Em **detalhes do problema**, forneça informações adicionais para ajudar a processar sua solicitação clicando em **fornecer detalhes**.

   ![Fornecer detalhes](./media/resource-manager-core-quotas-request/provide-details.png)

7. No painel **detalhes da cota** , selecione **modelo de implantação** e selecione um **local.**

   ![Detalhes da cota DM](./media/resource-manager-core-quotas-request/1-7.png)

8. Para o local selecionado, selecione valor de **tipo** como **' padrão '** . Você pode solicitar tipos de cota padrão e de baixa prioridade de um único caso de suporte por meio do suporte de seleção múltipla no campo **tipo** . Saiba mais sobre como **aumentar os limites de cota de baixa prioridade** na **página de > de <** .

   ![Família de SKU](./media/resource-manager-core-quotas-request/1-8.png)

9. Selecionar as **famílias de SKU** que exigem um aumento

   ![Família de SKU](./media/resource-manager-core-quotas-request/1-9.png)

10. Insira os novos limites que você deseja na assinatura. Para remover uma linha, desmarque a SKU da lista suspensa família de SKUs ou clique no ícone descartar "x". 

   ![Novos limites](./media/resource-manager-core-quotas-request/1-10.png)

11. Para solicitar a cota para mais de um local, você pode verificar em outro **local** na lista suspensa e selecionar o tipo de VM apropriado. Esta etapa sobrecarrega as famílias de SKU selecionadas para o **local** anterior em relação ao novo local e você pode simplesmente inserir os novos limites desejados.

   ![Vários locais](./media/resource-manager-core-quotas-request/1-11.png)
   
12. Depois de inserir a cota desejada para cada família de SKUs, clique em **salvar e continue** no painel detalhes da cota para continuar com a criação da solicitação de suporte.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>Solicitar aumento de cota de vCPU padrão por série de VM no nível de assinatura usando usos + folha de cota

Siga as instruções abaixo usando para criar uma solicitação de suporte por meio da folha ' uso + cota ' do Azure disponível no portal do Azure.

Você também pode **solicitar a cota para várias regiões** por meio de um único caso de suporte. Consulte a etapa 10 abaixo para obter detalhes

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

   ![Folha problema de cota](./media/resource-manager-core-quotas-request/1-1-6.png)

7. Para o local selecionado, selecione valor de **tipo** como **' padrão '** . Você pode solicitar tipos de cota padrão e de baixa prioridade de um único caso de suporte por meio do suporte de seleção múltipla no campo **tipo** . Saiba mais sobre como **aumentar os limites de vCPUs de baixa prioridade** nesta [página](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

   ![Série de SKUs selecionada](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. Selecionar as **famílias de SKU** que exigem um aumento

   ![Série de SKUs selecionada](./media/resource-manager-core-quotas-request/1-1-8.png)

9. Insira os novos limites que você deseja na assinatura. Para remover uma linha, desmarque a SKU da lista suspensa família de SKUs ou clique no ícone descartar "x". 

   ![Nova solicitação de cota de SKU](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. Para solicitar a cota para mais de um local, você pode verificar em outro **local** na lista suspensa e selecionar o tipo de VM apropriado. Esta etapa sobrecarrega as famílias de SKU selecionadas para o **local** anterior em relação ao novo local e você pode simplesmente inserir os novos limites desejados.
   
    ![Nova solicitação de cota de SKU](./media/resource-manager-core-quotas-request/1-1-10.png)
 
