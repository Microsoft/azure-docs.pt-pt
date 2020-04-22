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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678836"
---
1. Na página **Criar uma página De Espreitar,** no separador **Configuração,** preencha as caixas como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Criar um separador de configuração de página de peering](../media/setup-direct-conf-tab.png)

    * Para **o tipo de peering,** selecione **Direct**.
    * Para **a rede Microsoft,** selecione **AS8075**. Não selecione ASN 8069. É reservado para aplicações especiais e apenas usado pelo peering da [Microsoft.](mailto:peering@microsoft.com)
    * Selecione **SKU** como **Basic Free**. Não selecione Premium Free porque está reservado para aplicações especiais.
    * Selecione a localização do **Metro** onde pretende converter o seu olhar para um recurso Azure. Se tiver ligações com a Microsoft na localização do **Metro** selecionada que não sejam convertidas para um recurso Azure, estas ligações serão listadas na secção de **ligações Peering,** como mostrado. Agora pode converter estas ligações de observação num recurso Azure.

        > [!div class="mx-imgBorder"]
        > ![Lista de ligações de pares](../media/setup-directlegacy-conf-tab.png)

1. Se necessitar de atualizar a largura de banda, selecione o botão de edição para uma linha para modificar as definições de ligação.

    > [!div class="mx-imgBorder"]
    > ![Botão Editar](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Se pretender adicionar ligações adicionais de peering com a Microsoft na localização selecionada do **Metro,** selecione **Criar novas**. Para mais informações, consulte [Criar ou modificar um 'peering' direto utilizando o portal](../howto-direct-portal.md).
    >

1. Selecione **Rever + criar**. Note que o portal executa a validação básica da informação que inseriu. Uma fita na parte superior exibe a mensagem *Execução de validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de validação de observação](../media/setup-direct-review-tab-validation.png)

1. Depois de a mensagem ter *sido aprovada,* verifique as suas informações. Envie o pedido selecionando **Criar**. Para modificar o seu pedido, selecione **Anterior** e repita os passos.

    > [!div class="mx-imgBorder"]
    > ![Submissão de pares](../media/setup-direct-review-tab-submit.png)

1. Depois de submeter o pedido, aguarde que a colocação termine. Se a implementação falhar, contacte [o peering](mailto:peering@microsoft.com)da Microsoft . Um destacamento bem sucedido aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Olhando para o sucesso](../media/setup-direct-success.png)
