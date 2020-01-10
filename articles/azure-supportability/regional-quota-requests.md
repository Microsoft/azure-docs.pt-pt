---
title: Solicitar um aumento nos limites de cota do vCPU regional do Azure | Microsoft Docs
description: Solicitações de aumento de cota regional
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e262651a6e040c40dbe240ad3437eff1914aa3e5
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750839"
---
# <a name="standard-quota-increase-limits-by-region"></a>Cota padrão: aumentar os limites por região 

O Azure Resource Manager dá suporte a dois tipos de cotas do vCPU para máquinas virtuais:
* *As VMs pagas conforme o uso* e as *instâncias de VM reservadas* estão sujeitas a uma *cota de vCPU padrão*.
* As *VMs pontuais* estão sujeitas a uma *cota de vCPU Spot*. 

A cota padrão do vCPU para instâncias de VM pagas conforme o uso e reservada é imposta em duas camadas para cada assinatura em cada região:
* A primeira camada é o *limite de vCPUs regional total* (em toda a série de VMs).
* A segunda camada é o *limite de vCPUs por VM da série* (como a vCPUs da série D).
 
Sempre que você implantar uma nova VM Spot, o uso total de vCPU novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Além disso, o número total de vCPUs novas e existentes implantadas em toda a série de VMs não deve exceder a cota de vCPU regional total aprovada para a assinatura. Se uma dessas cotas for excedida, a implantação da VM não será permitida. 

Você pode solicitar um aumento no limite de cota de vCPU para a série de VMs usando o portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPU regional total pelo mesmo valor.

Quando você cria uma nova assinatura, o número total padrão de vCPUs regionais pode não ser igual à cota de vCPU padrão total para todas as séries de VMs individuais. Essa discrepância pode resultar em uma assinatura com cota suficiente para cada série de VM individual que você deseja implantar. Mas pode não haver cota suficiente para acomodar o total regional de vCPUs para todas as implantações. Nesse caso, você deve enviar uma solicitação para aumentar explicitamente o limite do número total de vCPUs regionais. O limite de vCPU regional total não pode exceder a cota total aprovada em todas as séries de VM para a região.

Para saber mais sobre as cotas de vCPU padrão, consulte [cotas de vCPU de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e [limites de serviço e assinatura do Azure](https://aka.ms/quotalimits).

Para saber mais sobre como aumentar os limites de vCPU de VM Spot, confira [cota de spot: aumentar os limites para todas as séries de VM](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Você pode solicitar um aumento no limite de cota padrão do vCPU por região de uma das duas maneiras, conforme descrito nas seções a seguir.

## <a name="request-a-quota-increase-by-region-from-the-help--support-pane"></a>Solicitar um aumento de cota por região do painel "ajuda + suporte"

Para solicitar um aumento de cota de vCPU por região do painel **ajuda + suporte** , faça o seguinte: 

1. No painel esquerdo da [portal do Azure](https://portal.azure.com), selecione **ajuda + suporte**.

   ![O link "ajuda + suporte"](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. No painel **ajuda + suporte** , selecione **nova solicitação de suporte**. 

    ![Novo pedido de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. Na lista suspensa **tipo de problema** , selecione limites de **serviço e assinatura (cotas)** .

   ![A lista suspensa "tipo de problema"](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Na lista suspensa **assinatura** , selecione a assinatura cuja cota você deseja aumentar.

   ![A lista suspensa "assinatura"](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. Na lista suspensa **tipo de cota** , selecione **outras solicitações**.

   ![A lista suspensa "tipo de cota"](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. No painel **detalhes do problema** , na caixa **Descrição** , forneça as seguintes informações adicionais: 

    a. Para o **modelo de implantação**, especifique **Resource Manager**.  
    b. Para **região**, especifique sua região necessária (por exemplo, **leste dos EUA 2**).  
    c. Para o **novo limite**, especifique um novo limite de vCPU para a região. Esse valor não deve exceder a soma das cotas aprovadas para a série SKU individual desta assinatura.

    ![O painel "detalhes do problema"](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.

## <a name="request-a-quota-increase-by-region-from-the-subscriptions-pane"></a>Solicitar um aumento de cota por região do painel "assinaturas"

Para solicitar um aumento de cota de vCPU por região no painel de **assinaturas** , faça o seguinte: 

1. No painel esquerdo da [portal do Azure](https://portal.azure.com), selecione **assinaturas**.

   ![O link "assinaturas"](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![O painel "assinaturas"](./media/resource-manager-core-quotas-request/select-subscription.png)

1. No painel esquerdo da página **> nome da assinatura do\<** , selecione **uso + cotas**.

   ![O link "uso + cotas"](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. No canto superior direito, selecione **solicitar aumento**.

   ![Aumento da solicitação](./media/resource-manager-core-quotas-request/request-increase.png)

1. Na lista suspensa **tipo de cota** , selecione **outras solicitações**.

   ![A lista suspensa "tipo de cota"](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. No painel **detalhes do problema** , na caixa **Descrição** , forneça as seguintes informações adicionais: 

    a. Para o **modelo de implantação**, especifique **Resource Manager**.  
    b. Para **região**, especifique sua região necessária (por exemplo, **leste dos EUA 2**).  
    c. Para o **novo limite**, especifique um novo limite de vCPU para a região. Esse valor não deve exceder a soma das cotas aprovadas para a série SKU individual desta assinatura.

    ![O painel "detalhes do problema"](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.

