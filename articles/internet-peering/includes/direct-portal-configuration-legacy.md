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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678836"
---
1. Na página **'Criar um Peering',** no **separador Configuração,** preencha as caixas como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Criar um separador de configuração de página de peering](../media/setup-direct-conf-tab.png)

    * Para **o tipo de peering**, selecione **Direct**.
    * Para **a rede Microsoft**, selecione **AS8075**. Não selecione ASN 8069. É reservado para aplicações especiais e só é usado pelo [microsoft peering](mailto:peering@microsoft.com).
    * Selecione **SKU** como **Basic Free**. Não selecione Premium Free porque está reservado para aplicações especiais.
    * Selecione a localização **do Metrô** onde pretende converter olhando para um recurso Azure. Se tiver ligações com a Microsoft na localização selecionada do **Metrô** que não seja convertida num recurso Azure, estas ligações serão listadas na secção **de ligações peering,** como mostrado. Agora pode converter estas ligações de espreitar para um recurso Azure.

        > [!div class="mx-imgBorder"]
        > ![Lista de ligações de espreitar](../media/setup-directlegacy-conf-tab.png)

1. Se precisar de atualizar a largura de banda, selecione o botão de edição para uma linha para modificar as definições de ligação.

    > [!div class="mx-imgBorder"]
    > ![Botão Editar](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Se pretender adicionar ligações de observação adicionais com a Microsoft na localização selecionada do **Metrô,** selecione **Criar novos**. Para obter mais informações, consulte [Criar ou modificar um espreitamento direto utilizando o portal](../howto-direct-portal.md).
    >

1. Selecione **Rever + criar**. Note que o portal executa a validação básica da informação que introduziu. Uma fita no topo exibe a mensagem *A executar a validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de validação de pares](../media/setup-direct-review-tab-validation.png)

1. Após a alteração da mensagem para *validação passada,* verifique as suas informações. Submeta o pedido selecionando **Criar**. Para modificar o seu pedido, selecione **Previous** e repita os passos.

    > [!div class="mx-imgBorder"]
    > ![Submissão de pares](../media/setup-direct-review-tab-submit.png)

1. Depois de submeter o pedido, aguarde que a colocação termine. Se a implementação falhar, contacte [a Microsoft a espreitar](mailto:peering@microsoft.com). Uma implantação bem sucedida aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Olhando o sucesso](../media/setup-direct-success.png)
