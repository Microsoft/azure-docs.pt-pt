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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774500"
---
1. Na página **criar um emparelhamento** , na guia **configuração** , preencha os campos, conforme mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > Configuração de emparelhamento de ![-](../media/setup-exchange-conf-tab.png) de troca

    * Para **tipo de emparelhamento**, selecione *Exchange*.
    * Selecione **SKU** como *básico gratuito*.
    * Escolha o local do **Metro** para onde você deseja configurar o emparelhamento.

        > [!NOTE]
        > Se você já tiver conexões de emparelhamento com a Microsoft no local **Metro** selecionado e estiver usando o portal pela primeira vez para configurar o emparelhamento nesse local, as conexões de emparelhamento existentes serão listadas na seção **conexões de emparelhamento** , conforme mostrado abaixo. A Microsoft converterá automaticamente essas conexões de emparelhamento no recurso do Azure para que você possa gerenciá-las todos junto com as novas conexões, em um único lugar. Consulte [converter um emparelhamento do Exchange herdado para o recurso do Azure usando o portal](../howto-legacy-exchange-portal.md) para obter mais informações.
        >

1. Em **conexões de emparelhamento**, clique em **criar novo** para adicionar uma linha para cada nova conexão que você deseja configurar.

    * Para configurar/modificar as configurações de conexão, clique no botão Editar de uma linha.

        > [!div class="mx-imgBorder"]
        > Configuração de emparelhamento de ![-](../media/setup-exchange-conf-tab-edit.png) de edição do Exchange

    * Para excluir uma linha, clique no botão **...** > **excluir**.

        > [!div class="mx-imgBorder"]
        > Configuração de emparelhamento de ![-](../media/setup-exchange-conf-tab-delete.png) de edição do Exchange

    * Você deve fornecer todas as configurações para uma conexão, conforme mostrado abaixo.

         > [!div class="mx-imgBorder"]
         > Configuração de emparelhamento ![-](../media/setup-exchange-conf-tab-connection.png) de conexão do Exchange

        1. Selecione o **recurso de emparelhamento** em que a conexão precisa ser configurada.
        1. Nos campos **endereço IPv4** e **endereço IPv6**, insira o endereço IPv4 e IPv6, respectivamente, que seria configurado nos roteadores da Microsoft usando o comando neighbor.
        1. Insira o número de prefixos IPv4 e IPv6 que serão anunciados nos campos **máximo de endereços IPv4 anunciados** e **máximo de endereços IPv6 anunciados** , respectivamente.
        1. Clique em **OK** para salvar as configurações de conexão.

1. Repita a etapa acima para adicionar mais conexões em qualquer instalação em que a Microsoft esteja colocalizada com sua rede, no **Metro** selecionado anteriormente.

1. Depois de adicionar todas as conexões necessárias, clique em **examinar + criar**.

    > [!div class="mx-imgBorder"]
    > Guia de configuração de emparelhamento ![final](../media/setup-exchange-conf-tab-final.png)

1. Observe que o portal executa a validação básica das informações inseridas. Isso é exibido em uma faixa de forma na parte superior, como *executando a validação final...* .

    > [!div class="mx-imgBorder"]
    > Guia validação de emparelhamento ![](../media/setup-direct-review-tab-validation.png)

1. Depois que ele se transformar em *validação aprovada*, verifique suas informações e envie a solicitação clicando em **criar**. Se você precisar modificar sua solicitação, clique em **anterior** e repita as etapas acima.

    > [!div class="mx-imgBorder"]
    > ](../media/setup-exchange-review-tab-submit.png) de envio de emparelhamento de ![

1. Depois de enviar a solicitação, aguarde até que ela conclua a implantação. Se a implantação falhar, entre em contato com [o emparelhamento da Microsoft](mailto:peering@microsoft.com). Uma implantação bem-sucedida será exibida como a seguir.

    > [!div class="mx-imgBorder"]
    > ![êxito de emparelhamento](../media/setup-direct-success.png)
