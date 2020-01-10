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
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775280"
---
1. Na página **criar um emparelhamento** , na guia **configuração** , preencha os campos, conforme mostrado abaixo.

    * Para **tipo de emparelhamento**, selecione *direto*.
    * Para **Microsoft Network**, escolha *AS8075*. Não crie emparelhamento com o ASN 8069. Ele é reservado para aplicativos especiais e usado apenas pelo [emparelhamento da Microsoft](mailto:peering@microsoft.com).
    * Selecione **SKU** como *básico gratuito*. Não selecione *Premium gratuitamente* , pois ele está reservado para aplicativos especiais.
    * Escolha o local do **Metro** para onde você deseja configurar o emparelhamento.

        > [!NOTE]
        > Se você já tiver conexões de emparelhamento com a Microsoft no local **Metro** selecionado e estiver usando o portal pela primeira vez para configurar o emparelhamento nesse local, as conexões de emparelhamento existentes serão listadas na seção **conexões de emparelhamento** , conforme mostrado abaixo. A Microsoft converterá automaticamente essas conexões de emparelhamento no recurso do Azure para que você possa gerenciá-las todos junto com as novas conexões, em um único lugar. Consulte [converter um emparelhamento direto herdado para o recurso do Azure usando o portal](../howto-legacy-direct-portal.md) para obter mais informações.
        >

1. Em **conexões de emparelhamento**, clique em **criar novo** para adicionar uma linha para cada nova conexão que você deseja configurar.

    * Para configurar/modificar as configurações de conexão, clique no botão Editar de uma linha.

        > [!div class="mx-imgBorder"]
        > Configuração de emparelhamento ![-edição direta](../media/setup-direct-conf-tab-edit.png)
    
    * Para excluir uma linha, clique no botão **...** > **excluir**.

        > [!div class="mx-imgBorder"]
        > Configuração de emparelhamento ![-edição direta](../media/setup-direct-conf-tab-delete.png)

    * Você deve fornecer todas as configurações para uma conexão, conforme mostrado abaixo.

         > [!div class="mx-imgBorder"]
         > Configuração de emparelhamento ![-conexão direta](../media/setup-direct-conf-tab-connection.png)

        1. Selecione o **recurso de emparelhamento** em que a conexão precisa ser configurada.
        1. O **provedor de endereços de sessão** é usado para determinar quem fornece a sub-rede necessária para configurar a sessão BGP entre sua rede e a Microsoft. Se você conseguir fornecer a sub-rede, escolha *par*. Caso contrário, escolha **Microsoft** e [emparelhamento da Microsoft](mailto:peering@microsoft.com) entrará em contato com você. Observe que a escolha desse processo levaria mais tempo para que a Microsoft processasse a solicitação de emparelhamento. Em alguns casos, a Microsoft pode não ser capaz de fornecer sub-redes que resultarão em negação de solicitação.
        1. Se você escolher **provedor de endereço de sessão** como *par*, insira o endereço IPv4 e IPv6 juntamente com a máscara de prefixo na sessão campos prefixo **IPv4** e **prefixo IPv6 da sessão** , respectivamente.
        1. Insira o número de prefixos IPv4 e IPv6 que serão anunciados nos campos **máximo de endereços IPv4 anunciados** e **máximo de endereços IPv6 anunciados** , respectivamente.
        1. Ajuste o controle deslizante de **largura de banda total** para refletir a largura de banda da conexão.
        1. Clique em **OK** para salvar as configurações de conexão.

1. Repita a etapa acima para adicionar mais conexões em qualquer instalação em que a Microsoft esteja colocalizada com sua rede, no **Metro** selecionado anteriormente.

1. Depois de adicionar todas as conexões necessárias, clique em **examinar + criar**.

    > [!div class="mx-imgBorder"]
    > Guia de configuração de emparelhamento ![final](../media/setup-direct-conf-tab-final.png)

1. Observe que o portal executa a validação básica das informações inseridas. Isso é exibido em uma faixa de forma na parte superior, como *executando a validação final...* .

    > [!div class="mx-imgBorder"]
    > Guia validação de emparelhamento ![](../media/setup-direct-review-tab-validation.png)

1. Depois que ele se transformar em *validação aprovada*, verifique suas informações e envie a solicitação clicando em **criar**. Se você precisar modificar sua solicitação, clique em **anterior** e repita as etapas acima.

    > [!div class="mx-imgBorder"]
    > ](../media/setup-direct-review-tab-submit.png) de envio de emparelhamento de ![

1. Depois de enviar a solicitação, aguarde até que ela conclua a implantação. Se a implantação falhar, entre em contato com [o emparelhamento da Microsoft](mailto:peering@microsoft.com). Uma implantação bem-sucedida será exibida como a seguir.

    > [!div class="mx-imgBorder"]
    > ![êxito de emparelhamento](../media/setup-direct-success.png)
