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
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678518"
---
1. Selecione **Criar um recurso** > **Ver tudo**.

    > [!div class="mx-imgBorder"]
    > ![Pesquisar Peering](../media/setup-seeall.png)

1. Procure **o Peering** na caixa de pesquisa e selecione **Enter** no teclado. A partir dos resultados, selecione um recurso **Peering.**

    > [!div class="mx-imgBorder"]
    > ![Peering de lançamento](../media/setup-launch.png)

1. Depois de **Peering** começar, reveja a página para entender os detalhes. Quando estiver pronto, selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Criar peering](../media/setup-create.png)

1. Na página **Criar uma página de Peering,** no separador **Basics,** preencha as caixas como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Peering Basics tab](../media/setup-basics-tab.png)

    * Selecione a sua **Subscrição**Azure .
    * Para o **grupo Recursos,** pode escolher um grupo de recursos existente da lista de drop-down ou criar um novo grupo selecionando **criar novos**. Vamos criar um novo grupo de recursos para este exemplo.
    * **O nome** corresponde ao nome do recurso e pode ser tudo o que escolher.
    * **A região** é selecionada automaticamente se escolher um grupo de recursos existente. Se escolheu criar um novo grupo de recursos, também precisa escolher a região do Azure onde pretende que o recurso resida.

        > [!NOTE]
        > A região onde reside um grupo de recursos é independente do local onde pretende criar o peering com a Microsoft. Mas é uma boa prática organizar os seus recursos de observação dentro de grupos de recursos que residem nas regiões mais próximas de Azure. Por exemplo, para os olhinhos em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou LESTE US2.

    * Selecione o seu ASN na caixa **Peer ASN.**

        > [!IMPORTANT]
        > * Só pode escolher um ASN com Validação state como Aprovado antes de submeter um pedido de peering. Se acabou de apresentar o seu pedido de PeerAsn, aguarde cerca de 12 horas para que a associação ASN seja aprovada. Se o ASN que selecionar estiver pendente de validação, verá uma mensagem de erro. 
        > * Se não vir o ASN que precisa de escolher, verifique se selecionou a subscrição correta. Em caso afirmativo, verifique se já criou o PeerAsn utilizando a [assinatura Associate Peer ASN para a assinatura Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Peering Basics preenchido](../media/setup-direct-basics-filled-tab.png)

    * Selecione **Seguinte : Configuração >** para continuar.
