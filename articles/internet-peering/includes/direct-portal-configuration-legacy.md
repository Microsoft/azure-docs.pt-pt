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
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775072"
---
1. Na página **criar um emparelhamento** , na guia **configuração** , preencha os campos, conforme mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > Configuração de emparelhamento ![-](../media/setup-direct-conf-tab.png) direta

    * Para **tipo de emparelhamento**, selecione *direto*.
    * Para **Microsoft Network**, escolha *AS8075*. Não selecione ASN 8069. Ele é reservado para aplicativos especiais e usado apenas pelo [emparelhamento da Microsoft](mailto:peering@microsoft.com).
    * Selecione **SKU** como *básico gratuito*. Não selecione *Premium gratuitamente* , pois ele está reservado para aplicativos especiais.
    * Escolha o local do **Metro** para o qual você deseja converter o emparelhamento no recurso do Azure. Se você tiver conexões de emparelhamento com a Microsoft no local **Metro** selecionado que não são convertidos no recurso do Azure, essas conexões serão listadas na seção **conexões de emparelhamento** , conforme mostrado abaixo. Agora você pode converter essas conexões de emparelhamento para o recurso do Azure.

        > [!div class="mx-imgBorder"]
        > Configuração de emparelhamento ![-conexões herdadas diretas](../media/setup-directlegacy-conf-tab.png)

1. Se você precisar atualizar a largura de banda, clique no botão Editar para uma linha, conforme realçado abaixo, para modificar as configurações de conexão.

    > [!div class="mx-imgBorder"]
    > Configuração de emparelhamento ![-edição direta](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Se você quiser adicionar conexões de emparelhamento adicionais com a Microsoft no local **Metro** selecionado, você pode fazer isso clicando em **criar novo** botão. Consulte [criar ou modificar um emparelhamento direto usando o portal](../howto-direct-portal.md) para obter mais informações.
    >

1. Clique em **examinar + criar**. Observe que o portal executa a validação básica das informações inseridas. Isso é exibido em uma faixa de forma na parte superior, como *executando a validação final...* .

    > [!div class="mx-imgBorder"]
    > Guia validação de emparelhamento ![](../media/setup-direct-review-tab-validation.png)

1. Depois que ele se transformar em *validação aprovada*, verifique suas informações e envie a solicitação clicando em **criar**. Se você precisar modificar sua solicitação, clique em **anterior** e repita as etapas acima.

    > [!div class="mx-imgBorder"]
    > ](../media/setup-direct-review-tab-submit.png) de envio de emparelhamento de ![

1. Depois de enviar a solicitação, aguarde até que ela conclua a implantação. Se a implantação falhar, entre em contato com [o emparelhamento da Microsoft](mailto:peering@microsoft.com). Uma implantação bem-sucedida será exibida como a seguir.

    > [!div class="mx-imgBorder"]
    > ![êxito de emparelhamento](../media/setup-direct-success.png)
