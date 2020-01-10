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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775085"
---
1. Vá para **grupos de recursos** e clique no grupo de recursos que você selecionou ao criar o recurso de **emparelhamento** . Você pode usar o campo de *filtro* se tiver muitos grupos de recursos.

    > [!div class="mx-imgBorder"]
    > ![grupo de recursos de emparelhamento](../media/setup-direct-get-resourcegroup.png)

1. Clique no recurso de **emparelhamento** que você criou.

    > [!div class="mx-imgBorder"]
    > ![exibição de recurso de emparelhamento](../media/setup-direct-get-open.png)

1. A página **visão geral** mostra informações de alto nível. Observe as informações realçadas abaixo.

    > [!div class="mx-imgBorder"]
    > ![exibição de recurso de emparelhamento](../media/setup-direct-get-overview.png)

1. À esquerda, clique em **informações de ASN** para exibir as informações enviadas ao criar PeerAsn

    > [!div class="mx-imgBorder"]
    > ![exibição de recurso de emparelhamento](../media/setup-direct-get-asninfo.png)

1. À esquerda, clique em **conexões**. Observe na parte superior um resumo das conexões de emparelhamento entre o ASN e a Microsoft, em diferentes instalações dentro do metro. Você também pode chegar ao Resumo de conexões da página **visão geral** , clicando em **conexões** no painel central, conforme realçado acima.

    > [!div class="mx-imgBorder"]
    > ![exibição de recurso de emparelhamento](../media/setup-direct-get-connectionssummary.png)

    * O **estado da conexão** corresponde ao estado da configuração da conexão de emparelhamento. Os Estados exibidos neste campo seguem o diagrama de estado mostrado em [passo a passos de emparelhamento direto](../walkthrough-direct-all.md)
    * O **estado da sessão IPv4** e o **estado da sessão IPv6** correspondem aos Estados da sessão BGP IPv4 e IPv6, respectivamente.  
    * Quando você seleciona uma linha na parte superior, a seção de ***conexão*** na parte inferior mostra detalhes de cada conexão. Você pode clicar nas marcas de seta para expandir a ***configuração***de subseções, o ***endereço IPv4*** e o ***endereço IPv6***
