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
ms.openlocfilehash: 2e8938f270ed175c687d975b0b248275ad92f8e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75775085"
---
1. Vá aos **grupos de Recursos** e clique no grupo de recursos que selecionou enquanto cria o recurso **Peering.** Pode utilizar o campo *Filter* se tiver demasiados grupos de recursos.

    > [!div class="mx-imgBorder"]
    > ![Grupo de recursos de observação](../media/setup-direct-get-resourcegroup.png)

1. Clique no recurso **Peering** que criou.

    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de observação de recursos](../media/setup-direct-get-open.png)

1. A página **de visão geral** mostra informações de alto nível. Observe a informação realçada abaixo.

    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de observação de recursos](../media/setup-direct-get-overview.png)

1. À esquerda, clique em **informações asnique** para visualizar informações submetidas enquanto cria O PeerAsn

    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de observação de recursos](../media/setup-direct-get-asninfo.png)

1. À esquerda, clique em **Ligações**. Observe no topo um resumo de ligações de observação entre a sua ASN e a Microsoft, através de diferentes instalações dentro do metrô. Também pode chegar ao resumo das ligações a partir da página **Overview,** clicando em **Ligações** no painel central como acima salientado.

    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de observação de recursos](../media/setup-direct-get-connectionssummary.png)

    * O Estado de **Ligação** corresponde ao estado da ligação de pares configurada. Os estados exibidos neste campo seguem o diagrama de estado mostrado em [direct peering walkthrough](../walkthrough-direct-all.md)
    * **IPv4 Session State** e **IPv6 Session State** correspondem aos estados de sessão iPv4 e IPv6 BGP respectivamente.  
    * Quando selecionar uma linha na parte superior, a secção ***de Ligação*** na parte inferior mostra detalhes para cada ligação. Pode clicar em marcas de seta para expandir a ***configuração***das sub-secções, ***o endereço IPv4*** e o ***endereço IPv6***
