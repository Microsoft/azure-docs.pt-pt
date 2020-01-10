---
title: Solicitar um aumento nos limites de cota de vCPU por série de VMs do Azure | Microsoft Docs
description: Este artigo discute como solicitar aumentos de limite de cota por VM vCPU.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750191"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Cota padrão: aumentar os limites por série de VMs

O Azure Resource Manager dá suporte a dois tipos de cotas do vCPU para máquinas virtuais:
* *As VMs pagas conforme o uso* e as *instâncias de VM reservadas* estão sujeitas a uma *cota de vCPU padrão*.
* As *VMs pontuais* estão sujeitas a uma *cota de vCPU Spot*. 

A cota padrão do vCPU para instâncias de VM pagas conforme o uso e reservada é imposta em duas camadas para cada assinatura em cada região:
* A primeira camada é o *limite de vCPUs regional total* (em toda a série de VMs).
* A segunda camada é o *limite de vCPUs da série por VM* (como o vCPUs da série Dv3). 

Sempre que você implantar uma nova VM Spot, o uso total de vCPU novo e existente para essa série de VMs não deverá exceder a cota de vCPU aprovada para essa série de VMs específica. Além disso, o número total de vCPUs novas e existentes implantadas em toda a série de VMs não deve exceder a cota de vCPU regional total aprovada para a assinatura. Se uma dessas cotas for excedida, a implantação da VM não será permitida.

Você pode solicitar um aumento no limite de cota de vCPU para a série de VMs usando o portal do Azure. Um aumento na cota da série de VMs aumenta automaticamente o limite de vCPU regional total pelo mesmo valor. 

Para saber mais sobre as cotas de vCPU padrão, consulte [cotas de vCPU de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e [limites de serviço e assinatura do Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests). 

Para saber mais sobre como aumentar o limite de vCPU por região para a cota padrão, consulte [cota padrão: aumentar os limites por região](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests). 

Para saber mais sobre como aumentar os limites de vCPU de VM Spot, confira [cota de spot: aumentar os limites para todas as séries de VM](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota).

Você pode solicitar um aumento nos limites de cota padrão do vCPU por série de VMs de uma das duas maneiras, conforme descrito nas seções a seguir.

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>Solicitar um aumento de cota padrão do painel "ajuda + suporte"

Para solicitar um aumento de cota de vCPU padrão por série de VMs do painel **ajuda + suporte** , faça o seguinte: 

> [!NOTE]
> Você também pode solicitar um aumento de limite de cota para várias regiões por meio de um único caso de suporte. Para obter detalhes, consulte a etapa 8.

1. No painel esquerdo da [portal do Azure](https://portal.azure.com), selecione **ajuda + suporte**.

   ![O link "ajuda + suporte"](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. No painel **ajuda + suporte** , selecione **nova solicitação de suporte**. 

    ![Novo pedido de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. Na lista suspensa **tipo de problema** , selecione limites de **serviço e assinatura (cotas)** .

   ![A lista suspensa "tipo de problema"](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. Na lista suspensa **assinatura** , selecione a assinatura cuja cota você deseja aumentar.

   ![A lista suspensa "assinatura"](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. Na lista suspensa **tipo de cota** , selecione o **limite de assinaturas computação-VM (núcleos-vCPUs) aumenta**. 

   ![A lista suspensa "tipo de cota"](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Na guia **detalhes** , em **detalhes do problema**, selecione **fornecer detalhes**e, em seguida, insira informações adicionais para ajudar a processar sua solicitação.

   ![O link "fornecer detalhes"](./media/resource-manager-core-quotas-request/provide-details.png)

1. No painel de **detalhes de cota** no canto superior direito, faça o seguinte:

   ![O painel "detalhes da cota"](./media/resource-manager-core-quotas-request/1-7.png)

   a. Na lista suspensa **modelo de implantação** , selecione o modelo apropriado.

   b. Na lista suspensa **locais** , selecione um local. Para o local selecionado, em **tipos**, na caixa **selecionar um tipo** , digite **padrão**.

   ![O painel "detalhes da cota"-tipos de cota](./media/resource-manager-core-quotas-request/1-8.png)

   Em **tipos**, você pode solicitar tipos de cota padrão e spot de um único caso de suporte por meio do suporte a várias seleções.
   
   Para obter mais informações sobre como aumentar os limites de cota Spot, consulte [VMs de ponto do Azure para conjuntos de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   c. Abaixo da lista suspensa **padrão** , selecione a série de SKUs cujas cotas você deseja aumentar.

   ![O painel "detalhes da cota"-série de SKUs](./media/resource-manager-core-quotas-request/1-9.png)

   d. Insira os novos limites de cota que você deseja para esta assinatura. Para remover uma SKU da lista, desmarque a caixa de seleção ao lado da SKU ou selecione o ícone **excluir** (X). 

   ![A caixa de texto "novo limite de vCPU"](./media/resource-manager-core-quotas-request/1-10.png)

1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional na lista suspensa e, em seguida, selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Locais adicionais no painel "detalhes da cota"](./media/resource-manager-core-quotas-request/1-11.png)
   
1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>Solicitar um aumento de cota padrão no painel "assinaturas"

Para solicitar um aumento de cota de vCPU padrão por série de VMs do painel **assinaturas** , faça o seguinte:

> [!NOTE]
> Você também pode solicitar um aumento de limite de cota para várias regiões por meio de um único caso de suporte. Para obter detalhes, consulte a etapa 7.

1. No painel esquerdo da [portal do Azure](https://portal.azure.com), selecione **assinaturas**.

   ![O link "assinaturas"](./media/resource-manager-core-quotas-request/subscriptions.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![O painel "assinaturas"](./media/resource-manager-core-quotas-request/select-subscription.png)

1. No painel esquerdo da página **> nome da assinatura do\<** , selecione **uso + cotas**.

   ![O link "uso + cotas"](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. No canto superior direito, selecione **solicitar aumento**.

   ![Aumento da solicitação](./media/resource-manager-core-quotas-request/request-increase.png)

1. Na lista suspensa **tipo de cota** , selecione o **limite de assinaturas computação-VM (núcleos-vCPUs) aumenta**.

   ![A lista suspensa "tipo de cota"](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
1. No painel de **detalhes de cota** no canto superior direito, faça o seguinte:

   ![O painel "detalhes da cota"](./media/resource-manager-core-quotas-request/1-1-6.png)

   a. Na lista suspensa **modelo de implantação** , selecione o modelo apropriado.

   b. Na lista suspensa **locais** , selecione um local. 
   
   c. Para o local selecionado, em **tipos**, selecione **selecionar um tipo**e, em seguida, marque a caixa de seleção **padrão** .

   ![A caixa de seleção "padrão"](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   Em **tipos**, você pode solicitar tipos de cota padrão e de baixa prioridade de um único caso de suporte por meio do suporte a várias seleções.
   
   Para obter mais informações sobre como aumentar os limites de cota Spot, consulte [VMs de ponto do Azure para conjuntos de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

   d. Abaixo da lista suspensa **padrão** , selecione a série de SKUs cujas cotas você deseja aumentar.

   ![O painel "detalhes da cota"-série de SKUs](./media/resource-manager-core-quotas-request/1-1-8.png)

   e. Insira os novos limites de cota que você deseja para esta assinatura. Para remover uma SKU da lista, desmarque a caixa de seleção ao lado da SKU ou selecione **excluir** (X). 

   ![A caixa de texto "novo limite de vCPU"](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional na lista suspensa e, em seguida, selecione um tipo de VM apropriado. 

   Esta etapa sobrecarrega a série de SKU que você selecionou para os locais anteriores. Insira os limites de cota que você deseja aplicar à série adicional.
   
   ![Locais adicionais no painel "detalhes da cota"](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.
