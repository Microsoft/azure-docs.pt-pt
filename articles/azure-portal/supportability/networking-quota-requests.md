---
title: Aumento de limite de rede | Microsoft Docs
description: Aumento do limite de rede
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547810"
---
# <a name="networking-limit-increase"></a>Aumento do limite de rede

Use o [portal do Azure](https://portal.azure.com) para aumentar a cota de rede.

Para exibir a cota e o uso de rede atual no portal do Azure, abra sua assinatura e selecione **usos + cotas**. Você também pode usar as opções a seguir para exibir o uso de rede e os limites.

* [CLI de uso](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [A API de uso de rede](/rest/api/virtualnetwork/virtualnetworks/listusage)

Você pode solicitar um aumento usando **ajuda + suporte** ou em **usos + cotas** no Portal.

> [!Note]
> Para alterar o tamanho padrão de **prefixos de IP público**, selecione **mínimo de comprimento de prefixo de rede IP público** na lista suspensa.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Solicitar aumento de cota de rede no nível de assinatura usando ajuda + suporte

Siga as instruções abaixo para criar uma solicitação de suporte usando **ajuda + suporte** no portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com)e selecione **ajuda + suporte** no menu portal do Azure ou procure e selecione **ajuda + suporte**.

    ![Ajuda + Suporte](./media/networking-quota-request/help-plus-support.png)

1. Selecione **Novo pedido de suporte**.

    ![Novo pedido de suporte](./media/networking-quota-request/new-support-request.png)

1. Para **tipo de problema**, escolha **limites de serviço e assinatura (cotas)** .

    ![Selecionar limites de assinatura do menu suspenso tipo de problema](./media/networking-quota-request/select-quota-issue-type.png)

1. Selecione a subscrição que precisa de uma quota maior.

    ![Selecionar notícias de assinatura](./media/networking-quota-request/select-subscription-support-request.png)

1. Em **tipo de cota**, selecione **rede**. Selecione **Avançar: soluções**.

    ![Selecionar tipo de cota](./media/networking-quota-request/select-quota-type-network.png)

1. Em **detalhes do problema**, selecione **fornecer detalhes** e preencha informações adicionais para ajudar a processar sua solicitação.

    ![Fornecer detalhes](./media/networking-quota-request/provide-details-link.png)

1. No painel de **detalhes de cota** , selecione um modelo de implantação, um local e os recursos a serem incluídos em sua solicitação.

    ![Detalhes da cota DM](./media/networking-quota-request/quota-details-network.png)

1. Insira os novos limites que você deseja na assinatura. Para remover uma linha, desmarque o recurso no menu **recursos** ou selecione o ícone descartar "x". Depois de inserir a cota para cada recurso, selecione **salvar e continuar** para continuar com a criação da solicitação de suporte.

    ![Novos limites](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Solicitar aumento de cota de rede no nível da assinatura usando usos + cotas

Siga estas instruções para criar uma solicitação de suporte usando o **uso + cota** no portal do Azure.

1. Em https://portal.azure.com, procure e selecione **assinaturas**.

    ![Subscrições](./media/networking-quota-request/search-for-suscriptions.png)

1. Selecione a subscrição que precisa de uma quota maior.

    ![Selecionar subscrição](./media/networking-quota-request/select-subscription-change-quota.png)

1. Selecionar **uso + cotas**

    ![Selecionar uso e cotas](./media/networking-quota-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **solicitar aumento**.

    ![Aumento da solicitação](./media/networking-quota-request/request-increase-from-subscription.png)

1. Siga as etapas a partir da etapa 3 em [solicitar aumento de cota de rede no nível da assinatura](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Sobre limites de rede

Para saber mais sobre os limites de rede, confira a [seção rede](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) da página limites ou nossas perguntas frequentes sobre limites de rede.
