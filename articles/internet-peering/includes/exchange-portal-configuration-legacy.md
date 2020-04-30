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
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678584"
---
1. Na página **Criar uma página De Espreitar,** no separador **Configuração,** preencha as caixas como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Criar uma página de peering Tipo de peering](../media/setup-exchange-conf-tab.png)

    * Para **o tipo de peering,** selecione **Exchange**.
    * Selecione **SKU** como **Basic Free**.
    * Selecione a localização do **Metro** onde pretende converter o seu olhar para um recurso Azure. Se tiver ligações com a Microsoft na localização do **Metro** selecionada que não sejam convertidas para um recurso Azure, estas ligações serão listadas na secção de **ligações Peering,** como mostrado. Agora pode converter estas ligações de observação num recurso Azure.

        > [!div class="mx-imgBorder"]
        > ![Lista de ligações de pares](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Não é possível modificar as definições para ligações de observação de legados. Se pretender adicionar ligações adicionais de peering com a Microsoft na localização selecionada do **Metro,** selecione **Criar novas**. Para mais informações, consulte [Criar ou modificar um 'Exchange peering', utilizando o portal](../howto-exchange-portal.md).
        >

1. Selecione **Rever + criar**. Note que o portal executa a validação básica da informação que inseriu. Uma fita na parte superior exibe a mensagem *Execução de validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de validação de observação](../media/setup-direct-review-tab-validation.png)

1. Depois de a mensagem ter *sido aprovada,* verifique as suas informações. Envie o pedido selecionando **Criar**. Se precisar modificar o seu pedido, selecione **Anterior** e repita os passos.

    > [!div class="mx-imgBorder"]
    > ![Submissão de pares](../media/setup-exchange-review-tab-submit.png)

1. Depois de submeter o pedido, aguarde que a colocação termine. Se a implementação falhar, contacte [o peering](mailto:peering@microsoft.com)da Microsoft . Uma implantação bem sucedida aparece como mostrado.

    > [!div class="mx-imgBorder"]
    > ![Olhando para o sucesso](../media/setup-direct-success.png)
