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
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678828"
---
1. Vá aos **grupos de Recursos**e selecione o grupo de recursos selecionado quando criou o recurso **Peering.** Utilize a caixa **de filtro** se tiver demasiados grupos de recursos.

    > [!div class="mx-imgBorder"]
    > ![Grupos de recursos](../media/setup-direct-get-resourcegroup.png)

1. Selecione o recurso **Peering** que criou.

    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de observação de recursos](../media/setup-direct-get-open.png)

1. A página **de visão geral** mostra informações de alto nível, como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Painel de visão geral do recurso de observação](../media/setup-direct-get-overview.png)

1. À esquerda, selecione **informações ASN** para visualizar as informações submetidas quando criou o PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![Peering recursos ASN informação](../media/setup-direct-get-asninfo.png)

1. À esquerda, selecione **Ligações**. No topo do ecrã, você vê um resumo de ligações de observação entre a sua ASN e microsoft, em diferentes instalações dentro do metro. Também pode aceder ao resumo das ligações a partir da página **'Visão Geral',** selecionando **Ligações** no centro do painel, como mostrado.

    > [!div class="mx-imgBorder"]
    > ![Peering conexões de recursos](../media/setup-direct-get-connectionssummary.png)

    * O Estado de **Ligação** corresponde ao estado da configuração da ligação de pares. Os estados apresentados neste campo seguem o diagrama de estado mostrado na passagem direta de [observação](../walkthrough-direct-all.md).
    * **O IPv4 Session State** e **o IPv6 Session State** correspondem aos estados da sessão iPv4 e IPv6 BGP, respectivamente. 
    * Quando seleciona uma linha na parte superior do ecrã, a secção **de Ligação** na parte inferior mostra detalhes para cada ligação. Selecione as setas para expandir **configuração,** **endereço IPv4**e **endereço IPv6**.
