---
title: Aumento de limite de rede | Documentos da Microsoft
description: Aumento de limite de rede
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297346"
---
# <a name="networking-limit-increase"></a>Aumento de limite de rede

Para ver a sua utilização do sistema de rede e a quota atual, pode visitar o **utilizações + Quota** painel no portal do Azure. Também pode utilizar o uso [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) ou o [API de utilização de rede](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) para ver a utilização de rede e os limites.

Pode pedir um aumento em **ajuda + suporte** painel ou o **utilizações + Quota** painel no portal.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Pedir aumento de quota de rede através de nível de subscrição do **ajuda + suporte** painel

Siga as instruções abaixo para criar um pedido de suporte através "Ajuda + suporte" painel do Azure disponível no portal do Azure. 

1. Partir https://portal.azure.com, selecione **ajuda + suporte**.

    ![Ajuda + suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecione **Novo pedido de suporte**. 

    ![Novo pedido de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Na lista pendente Tipo de problema, escolha **limites de serviço e subscrição (cotas)** .

    ![Lista pendente Tipo de problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecione a subscrição que precisa de uma quota maior.

    ![Selecione a subscrição newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecione **Networking** na **tipo de quota** lista pendente. 

    ![Selecione o tipo de quota](./media/networking-quota-request/select-quota-type-network.png)

6. Na **detalhes do problema**, forneça informações adicionais para ajudar o processo de seu pedido clicando **fornecem detalhes**.

    ![Forneça detalhes](./media/resource-manager-core-quotas-request/provide-details.png)

7. Na **detalhes da Quota** painel, selecione o modelo, uma localização e os recursos que pretende pedir um aumento para implementação.

    ![Detalhes da quota DM](./media/networking-quota-request/quota-details-network.png)

8.  Introduza nos novos limites de que pretende na subscrição. Para remover uma linha, desmarque o recurso no menu pendente do recurso ou clique no ícone de rejeição "x". Depois de introduzir a quota pretendida para cada recurso, clique em **guardar e continuar** no painel de detalhes de Quota para continuar com a criação do pedido de suporte.

    ![Novos limites](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Pedir aumento de quota de rede através de nível de subscrição **utilizações + Quota** painel

Siga as instruções abaixo para criar um pedido de suporte através do Azure "utilização + quotas" painel disponível no portal do Azure. 

1. Partir https://portal.azure.com, selecione **subscrições**.

    ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecione a subscrição que precisa de uma quota maior.

    ![Selecionar subscrição](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selecione **utilização + quotas**

    ![Selecione a utilização e quotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. No canto superior direito, selecione **pedir aumento**.

    ![Pedir aumento](./media/resource-manager-core-quotas-request/request-increase.png)

5. Siga os passos a partir do passo 3 do *pedido de rede aumento de quota ao nível da subscrição* secção com o **ajuda + suporte** seção do painel

## <a name="about-networking-limits"></a>Sobre limites de rede

Para obter mais informações sobre limites de rede, consulte a [secção do funcionamento em rede](../azure-subscription-service-limits.md#networking-limits) de nossas perguntas frequentes de limites de rede ou a página de limites