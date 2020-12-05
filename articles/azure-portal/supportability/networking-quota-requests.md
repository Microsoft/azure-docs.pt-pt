---
title: Aumento do limite de rede Microsoft Docs
description: Aumento do limite de rede
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: acb05da0255445de31e08f2724dcb484a3e05b17
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "84764270"
---
# <a name="networking-limit-increase"></a>Aumento do limite de rede

Utilize o [portal Azure](https://portal.azure.com) para aumentar a sua quota de networking.

Para ver a sua utilização e quota de rede atuais no portal Azure, abra a sua subscrição e, em seguida, selecione **Usages + quotas**. Também pode utilizar as seguintes opções para visualizar a utilização da sua rede e os limites.

* [Uso CLI](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [A API de utilização da rede](/rest/api/virtualnetwork/virtualnetworks/listusage)

Pode solicitar um aumento utilizando **o suporte Help +** ou em **Utilizações + quotas** no portal.

> [!Note]
> Para alterar o tamanho **prefixo por prefixos IP públicos,** selecione **Min Public IP InterNetwork Prefix Length** da lista de dropdown.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Pedido Aumento da quota de rede a nível de subscrição usando o suporte Help +

Siga as instruções abaixo para criar um pedido de apoio utilizando o **suporte Help +** no portal Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, selecione **Ajuda + suporte** a partir do menu do portal Azure ou procure e selecione Ajuda + **suporte**.

    ![Ajuda + Suporte](./media/networking-quota-request/help-plus-support.png)

1. Selecione **Novo pedido de suporte**.

    ![Novo pedido de suporte](./media/networking-quota-request/new-support-request.png)

1. Para **o tipo de emissão**, escolha **limites de serviço e de subscrição (quotas)**.

    ![Selecione limites de subscrição a partir do dropdown do tipo de emissão](./media/networking-quota-request/select-quota-issue-type.png)

1. Selecione a subscrição que precisa de uma quota maior.

    ![Selecione nova subscrição SR](./media/networking-quota-request/select-subscription-support-request.png)

1. Sob **o tipo Quota**, selecione **Networking**. Selecione **Seguinte: Soluções**.

    ![Selecione tipo de quota](./media/networking-quota-request/select-quota-type-network.png)

1. Em **DETALHES DE PROBLEMAs,** selecione **Fornecer detalhes** e preencha informações adicionais para ajudar a processar o seu pedido.

    ![Fornecer detalhes](./media/networking-quota-request/provide-details-link.png)

1. No painel **de detalhes da Quota,** selecione um modelo de implantação, uma localização e os recursos para incluir no seu pedido.

    ![Detalhes da quota DM](./media/networking-quota-request/quota-details-network.png)

1. Introduza os novos limites que gostaria na subscrição. Para remover uma linha, desescolh o recurso do menu **Recursos** ou selecione o ícone "x" de devoluções. Depois de introduzir a quota para cada recurso, **selecione Save e continue** com a criação do pedido de apoio.

    ![Novos Limites](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Pedido Aumento do contingente de rede a nível de subscrição utilizando Utilizações + quotas

Siga estas instruções para criar um pedido de apoio utilizando **a quota Usage +** no portal Azure.

1. A partir https://portal.azure.com de, pesquisar e selecionar **Subscrições.**

    ![Subscrições](./media/networking-quota-request/search-for-suscriptions.png)

1. Selecione a subscrição que precisa de uma quota maior.

    ![Selecionar subscrição](./media/networking-quota-request/select-subscription-change-quota.png)

1. Selecione **Usage + quotas**

    ![Selecione a utilização e as quotas](./media/networking-quota-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **Pedir aumento**.

    ![Aumento do pedido](./media/networking-quota-request/request-increase-from-subscription.png)

1. Siga os passos a partir do passo 3 no [aumento da quota de rede de pedidos ao nível da subscrição.](#request-networking-quota-increase-at-subscription-level-using-help--support)

## <a name="about-networking-limits"></a>Sobre os limites de networking

Para saber mais sobre os limites de rede, consulte [a secção](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) de Rede da página limites ou as nossas FAQ limites de rede.
