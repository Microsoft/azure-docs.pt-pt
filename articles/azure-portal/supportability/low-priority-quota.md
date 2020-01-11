---
title: Identificar a cota da máquina virtual | Microsoft Docs
description: Aumentar os limites de cota, fazendo solicitações de cota Spot
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9567df95e4e66d9c6b82b29cb9bba9adaf39349d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897302"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Cota de spot: aumentar os limites para todas as séries de VM

As máquinas virtuais (VMs) spot fornecem um modelo diferente de uso do Azure. Eles permitem que você assuma custos menores no Exchange para permitir que o Azure remova VMs conforme necessário para implantações de instância de VM pagas conforme o uso ou reservadas. Para obter mais informações sobre VMs pontuais, consulte [VMs de ponto do Azure para conjuntos de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

O Azure Resource Manager dá suporte a dois tipos de cotas do vCPU para máquinas virtuais:
* *As VMs pagas conforme o uso* e as *instâncias de VM reservadas* estão sujeitas a uma *cota de vCPU padrão*.
* As *VMs pontuais* estão sujeitas a uma *cota de vCPU Spot*. 

Para o tipo de *cota vCPU Spot* , as cotas de vCPU do Resource Manager são impostas em toda a série de VMs disponíveis como um único limite regional.

Sempre que você implantar uma nova VM Spot, o uso total de vCPU novo e existente para todas as instâncias de VM Spot não deverá exceder o limite de cota vCPU Spot aprovado. Se a cota de spot for excedida, a implantação da VM Spot não será permitida. 

Este artigo discute como solicitar um aumento no limite de cota vCPU Spot usando o portal do Azure. 

Para saber mais sobre as cotas de vCPU padrão, consulte [cotas de vCPU de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) e [limites de serviço e assinatura do Azure](https://aka.ms/quotalimits). 

Para saber mais sobre como aumentar o limite de vCPU por região, confira [cota padrão: aumento de limite de vCPU regional](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

## <a name="request-a-quota-limit-increase-from-the-help--support-pane"></a>Solicitar um aumento de limite de cota do painel "ajuda + suporte" 

Para solicitar um aumento de limite de cota de spot para todas as séries de VM do painel **ajuda + suporte** , faça o seguinte:

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

1. Selecione a guia **detalhes** e, em **detalhes do problema**, selecione **fornecer detalhes**e insira informações adicionais para ajudar a processar sua solicitação.

   ![O link "fornecer detalhes"](./media/resource-manager-core-quotas-request/provide-details.png)
   
1. No painel de **detalhes de cota** no canto superior direito, faça o seguinte:

   ![O painel "detalhes da cota"](./media/resource-manager-core-quotas-request/3-7.png)

   a. Na lista suspensa **modelo de implantação** , selecione o modelo apropriado.

   b. Na lista suspensa **locais** , selecione um local. Para o local selecionado, em **tipos**, na caixa **selecionar um tipo** , digite **Spot**. 
   
   ![A guia detalhes da "nova solicitação de suporte"](./media/resource-manager-core-quotas-request/3-8.png)

    Em **tipos**, você pode solicitar tipos de cota padrão e spot de um único caso de suporte por meio do suporte a várias seleções. 
    
    Para obter mais informações, consulte [cota padrão: aumento de limite de vCPU de série por VM](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   c. Insira o novo limite de cota que você deseja para esta assinatura. 
 
   ![A caixa de texto "novo limite de vCPU"](./media/resource-manager-core-quotas-request/3-9.png)

1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional na lista suspensa e, em seguida, selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Locais adicionais no painel "detalhes da cota"](./media/resource-manager-core-quotas-request/3-10.png)

1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Solicitar um aumento de limite de cota no painel "assinaturas"

Para solicitar um aumento de limite de cota de spot para todas as séries de VM do painel **assinaturas** , faça o seguinte:

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

   ![O painel "detalhes da cota"](./media/resource-manager-core-quotas-request/3-2-6.png)
 
   a. Na lista suspensa **modelo de implantação** , selecione o modelo apropriado.

   b. Na lista suspensa **locais** , selecione um local. 
   
   c. Para o local selecionado, em **tipos**, na caixa **selecionar um tipo** , digite **Spot**.

   ![O painel "detalhes da cota"](./media/resource-manager-core-quotas-request/3-2-7.png)

   Para obter mais informações, consulte [cota padrão: aumento de limite de vCPU de série por VM](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

   d. Insira o novo limite de cota que você deseja para esta assinatura.

   ![A caixa de texto "novo limite de vCPU"](./media/resource-manager-core-quotas-request/3-2-8.png)
 
1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional na lista suspensa e, em seguida, selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Locais adicionais no painel "detalhes da cota"](./media/resource-manager-core-quotas-request/3-2-9.png)
 
1. Selecione **salvar e continuar** para continuar criando a solicitação de suporte.


