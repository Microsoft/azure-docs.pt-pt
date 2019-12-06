---
title: Cota de spot | Microsoft Docs
description: Solicitações de cota de spot
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850572"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>Cota de spot: limite de aumento para todas as séries de VM

As VMs pontuais fornecem um modelo diferente de uso do Azure, negociando um custo menor para permitir que o Azure Remova uma VM conforme necessário para implantações de instância de VM pagas conforme o uso ou reservadas. Leia mais sobre VMs pontuais [aqui](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

O Gerenciador de recursos dá suporte a dois tipos de cotas do vCPU para máquinas virtuais. **As VMs pagas conforme o uso e as instâncias de VM reservadas** usam a cota padrão. As **VMs pontuais** usam a cota Spot. 

Para o tipo de **cota de spot** , as cotas de vCPU do Resource Manager são impostas em toda a série de VMs disponíveis como um único limite regional.

Sempre que uma nova VM Spot for implantada, a soma do uso de vCPUs novo e existente para todas as instâncias de VM Spot não deverá exceder o limite de cota vCPU Spot aprovado. Se a cota de spot for excedida, a implantação da VM Spot não será permitida. Você pode solicitar um aumento do limite de cota vCPUs spot de portal do Azure. 

Saiba mais sobre as cotas de vCPU padrão na página de cotas do vCPU de máquina virtual e na página limites de serviço e assinatura do Azure. Saiba mais sobre como aumentar o limite de vCPU regionais para a cota padrão nesta [página](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

Agora você pode solicitar um aumento nos **limites de cota spot para todas as séries de VM** por meio da folha **ajuda + suporte** ou da folha **usos + cota** no Portal.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Aumento do limite de cota de solicitação para todas as séries VM por assinatura usando a folha ajuda + suporte

Siga as instruções abaixo para criar uma solicitação de suporte por meio da folha ' ajuda + suporte ' do Azure disponível no portal do Azure.

Você também pode **solicitar a cota para várias regiões** por meio de um único caso de suporte. Consulte a etapa 10 abaixo para obter detalhes. 


1. Em https://portal.azure.com, selecione **ajuda + suporte**.

   ![Ajuda + Suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
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
   
7.  No painel **detalhes da cota***, selecione **modelo de implantação** e selecione um **local**.

![Fornecer detalhes](./media/resource-manager-core-quotas-request/3-7.png)

8. Para o local selecionado, selecione valor de **tipo** como **' spot '** . Você pode solicitar tipos de cota padrão e spot de um único caso de suporte por meio do suporte de seleção múltipla no campo **tipo** . Saiba mais sobre como **aumentar a cota padrão por série de VM** nesta [página](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Fornecer detalhes](./media/resource-manager-core-quotas-request/3-8.png)

9.  Insira o novo limite que você deseja na assinatura. 
 
 ![Fornecer detalhes](./media/resource-manager-core-quotas-request/3-9.png)

10. Para solicitar a cota para mais de um local, você pode verificar em outro local na lista suspensa e selecionar o tipo de VM apropriado. Em seguida, você pode inserir os novos limites desejados.

![Fornecer detalhes](./media/resource-manager-core-quotas-request/3-10.png)

11. Depois de inserir a cota desejada, clique em **salvar e continue** no painel detalhes da cota para continuar com a criação da solicitação de suporte.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Aumento do limite de cota de solicitação para todas as séries de VM por assinatura usando usos + folha de cota

Siga as instruções abaixo usando para criar uma solicitação de suporte por meio da folha ' uso + cota ' do Azure disponível no portal do Azure.

Você também pode **solicitar a cota para várias regiões** por meio de um único caso de suporte. Consulte a etapa 9 abaixo para obter detalhes. 

1.  Em https://portal.azure.com, selecione **assinaturas**.

 ![Subscrições](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  Selecione a subscrição que precisa de uma quota maior.

 ![Selecionar subscrição](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  Selecione **uso + cotas**.

 ![Selecionar uso e cotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  No canto superior direito, selecione **solicitar aumento.**

   ![Aumento da solicitação](./media/resource-manager-core-quotas-request/request-increase.png)

5.  Selecione **computação – o limite de assinatura de VM (núcleos-vCPUs) aumenta** como o tipo de cotação.

  ![Preencher formulário](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  No painel **detalhes da cota** , selecione modelo de implantação e selecione um local.

  ![Preencher formulário](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  Para o local selecionado, selecione valor de **tipo** como **' spot '.** Você pode solicitar tipos de cota padrão e spot de um único caso de suporte por meio do suporte de seleção múltipla no campo **tipo** . Saiba mais sobre como **aumentar a cota padrão por série de VM** nesta [página](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Preencher formulário](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Insira o novo limite que você deseja na assinatura.

  ![Preencher formulário](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Para solicitar a cota para mais de um local, você pode verificar em outro **local** na lista suspensa e selecionar o tipo de VM apropriado. Em seguida, você pode inserir os novos limites desejados.

  ![Preencher formulário](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. Depois de inserir a cota desejada, clique em **salvar e continue** no painel detalhes da cota para continuar com a criação da solicitação de suporte.


