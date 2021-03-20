---
title: incluir ficheiro
titleSuffix: Azure
description: incluir ficheiro
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3507aacc68de25f7368cbe3cda917077564c56eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96356249"
---
1. Vá aos **grupos de Recursos** e selecione o grupo de recursos selecionado quando criou o recurso **Peering.** Utilize a caixa **Filtro** se tiver demasiados grupos de recursos.

    > [!div class="mx-imgBorder"]
    > ![Grupos de recursos](../media/setup-direct-get-resourcegroup.png)

1. Selecione o recurso **Peering** que criou.

    > [!div class="mx-imgBorder"]
    > ![A página 'Vista Geral' é selecionada no painel esquerdo. Mostra informações sobre o PeeringResourceGroup. Na lista de peering, AshburnPeering é destacado.](../media/setup-direct-get-open.png)

1. A **página 'Visão Geral'** mostra informações de alto nível, como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Painel de visão geral do recurso](../media/setup-direct-get-overview.png)

1. À esquerda, selecione **informações ASN** para ver as informações submetidas quando criou o PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![Informação ASN de recurso de esprevação](../media/setup-direct-get-asninfo.png)

1. À esquerda, selecione **Connections**. No topo do ecrã, vê-se um resumo das ligações entre a ASN e a Microsoft, através de diferentes instalações dentro do metrô. Também pode aceder ao resumo das ligações a partir da página **'Vista Geral',** selecionando **Ligações** no centro do painel, como mostrado.

    > [!div class="mx-imgBorder"]
    > ![Conexões de recursos de espreitar](../media/setup-direct-get-connectionssummary.png)

    * **O Estado de Ligação** corresponde ao estado da configuração da ligação de pares. Os estados apresentados neste campo seguem o diagrama de estado mostrado no [walkthrough de observação direta](../walkthrough-direct-all.md).
    * **O Estado da Sessão do IPv4** e o **Estado da Sessão do IPv6** correspondem aos estados da sessão do IPv4 e do IPv6 BGP, respectivamente. 
    * Quando seleciona uma linha na parte superior do ecrã, a secção **'Ligação'** na parte inferior mostra detalhes para cada ligação. Selecione as setas para expandir **a configuração,** **o endereço IPv4** e **o endereço IPv6**.
