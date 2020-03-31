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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774643"
---
1. Clique **em Criar um recurso** > **Ver tudo.**

    > [!div class="mx-imgBorder"]
    > ![Pesquisar Peering](../media/setup-seeall.png)

1. Procure *o Peering* na caixa de pesquisa e bata *enter* no seu teclado. A partir dos resultados, clique no recurso **Peering.**

    > [!div class="mx-imgBorder"]
    > ![Peering de lançamento](../media/setup-launch.png)

1. Assim que o **Peering** for lançado, reveja a página para compreender os detalhes. Quando estiver pronto, clique em **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Criar peering](../media/setup-create.png)

1. Na página **Criar uma página de Peering,** sob o separador **Basics,** preencha os campos como mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![Peering Basics](../media/setup-basics-tab.png)

    * Escolha a sua **Assinatura**Azure.
    * Para **o grupo Recursos,** pode escolher um grupo de recursos existente a partir do drop-down ou criar um novo grupo clicando **em Criar novo**. Criaremos um novo grupo de recursos para este exemplo.
    * **O nome** corresponde ao nome do recurso e pode ser tudo o que escolher.
    * **A região** é selecionada automaticamente se escolher um grupo de recursos existente no degrau acima. Se escolheu criar um novo grupo de recursos, então também precisa escolher a região azure onde quer que o recurso resida. E.U.A. Leste

        > [!NOTE]
        > Região onde o grupo de recursos reside é independente do local onde pretende criar o peering com a Microsoft. Mas é uma boa prática organizar os seus recursos de observação dentro de grupos de recursos que residem nas regiões mais próximas do Azure. Por exemplo: para espreitar em Ashburn, você pode criar um grupo de recursos no *Leste dos EUA* ou LESTE *US2*

    * Escolha o seu ASN no campo **Peer ASN.**

        > [!IMPORTANT]
        > * Só pode escolher um ASN com Validação state como "Aprovado" antes de submeter um pedido de peering. Se acabou de apresentar o seu pedido de PeerAsn, aguarde cerca de 12 horas para que a associação ASN seja "Aprovada". Se o ASN selecionar estiver pendente de validação, verá uma mensagem de erro. 
        > * Se não vir a ASN, verifique se selecionou a subscrição correta. Em caso afirmativo, verifique se já criou o PeerAsn usando [o Associate Peer ASN para a Assinatura Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Peering Basics preenchido](../media/setup-direct-basics-filled-tab.png)

    * Clique no **Seguinte: Configuração >** para continuar.
