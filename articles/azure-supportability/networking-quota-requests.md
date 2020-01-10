---
title: Aumento de limite de rede | Microsoft Docs
description: Aumento do limite de rede
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c4351afe7781f4340dfd943828d65e94efbdc0a7
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659309"
---
# <a name="networking-limit-increase"></a>Aumento do limite de rede

Para exibir a cota e o uso de rede atual, você pode visitar a folha **usos + cota** no portal do Azure. Você também pode usar a [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)de uso, o [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) ou a [API de uso de rede](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) para exibir o uso da rede e os limites.

Você pode solicitar um aumento via folha **ajuda + suporte** ou a folha **usos + cota** no Portal.

> [!Note]
> Para alterar o tamanho padrão de para o prefixo de IP público, selecione "comprimento mínimo de prefixo de InterNet do IP público" na lista suspensa.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Solicitar aumento de cota de rede no nível de assinatura usando a folha **ajuda + suporte**

Siga as instruções abaixo para criar uma solicitação de suporte por meio da folha ' ajuda + suporte ' do Azure disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **ajuda + suporte**.

    ![Ajuda + Suporte](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Selecione **Novo pedido de suporte**. 

    ![Novo pedido de suporte](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. Na lista suspensa tipo de problema, escolha **limites de serviço e assinatura (cotas)** .

    ![Lista suspensa tipo de problema](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Selecione a subscrição que precisa de uma quota maior.

    ![Selecionar notícias de assinatura](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Selecione **rede** na lista suspensa **tipo de cota** . 

    ![Selecionar tipo de cota](./media/networking-quota-request/select-quota-type-network.png)

6. Em **detalhes do problema**, forneça informações adicionais para ajudar a processar sua solicitação clicando em **fornecer detalhes**.

    ![Fornecer detalhes](./media/resource-manager-core-quotas-request/provide-details.png)

7. No painel **detalhes da cota** , selecione modelo de implantação, um local e os recursos para os quais você deseja solicitar um aumento.

    ![Detalhes da cota DM](./media/networking-quota-request/quota-details-network.png)

8.  Insira os novos limites que você deseja na assinatura. Para remover uma linha, desmarque o recurso do menu suspenso de recursos ou clique no ícone descartar "x". Depois de inserir a cota desejada para cada recurso, clique em **salvar e continue** no painel detalhes da cota para continuar com a criação da solicitação de suporte.

    ![Novos limites](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Solicitar aumento de cota de rede no nível da assinatura usando **usos +** folha de cota

Siga as instruções abaixo usando para criar uma solicitação de suporte por meio da folha ' uso + cota ' do Azure disponível no portal do Azure. 

1. Em https://portal.azure.com, selecione **assinaturas**.

    ![Subscrições](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Selecione a subscrição que precisa de uma quota maior.

    ![Selecionar subscrição](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Selecionar **uso + cotas**

    ![Selecionar uso e cotas](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. No canto superior direito, selecione **solicitar aumento**.

    ![Aumento da solicitação](./media/resource-manager-core-quotas-request/request-increase.png)

5. Siga as etapas a partir da etapa 3 na seção *solicitar aumento de cota de rede na assinatura* usando a seção da folha **ajuda + suporte**

## <a name="about-networking-limits"></a>Sobre limites de rede

Para saber mais sobre os limites de rede, consulte a [seção rede](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) da página limites ou nossas perguntas frequentes sobre limites de rede
