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
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774643"
---
1. Clique em **criar um recurso** > **ver tudo**.

    > [!div class="mx-imgBorder"]
    > ](../media/setup-seeall.png) de emparelhamento de pesquisa ![

1. Pesquise *emparelhamento* na caixa de pesquisa e pressione *Enter* no teclado. Nos resultados, clique em recurso de **emparelhamento** .

    > [!div class="mx-imgBorder"]
    > ![iniciar o emparelhamento](../media/setup-launch.png)

1. Quando o **emparelhamento** for iniciado, examine a página para entender os detalhes. Quando estiver pronto, clique em **criar**.

    > [!div class="mx-imgBorder"]
    > ![criar emparelhamento](../media/setup-create.png)

1. Na página **criar um emparelhamento** , na guia **conceitos básicos** , preencha os campos conforme mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > Noções básicas de emparelhamento ![](../media/setup-basics-tab.png)

    * Escolha sua **assinatura**do Azure.
    * Para o **grupo de recursos**, você pode escolher um grupo de recursos existente na lista suspensa ou criar um novo grupo clicando em **criar novo**. Criaremos um novo grupo de recursos para este exemplo.
    * **Nome** corresponde ao nome do recurso e pode ser qualquer coisa que você escolher.
    * A **região** será selecionada automaticamente se você escolher um grupo de recursos existente na etapa anterior. Se você optar por criar um novo grupo de recursos, também precisará escolher a região do Azure onde deseja que o recurso resida. Este dos E.U.A.

        > [!NOTE]
        > A região onde o grupo de recursos reside é independente do local onde você deseja criar o emparelhamento com a Microsoft. Mas é uma prática recomendada organizar seus recursos de emparelhamento em grupos de recursos que residem nas regiões do Azure mais próximas. Por exemplo: para emparelhamentos no Ashburn, você pode criar um grupo de recursos no *leste dos EUA* ou no *leste dos EUA 2*

    * Escolha seu ASN no campo **ASN par** .

        > [!IMPORTANT]
        > * Você só pode escolher um ASN com ValidationState como "aprovado" antes de enviar uma solicitação de emparelhamento. Se você acabou de enviar sua solicitação PeerAsn, aguarde 12 horas ou para que a associação ASN seja "aprovada". Se o ASN selecionado tiver uma validação pendente, você verá uma mensagem de erro. 
        > * Se você não vir o ASN que precisa escolher, verifique se você selecionou a assinatura correta. Nesse caso, verifique se você já criou o PeerAsn usando [associar o ASN do peer à assinatura do Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > Noções básicas de emparelhamento ![preenchidas](../media/setup-direct-basics-filled-tab.png)

    * Clique em **Avançar: configuração >** para continuar.
