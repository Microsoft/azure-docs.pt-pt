---
title: Aumento do limite de networking Microsoft Docs
description: Aumento do limite de rede
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547810"
---
# <a name="networking-limit-increase"></a>Aumento do limite de rede

Utilize o [portal Azure](https://portal.azure.com) para aumentar a sua quota de rede.

Para ver o seu uso atual em Rede e quota no portal Azure, abra a sua subscrição e, em seguida, selecione **Utilizações + quotas**. Também pode utilizar as seguintes opções para visualizar o uso e limites da sua rede.

* [ClI de utilização](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [A API de utilização da rede](/rest/api/virtualnetwork/virtualnetworks/listusage)

Pode solicitar um aumento utilizando **ajuda + suporte** ou em **Utilizações + quotas** no portal.

> [!Note]
> Para alterar o tamanho padrão dos **Prefixos IP públicos,** selecione **Min Public IP InterNetwork Prefix Length** da lista de dropdown.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Solicitar aumento da quota de networking ao nível de subscrição usando ajuda + suporte

Siga as instruções abaixo para criar um pedido de apoio utilizando **ajuda + suporte** no portal Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, selecione **Ajuda + suporte** do menu do portal Azure ou procure e selecione Ajuda + **suporte**.

    ![Ajuda + Suporte](./media/networking-quota-request/help-plus-support.png)

1. Selecione **Novo pedido de suporte**.

    ![Novo pedido de suporte](./media/networking-quota-request/new-support-request.png)

1. Para **o tipo de emissão,** escolha limites de serviço e **subscrição (quotas)**.

    ![Selecione limites de subscrição a partir de dropdown tipo de edição](./media/networking-quota-request/select-quota-issue-type.png)

1. Selecione a subscrição que precisa de uma quota maior.

    ![Selecione newSR de subscrição](./media/networking-quota-request/select-subscription-support-request.png)

1. Sob o **tipo de quota,** selecione **Networking**. Selecione **Seguinte: Soluções**.

    ![Selecionar tipo de quota](./media/networking-quota-request/select-quota-type-network.png)

1. Em **DETALHES DE PROBLEMAS,** selecione **Fornecer detalhes** e preencher informações adicionais para ajudar a processar o seu pedido.

    ![Fornecer detalhes](./media/networking-quota-request/provide-details-link.png)

1. No painel de detalhes da **Quota,** selecione um modelo de implementação, uma localização e os recursos a incluir no seu pedido.

    ![Detalhes da quota DM](./media/networking-quota-request/quota-details-network.png)

1. Introduza os novos limites que deseja na subscrição. Para remover uma linha, desmarque o recurso do menu **Recursos** ou selecione o ícone "x" de devoluções. Depois de introduzir a quota para cada recurso, selecione **Guardar e continuar** com a criação do pedido de apoio.

    ![Novos Limites](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Solicitar aumento da quota de networking ao nível da subscrição utilizando utilizações + quotas

Siga estas instruções para criar um pedido de suporte utilizando a utilização de **utilização + quota** no portal Azure.

1. A https://portal.azure.compartir de , procure e selecione **Assinaturas**.

    ![Subscrições](./media/networking-quota-request/search-for-suscriptions.png)

1. Selecione a subscrição que precisa de uma quota maior.

    ![Selecionar subscrição](./media/networking-quota-request/select-subscription-change-quota.png)

1. **Selecione Utilização + quotas**

    ![Selecione utilização e quotas](./media/networking-quota-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **Pedido aumentar**.

    ![Aumento de pedidos](./media/networking-quota-request/request-increase-from-subscription.png)

1. Siga os passos a partir do passo 3 da quota de [rede de pedidos aumenta ao nível da subscrição](#request-networking-quota-increase-at-subscription-level-using-help--support).

## <a name="about-networking-limits"></a>Sobre os limites de networking

Para saber mais sobre os limites de Networking, consulte a [secção de Networking](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) da página de limites ou as nossas FAQ de Limites de Rede.
