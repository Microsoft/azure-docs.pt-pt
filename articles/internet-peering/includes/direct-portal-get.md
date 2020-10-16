---
title: ficheiro de inclusão
titleSuffix: Azure
description: ficheiro de inclusão
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678828"
---
1. Vá aos **grupos de Recursos**e selecione o grupo de recursos selecionado quando criou o recurso **Peering.** Utilize a caixa **Filtro** se tiver demasiados grupos de recursos.

    > [!div class="mx-imgBorder"]
    > ![Grupos de recursos](../media/setup-direct-get-resourcegroup.png)

1. Selecione o recurso **Peering** que criou.

    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de peering](../media/setup-direct-get-open.png)

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
    * Quando seleciona uma linha na parte superior do ecrã, a secção **'Ligação'** na parte inferior mostra detalhes para cada ligação. Selecione as setas para expandir **a configuração,** **o endereço IPv4**e **o endereço IPv6**.
