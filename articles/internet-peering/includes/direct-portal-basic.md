---
title: ficheiro de inclusão
titleSuffix: Azure
description: ficheiro de inclusão
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: e2804c8f9b1af89ac0ea86ec14136df66d900060
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83846179"
---
1. **Selecione Criar um recurso**Ver  >  **tudo.**

    > [!div class="mx-imgBorder"]
    > ![Pesquisar Peering](../media/setup-seeall.png)

1. Procure **por Peering** na caixa de pesquisa e selecione **Enter** no seu teclado. A partir dos resultados, selecione um recurso **Peering.**

    > [!div class="mx-imgBorder"]
    > ![Iniciar Peering](../media/setup-launch.png)

1. Depois de **Peering** começar, reveja a página para entender os detalhes. Quando estiver pronto, selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Criar peering](../media/setup-create.png)

1. Na página **'Criar um Peering',** no separador **Básicos,** preencha as caixas como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Separador básico de peering](../media/setup-basics-tab.png)

    * Selecione a sua **Assinatura Azure**.
    * Para **o grupo de recursos,** pode escolher um grupo de recursos existente da lista de drop-down ou criar um novo grupo selecionando Criar **novos**. Vamos criar um novo grupo de recursos para este exemplo.
    * **O nome** corresponde ao nome do recurso e pode ser o que escolher.
    * **A região** é autoeleita se escolher um grupo de recursos existente. Se escolheu criar um novo grupo de recursos, também precisa escolher a região de Azure onde deseja que o recurso resida.

        > [!NOTE]
        > A região onde reside um grupo de recursos é independente do local onde pretende criar olhando com a Microsoft. Mas é uma boa prática organizar os seus recursos de observação dentro de grupos de recursos que residem nas regiões mais próximas do Azure. Por exemplo, para espreitar em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou no Leste dos EUA.

    * Selecione o seu ASN na caixa **Peer ASN.**

        > [!IMPORTANT]
        > * Só pode escolher uma ASN com Validação Estado como Aprovado antes de submeter um pedido de espreitar. Se acabou de submeter o seu pedido peerAsn, aguarde cerca de 12 horas para que a associação ASN seja aprovada. Se a ASN selecionada estiver pendente de validação, verá uma mensagem de erro. 
        > * Se não vir a ASN que precisa de escolher, verifique se selecionou a subscrição correta. Em caso afirmativo, verifique se já criou o PeerAsn utilizando [a assinatura Associate Peer ASN para a Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Básicos de peering preenchidos](../media/setup-direct-basics-filled-tab.png)

    * Selecione **Seguinte : Configuração >** para continuar.
