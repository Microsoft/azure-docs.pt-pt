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
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775215"
---
1. Na página **Criar uma página De Espreitar,** sob o separador **Configuração,** preencha os campos como mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![Configuração de peering - Troca](../media/setup-exchange-conf-tab.png)

    * Para **o tipo de peering,** selecione *Exchange*.
    * Selecione **SKU** como *Basic Free*.
    * Escolha a localização do **Metro** para onde pretende converter o seu olhar para o recurso Azure. Se tiver ligações com a Microsoft na localização do **Metro** selecionada que não sejam convertidas para recurso Azure, então tais ligações serão listadas na secção de **ligações Peering,** como mostrado abaixo. Agora pode converter estas ligações de observação para o recurso Azure.

        > [!div class="mx-imgBorder"]
        > ![Configuração de peering - Troca - Conexões Legacy](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Não é possível modificar as definições para ligações de observação de legados. Se pretender adicionar ligações adicionais de pares com a Microsoft na localização do **Metro** selecionada, pode fazê-lo clicando em **Criar um novo** botão. Consulte [criar ou modificar um 'Exchange' utilizando o portal](../howto-exchange-portal.md) para obter mais informações.
        >

1. Clique em **Rever + criar**. Observe que o portal executa a validação básica da informação que inseriu. Isto é exibido numa fita no topo, como *validação final de execução...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de validação de observação](../media/setup-direct-review-tab-validation.png)

1. Depois de se recorrer a *Validação Passada,* verifique as suas informações e submeta o pedido clicando em **Criar**. Se precisar modificar o seu pedido, clique em **Anterior e** repita os passos acima.

    > [!div class="mx-imgBorder"]
    > ![Submeter-se](../media/setup-exchange-review-tab-submit.png)

1. Assim que submeter o pedido, aguarde que termine a implantação. Se a implementação falhar, contacte [o peering](mailto:peering@microsoft.com)da Microsoft . Uma implantação bem sucedida aparecerá como abaixo.

    > [!div class="mx-imgBorder"]
    > ![O sucesso do peering](../media/setup-direct-success.png)
